# Serverless Computing and Event-Driven Architectures - Session 12

> BITS Pilani — **CSI ZG527 / SE ZG527 / SS ZG527: Cloud Computing**

---

## Table of Contents

1. [Serverless Computing Fundamentals](#serverless-computing-fundamentals)
2. [Function-as-a-Service (FaaS)](#function-as-a-service-faas)
3. [Event-Driven Architecture](#event-driven-architecture)
4. [Microservices with Serverless](#microservices-with-serverless)
5. [Serverless Data Processing](#serverless-data-processing)
6. [Cost Optimization and Operations](#cost-optimization-and-operations)

---

## Serverless Computing Fundamentals

### What is Serverless?

**Misconception:** "No servers exist"

**Reality:** Servers exist but you don't manage them
- Cloud provider handles: provisioning, scaling, patching, networking
- You provide: code and configuration
- You pay: for execution time, not allocated capacity

### Evolution: Monolith → Containers → Serverless

**Monolithic Architecture:**
```
Application
├─ Web Server
├─ Business Logic
├─ Database Connection
└─ File Processing

Deployment: One VM/instance
Scaling: Scale entire application
Cost: Pay whether processing requests or idle
```

**Container Architecture (Kubernetes):**
```
Container 1 (Web)      ← Manage scaling
Container 2 (API)      ← Manage networking
Container 3 (Worker)   ← Manage deployment
Container 4 (Cache)    ← Manage updates
...

Benefits: Granular scaling
Cost: Pay for all containers always running
```

**Serverless Architecture:**
```
Lambda Function (Web Handler)
Lambda Function (API Handler)
Lambda Function (Async Worker)
Lambda Function (Email Sender)

Benefits: 
- Scale each independently
- Pay per millisecond executed
- Zero infrastructure management
```

### Serverless Benefits

**Developer Velocity:**
- No infrastructure to manage
- Deploy function in seconds
- Instant scaling
- Reduced operational burden

**Cost:**
```
Traditional VM (always on):
- $0.10/hour = $730/month
- Regardless of load

Lambda (pay-per-use):
- 1 million requests/month
- 1 GB RAM, 100ms average
- Cost: $0.20/month
- 99.73% cheaper!
```

**Automatic Scaling:**
```
Traditional: Black Friday spike
├─ Predict load
├─ Provision capacity
├─ Deploy changes
├─ Wait for warm-up
└─ 30+ minutes

Serverless:
├─ Spike detected
├─ Automatically invoke more functions
└─ 100ms (sub-second)
```

### Serverless Limitations

**Cold Starts:**
```
First invocation: 300-5000ms (function initialization)
Subsequent: 1-10ms (warm)

For latency-sensitive applications (online gaming), problematic
Mitigation: Reserved concurrency, Provisioned Concurrency
```

**Execution Timeout:**
```
Maximum execution time: 15 minutes (AWS Lambda)
Limitation: Long-running processes impossible
Solution: Async processing with queues
```

**State Management:**
```
Stateless by default: Each invocation independent
Limitation: Cannot store data in memory between invocations
Solution: Use external database or cache

Example Problem:
Function invocation 1: Session object = {"user": "john"}
Function invocation 2: Session object = ??? (lost)
```

---

## Function-as-a-Service (FaaS)

### AWS Lambda

**Function Structure:**
```python
def lambda_handler(event, context):
    """
    event: Input from trigger (API request, S3 event, etc.)
    context: Runtime information (request ID, timeout, etc.)
    """
    
    name = event['queryStringParameters']['name']
    return {
        'statusCode': 200,
        'body': f'Hello, {name}!'
    }
```

**Supported Runtimes:**
- Python 3.11
- Node.js 18, 20
- Java 17, 21
- Go 1.x
- .NET 6, 8
- Ruby 3.2
- Custom runtimes (C++, Rust)

### Lambda Execution Model

**Request Flow:**
```
1. API Gateway receives request
2. Invokes Lambda function
3. Lambda runtime starts (cold start if needed)
4. Executes handler code
5. Returns response to API Gateway
6. API Gateway returns to client
7. Function environment remains warm (10-15 minutes)
```

**Concurrency:**
```
Default: 1000 concurrent executions

Request 1: Invoke Lambda
Request 2: Invoke Lambda (concurrent if available)
Request 3: Invoke Lambda
...
Request 1001: Throttled (wait in queue)

Reserved Concurrency:
- Allocate N concurrent executions to specific function
- Guarantees availability
- Unused capacity unused (cost incurred)

Provisioned Concurrency:
- Keep N functions warm and ready
- Eliminates cold starts
- Higher cost than reserved
```

### Lambda Pricing

**Compute:**
```
$0.0000002 per millisecond per MB allocated

Example:
- 1000 GB-seconds/month (1 GB × 1000 seconds)
- Cost: $0.0000002 × 1000 × 1000 = $0.20

Tier 1 (free): 1 million requests + 400,000 GB-seconds
```

**Comparison:**
```
EC2 t3.micro: $0.0116/hour ≈ $8.47/month
Lambda: $0.20/month (for equivalent workload)
Savings: 97%+ for bursty workloads
```

---

## Event-Driven Architecture

### Event Concept

**Event:** Something interesting happened

```
Examples:
- User uploaded file (S3: PutObject)
- Record inserted in database (DynamoDB Streams)
- Message published (SNS Topic)
- File received from partner (SQS Queue)
- API request (API Gateway)
- Schedule triggered (CloudWatch Events)
```

### Event-Driven Patterns

**Push Model (Synchronous):**
```
API Request
    │
    ▼
Lambda Function
    │
    ├─ Read from DynamoDB
    ├─ Validate input
    ├─ Process
    └─ Return response (200ms)

Result: Client waits for response
Latency: Critical
Scaling: Limited by processing time
```

**Pull Model (Asynchronous):**
```
User uploads file → S3 Trigger
                      │
                      ▼
                  SQS Queue
                      │
              ┌───────┼───────┐
              │       │       │
              ▼       ▼       ▼
          Lambda   Lambda   Lambda
        Worker 1  Worker 2  Worker 3
              │       │       │
              └───────┼───────┘
                      │
                      ▼
                  Result Database

Result: Client returns immediately
        Background workers process
Latency: Not critical
Scaling: Can add many workers
```

### Event Sources

**AWS Event Sources:**

**S3:**
```
Configuration:
├─ Bucket: my-uploads
├─ Event: ObjectCreated:*
└─ Destination: Lambda function

Trigger: File uploaded
Event: {
    "Records": [{
        "s3": {
            "bucket": {"name": "my-uploads"},
            "object": {"key": "image.jpg"}
        }
    }]
}
```

**DynamoDB Streams:**
```
Configuration:
├─ Table: Orders
├─ Stream: New and old image
└─ Destination: Lambda

Trigger: Record modified
Event includes:
├─ Old value
├─ New value
├─ Operation type (INSERT, UPDATE, DELETE)
```

**SQS (Simple Queue Service):**
```
Producer sends message:
└─ {"action": "send-email", "to": "user@example.com"}

Lambda polls SQS:
├─ Retrieve message
├─ Process
├─ Delete from queue

Result: Decoupled processing
```

**SNS (Simple Notification Service):**
```
Publisher sends event:
└─ Order Completed

Subscribers receive:
├─ Lambda function 1 (send confirmation email)
├─ Lambda function 2 (update inventory)
└─ Lambda function 3 (create shipment)

Result: One event, multiple handlers
```

---

## Microservices with Serverless

### Service Decomposition

**Monolithic E-Commerce:**
```
Monolith
├─ User Management
├─ Product Catalog
├─ Shopping Cart
├─ Payment Processing
├─ Order Management
├─ Inventory
└─ Shipping

Issues:
- Single point of failure
- Cannot scale independently
- Deployment requires full testing
- Language locked (all same tech)
```

**Serverless Microservices:**
```
API Gateway
    │
    ├─ POST /users → Lambda (Node.js) - User Service
    ├─ GET /products → Lambda (Python) - Catalog Service
    ├─ POST /orders → Lambda (Java) - Order Service
    │   └─ Triggers Order Processing Step Function
    ├─ POST /payment → Lambda (Python) - Payment Service
    └─ GET /shipment/{id} → Lambda (Go) - Shipping Service

Each:
├─ Independent codebase
├─ Own database
├─ Separate version control
├─ Can be deployed independently
└─ Scales independently
```

### Inter-Service Communication

**Synchronous (Request-Response):**
```
Order Service
    │
    ├─ Call Inventory Service (HTTP)
    │   └─ Check stock available
    ├─ Call Payment Service (HTTP)
    │   └─ Process payment
    └─ Return order confirmation

Issue: Chain fails if one service slow/unavailable
Timeout: 15 min limit for Lambda
```

**Asynchronous (Event-Driven):**
```
Order Service
    │
    └─ Publish "Order Created" event
         │
         ├─ Inventory Service subscribes
         │   └─ Deducts stock
         ├─ Payment Service subscribes
         │   └─ Processes payment
         └─ Notification Service subscribes
             └─ Sends confirmation email

Benefit: Services operate independently
Issue: Eventual consistency (stock deducted after slight delay)
```

---

## Serverless Data Processing

### Data Pipeline

**Scenario: Image Processing**

```
1. User uploads image → S3 Bucket
2. S3 Trigger → Lambda Function
3. Lambda:
   ├─ Download image from S3
   ├─ Resize (thumbnail, 300x300)
   ├─ Apply watermark
   ├─ Compress (reduce file size)
   └─ Upload to output bucket
4. Upload completion → SNS notification
5. Web server subscribes → Update database

Result:
- Fully automated
- Scales to millions of images
- Cost: Pay only for processing time
```

### Step Functions (Workflow Orchestration)

**Complex Process:** Order Processing with Approvals

```
Step Function Definition:
1. Start
2. Validate Order (Lambda)
   ├─ If invalid → Fail
   ├─ If valid → Continue
3. Check Inventory (Lambda)
   ├─ If out of stock → Notify (SNS)
   ├─ If in stock → Continue
4. Process Payment (Lambda)
   ├─ If declined → Refund (Lambda)
   ├─ If approved → Continue
5. Create Shipment (Lambda)
6. Send Confirmation (SNS)
7. End

Visual Workflow:
start → validate → inventory → payment → shipment → email → end
         │          │            │
         └──────────┴────────────┘ (error paths)

Benefits:
- Complex logic expressed as state machine
- Automatic retries on failure
- Wait states (pause processing)
- Conditional branching
- Parallel processing
```

---

## Cost Optimization and Operations

### Cost Analysis

**Scenario: E-commerce with 100M API requests/month**

```
Traditional EC2:
├─ 2 m5.xlarge instances: $600/month
├─ 1 RDS PostgreSQL: $400/month
├─ ALB: $20/month
├─ Data transfer: $100/month
└─ Total: $1,120/month

Serverless:
├─ Lambda: 100M × 100ms × 1GB = 100M GB-ms = 416,666 GB-seconds
│   Cost: $0.0000002 × 416,666 = $0.083/month (free tier covers)
├─ API Gateway: 100M × $0.0000035 = $350/month
├─ DynamoDB: 10M writes + 20M reads = $1,000/month
└─ Total: ~$1,350/month

Result: Serverless 20% more expensive at scale
Benefit: Easier to operate, automatic scaling
```

**When Serverless Wins:**
```
✓ Bursty traffic (social media campaigns)
✓ Varies by time of day (business hours vs night)
✓ Multiple use cases (video processing for some, API for others)
✗ Consistent baseline load (20K requests/sec always)
```

### Monitoring and Observability

**CloudWatch Logs:**
```python
import logging
logger = logging.getLogger()

def handler(event, context):
    logger.info(f"Received event: {event}")
    logger.error(f"Processing failed: {error}")
    return response
```

**CloudWatch Metrics:**
```
Lambda automatically tracks:
├─ Duration (execution time)
├─ Errors (exceptions thrown)
├─ Throttles (concurrency exceeded)
├─ Invocations (number of calls)
└─ ConcurrentExecutions (parallel running)
```

**X-Ray Tracing:**
```
Request flow visualization:
API Gateway (10ms)
  └─ Lambda 1 (100ms)
      └─ DynamoDB Query (50ms)
      └─ SNS Publish (5ms)

Identify bottlenecks and optimize
```

---

## Summary

**Serverless Benefits:**
- Zero infrastructure management
- Sub-second auto-scaling
- Pay-per-use pricing
- Event-driven programming
- Microservices friendly

**Serverless Challenges:**
- Cold starts (mitigable)
- Execution timeout (15 min limit)
- State management (use external storage)
- Debugging complexity
- Vendor lock-in (AWS-specific APIs)

**Best Use Cases:**
- APIs and webhooks
- Event processing (file uploads, database changes)
- Scheduled tasks (batch processing)
- Real-time processing (streams)
- Microservices architecture

