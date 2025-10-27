# Cloud Design Patterns: Problem-Based Learning

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

---

## Student Task (30-minute bottleneck analysis)

Students must:

1. **Identify the root causes** of each issue
2. **Map the bottlenecks** to specific architectural anti-patterns:
   - Where is tight coupling causing problems?
   - Where are single points of failure?
   - What operations are blocking unnecessarily?
   - Where is data consistency at risk?
   - What resources are being overutilized?
3. **Prioritize the issues** by impact and provide quantitative reasoning

---

## Architecture Proposal Task

After the bottleneck analysis, students must design a new architecture that:

### Requirements

1. **Handle 100,000 concurrent users** during flash sales
2. **Guarantee inventory accuracy** - no overselling
3. **Achieve 99.9% availability** - failures in one component shouldn't cascade
4. **Maintain sub-second response times** for order placement (under load)
5. **Process at least 5,000 orders per second** at peak
6. **Ensure eventual consistency** where appropriate, but strong consistency for inventory
7. **Enable horizontal scaling** of all components
8. **Provide graceful degradation** when external services fail

### Deliverables

Students must provide:

#### 1. Architecture Diagram
Showing:
- All components and their interactions
- Data flow for order placement
- Synchronous vs asynchronous boundaries
- State management approach

#### 2. Pattern Justification Document
Explaining:
- Which cloud design patterns they're applying
- Why each pattern solves specific bottlenecks
- Trade-offs of their approach
- How their design meets each requirement

#### 3. Data Consistency Strategy
- How they handle inventory deduction
- How they prevent race conditions
- Transaction boundaries
- Compensation strategies for failures

#### 4. Scalability Analysis
- Which components can scale horizontally
- What resources need vertical scaling
- Cost implications of their design

#### 5. Failure Scenarios
- What happens when payment service is down?
- What happens when database has issues?
- What happens when a service instance crashes?

---

## Evaluation Criteria

Students will be evaluated on:

- **Correctness**: Does the solution actually solve the bottlenecks?
- **Pattern Application**: Appropriate use of cloud design patterns
- **Trade-off Analysis**: Understanding of CAP theorem, consistency models
- **Scalability**: Can the design handle 10x growth?
- **Resilience**: Fault tolerance and recovery mechanisms
- **Practicality**: Is this implementable? What's the migration path?

---

## Potential Patterns to Consider

Solutions might incorporate patterns such as:
- Queue-Based Load Leveling
- Retry Pattern
- Circuit Breaker
- CQRS (Command Query Responsibility Segregation)
- Event Sourcing
- Cache-Aside
- Competing Consumers
- Materialized View
- Compensating Transaction
- Bulkhead
- Throttling

**Note**: Students should discover and justify which patterns are most appropriate for solving the specific bottlenecks identified in their analysis.

---

## Key Learning Objectives

This problem requires students to think holistically about:
- Distributed systems design
- Understanding the nuances of consistency vs. availability
- Making informed architectural decisions under constraints
- Trade-offs between different design patterns
- Real-world scalability challenges in e-commerce systems
