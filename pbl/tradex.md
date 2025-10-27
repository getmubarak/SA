## Scenario: High-Frequency Stock Trading Platform - "TradeX"

### Business Context
You're the chief architect for "TradeX," a stock trading platform serving retail and institutional investors. The platform handles 50 million trades per day, supports 10 million active users, integrates with 15 stock exchanges globally, and offers real-time market data, algorithmic trading, portfolio management, and social trading features. The platform must comply with financial regulations (SEC, MiFID II, etc.) requiring complete audit trails and preventing market manipulation.

---

## Detailed Functionality

### Current System Architecture

#### 1. Client Applications
- Web trading terminal (React-based)
- Mobile apps (iOS/Android)
- Desktop trading application (Electron)
- API for algorithmic traders
- All clients connect via REST APIs and WebSocket for market data

#### 2. Backend Services
- **Order Management Service (OMS)**: Handles buy/sell orders
- **Market Data Service**: Streams real-time price feeds from exchanges
- **Portfolio Service**: Tracks user holdings and positions
- **Risk Management Service**: Pre-trade risk checks and position limits
- **Settlement Service**: Post-trade clearing and settlement
- **Account Service**: User accounts, KYC, and balances
- **Analytics Service**: Technical indicators, charts, backtesting
- **Notification Service**: Price alerts, order fills, margin calls
- **Reporting Service**: Trade confirmations, tax documents, statements
- **Compliance Service**: Regulatory reporting and audit logs

#### 3. Infrastructure
- Primary datacenter co-located with NYSE (New York)
- Secondary datacenter in London
- **PostgreSQL** for transactional data (orders, accounts, positions)
- **TimescaleDB** for market data time-series
- **Redis** for session management and rate limiting
- **Kafka** for order flow and event streaming
- **Elasticsearch** for audit log search
- Direct market access (DMA) connections to exchanges via FIX protocol

#### 4. Current Flows

**Order Placement Flow:**
```
1. User places market order for 100 shares of AAPL
2. Client app sends order to Order Management Service
3. OMS validates order (stock symbol, quantity, order type)
4. OMS calls Account Service to check buying power (synchronous)
5. Account Service queries PostgreSQL for account balance
6. Account Service calculates available buying power
7. OMS calls Risk Management Service (synchronous)
8. Risk Service checks:
   - Position limits (max shares per stock)
   - Concentration limits (max % of portfolio)
   - Day trading limits (pattern day trader rules)
   - Margin requirements
9. Risk Service queries Portfolio Service for current positions
10. Portfolio Service queries PostgreSQL (5 tables joined)
11. If all checks pass, OMS creates order record in database
12. OMS sends order to exchange via FIX gateway
13. Exchange acknowledges order
14. OMS updates order status to "PENDING"
15. User sees "Order Submitted" confirmation
```

**Order Fill Flow:**
```
1. Exchange sends fill notification via FIX protocol
2. FIX Gateway receives message
3. Gateway writes to Kafka topic "order-fills"
4. OMS consumes from Kafka
5. OMS updates order status to "FILLED"
6. OMS calls Portfolio Service to update positions (synchronous)
7. Portfolio Service updates PostgreSQL (3 tables: positions, transactions, history)
8. Portfolio Service recalculates portfolio value
9. OMS calls Account Service to update cash balance (synchronous)
10. Account Service updates PostgreSQL
11. OMS calls Notification Service (synchronous)
12. Notification Service sends push notification to user
13. OMS publishes event to Kafka "order-completed" topic
14. Settlement Service consumes event for T+2 settlement
```

**Market Data Flow:**
```
1. Exchange publishes market data feed (100,000 quotes/second)
2. Market Data Service receives feed
3. For each quote:
   - Parse and validate
   - Write to TimescaleDB (every single quote)
   - Calculate technical indicators (VWAP, RSI, etc.)
   - Check if any user has price alert for this stock
   - Query PostgreSQL for users with alerts (1 query per stock)
   - Publish to WebSocket for connected clients
4. Web clients receive real-time updates
```

**Portfolio Valuation Flow:**
```
1. User opens portfolio page
2. Client requests portfolio from Portfolio Service
3. Portfolio Service queries PostgreSQL for all positions (50-500 positions per user)
4. For each position:
   - Query Market Data Service for current price (synchronous REST call)
   - Calculate current value (quantity × price)
   - Calculate gain/loss
   - Calculate day change
5. Sum up total portfolio value
6. Return to client
7. Client auto-refreshes every 5 seconds (repeats entire process)
```

---

## Technical Bottleneck (30-minute analysis challenge)

### The Problem

The platform is experiencing critical failures that threaten business operations and regulatory compliance:

#### Issue 1: Order Latency Explosion
- Order placement takes 800ms-2000ms during market hours
- Pre-trade risk checks account for 60% of latency
- Each risk check queries 5 different services synchronously
- Risk Management Service queries entire order history (millions of records)
- During market open (9:30 AM ET), latency spikes to 5-8 seconds
- 12% of orders timeout before reaching exchange
- Users see "Order Submitted" but order already rejected
- Institutional clients missing price targets due to latency
- Lost $50M in trading volume last quarter due to slow executions

