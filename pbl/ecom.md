## Scenario: E-Commerce Flash Sale Platform

### Business Context
You're the lead architect for "ShopFast," an e-commerce platform experiencing rapid growth. The company is planning their biggest flash sale event with expected traffic of 100,000 concurrent users purchasing limited-quantity items (only 50 units of each product available).

---

## Detailed Functionality

### Current System Architecture

#### 1. Frontend Application
- React-based single-page application
- Hosted on a web server cluster (5 instances)
- Directly communicates with backend APIs

#### 2. Backend Services
- **Product Service**: Manages product catalog and inventory
- **Order Service**: Handles order creation and processing
- **Payment Service**: Processes payments via third-party gateway
- **User Service**: Manages authentication and user profiles
- **Notification Service**: Sends email confirmations

#### 3. Database Layer
- Single relational database (PostgreSQL)
- All services connect directly to this database
- Current schema includes: Users, Products, Orders, Inventory, Payments

#### 4. Current Flow (Order Placement)
```
User clicks "Buy Now"
→ Frontend sends request to Order Service
→ Order Service checks inventory in database
→ If available, creates order record
→ Calls Payment Service (synchronous)
→ Payment Service processes payment
→ Order Service updates inventory
→ Calls Notification Service (synchronous)
→ Returns success/failure to user
```

---

## Technical Bottleneck (30-minute analysis challenge)

### The Problem

During the last flash sale simulation with only 10,000 concurrent users, the following issues occurred:

#### Issue 1: Race Conditions in Inventory
- Multiple users successfully purchased items even after stock reached zero
- 127 orders were placed for a product with only 50 units
- Overselling resulted in customer complaints and refunds

#### Issue 2: Database Connection Exhaustion
- Database connection pool (max 100 connections) was exhausted within 2 minutes
- Response times degraded from 200ms to 45+ seconds
- 67% of requests resulted in timeout errors
- Database CPU utilization reached 98%

#### Issue 3: Cascading Failures
- When Payment Service experienced 3-second delays (third-party API slowdown)
- Order Service threads were blocked waiting for payment responses
- This blocked new order requests from being processed
- Eventually, all backend services became unresponsive

#### Issue 4: Inefficient Data Access
Every order request required 8 separate database queries:
- Check user authentication
- Fetch product details
- Check inventory count
- Lock inventory row
- Create order record
- Update inventory
- Create payment record
- Fetch user email for notification

#### Issue 5: Notification Bottleneck
- Sending confirmation emails (synchronously) added 800ms to each request
- When email service had issues, orders couldn't complete
- No retry mechanism for failed email deliveries