#### Issue 2: Market Data Bottleneck
- Market Data Service writes 100,000 quotes/second to TimescaleDB
- Database write throughput maxed at 80,000 writes/second
- Backpressure causes 20% of market data to be dropped
- During high volatility (earnings, Fed announcements), system drops 40% of quotes
- Users see stale prices (30-90 seconds old)
- Price alerts trigger late or not at all
- WebSocket connections drop when service overwhelms
- TimescaleDB storage growing 500GB per day (4TB total)
- Historical data queries take 30+ seconds

#### Issue 3: Portfolio Valuation Meltdown
- Portfolio page takes 15-30 seconds to load for active traders
- Each portfolio refresh makes 50-500 API calls (one per position)
- Market Data Service overwhelmed by portfolio valuation requests
- 80% of API traffic is portfolio price lookups
- Same price data fetched thousands of times per second
- During market hours, Portfolio Service crashes 2-3 times/hour
- Users spam refresh, amplifying the problem
- Mobile app battery drain from constant refreshes

#### Issue 4: Database Deadlocks and Contention
- 5,000-8,000 deadlocks per hour during peak trading
- Multiple services update same account/position records simultaneously:
  - OMS updating order status
  - Portfolio Service updating positions
  - Account Service updating cash balance
  - Risk Service checking limits
  - Settlement Service updating settled positions
- Order fills delayed by database locks (30-60 seconds)
- Users see incorrect portfolio values due to stale reads
- Some orders "lost" - filled at exchange but not reflected in user account
- Manual reconciliation required (20-30 cases per day)

#### Issue 5: Settlement Service Failures
- Settlement runs as batch job at end of day (6 PM ET)
- Processes all day's trades in single transaction
- Takes 4-6 hours to complete
- If job fails (happens 2-3 times/week), must restart from beginning
- No incremental processing
- Users can't trade after hours if settlement hasn't completed
- Cash balances incorrect until settlement finishes
- Regulatory requirement: settle by T+2 (2 days after trade)
- Currently failing 15% of T+2 deadlines

#### Issue 6: Risk Management Race Conditions
- Risk checks performed on stale data
- User has $10,000 buying power
- User places 5 simultaneous orders, each for $10,000
- All 5 orders pass risk check (querying same stale balance)
- All 5 orders submitted to exchange
- Account now overdrawn by $40,000
- Margin call issued automatically
- Happens 50-100 times per day
- Regulatory violation (Regulation T)

#### Issue 7: Audit Trail Incompleteness
- Order lifecycle events spread across multiple services
- No unified view of order journey
- When regulators request audit trail, requires manual assembly
- Takes 2-3 days to produce complete audit report
- 15% of orders missing some lifecycle events
- Cannot prove sequence of events for disputed trades
- Kafka topics have 7-day retention (regulatory requirement: 7 years)
- No point-in-time recovery for trade data
- Failed SEC audit last year due to incomplete records

#### Issue 8: Market Data Subscription Chaos
- 10 million users each subscribe to market data
- Average user watches 20 stocks
- 200 million active subscriptions
- No subscription aggregation - each user gets dedicated WebSocket connection
- WebSocket server managing 10 million concurrent connections
- Each connection consumes 50KB memory (500GB total)
- When popular stocks move (TSLA, AAPL), millions of users get updates
- Fan-out causes network saturation
- During GameStop event (high volatility), system completely failed
- No way to throttle or prioritize data delivery

#### Issue 9: Cross-Exchange Routing Inefficiency
- User order might execute on 15 different exchanges
- OMS sends order to all exchanges simultaneously (smart order routing)
- Each exchange has different latency (2ms - 50ms)
- First exchange to fill wins
- Must cancel order at other 14 exchanges
- Cancel messages don't always arrive in time
- Partial fills across multiple exchanges common
- Some orders over-filled (bought 200 shares instead of 100)
- Cost of over-fills: $2M per month
- No coordination between exchange connections

#### Issue 10: Algorithmic Trading Bottlenecks
- Algo traders send 1,000+ orders per second
- Each order goes through full REST API stack
- API rate limiting per user (100 requests/second)
- High-frequency traders need 10,000+ orders/second
- Losing business to competitors with faster APIs
- Algo trading revenue down 40% year-over-year
- REST overhead adds 5-15ms per order
- Market makers demanding <1ms order latency
- Current architecture cannot support low-latency requirements

#### Issue 11: Real-Time P&L Calculation Failure
- Profit & Loss (P&L) calculated by summing all positions
- Requires joining 7 database tables
- Query takes 5-10 seconds for active traders (1,000+ positions)
- P&L shown on UI is 30-60 seconds stale
- During high volatility, users can't see real-time gains/losses
- Margin calls issued based on stale P&L data
- Users forced to close positions unnecessarily
- Complaints from day traders about "blind trading"

#### Issue 12: Disaster Recovery Gap
- Primary datacenter has 15ms latency to exchanges
- Secondary datacenter (London) has 80ms latency to US exchanges
- Cannot failover without unacceptable latency increase
- No active-active architecture
- DR site only has daily backups
- RTO (Recovery Time Objective): 4 hours
- RPO (Recovery Point Objective): 24 hours (unacceptable for trading)
- If NYC datacenter fails, lose entire day's trading data
- Regulatory requirement: RPO < 1 hour


7. **99.999
