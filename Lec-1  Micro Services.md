# Microservices Architecture - Detailed Notes

## What are Microservices?

**Microservices** is an architectural style where an application is built as a collection of small, independent services that work together, rather than one large monolithic application.

### Key Characteristics

**Independent services**: Each microservice handles a specific business function (e.g., user authentication, payment processing, inventory management)

**Separate deployment**: Each service can be deployed, updated, and scaled independently

**Own database**: Services typically have their own database (not shared)

**Communication**: Services communicate via APIs (usually HTTP/REST, gRPC, or message queues)

**Technology diversity**: Each service can use different programming languages, frameworks, or databases

### Example: E-commerce Application

**Monolithic e-commerce app**: One large codebase with user management, products, orders, payments all together

**Microservices e-commerce app**:
- User Service - handles login, profiles
- Product Service - manages product catalog
- Order Service - processes orders
- Payment Service - handles transactions
- Notification Service - sends emails/SMS

Each runs independently and communicates through APIs.

### Advantages

**Scalability**: Scale only the services that need it

**Resilience**: One service failing doesn't crash the entire app

**Faster development**: Teams can work on different services simultaneously

**Technology flexibility**: Use the best tool for each job

**Easier updates**: Deploy changes to one service without touching others

### Disadvantages

**Complexity**: More moving parts to manage

**Network overhead**: Services communicate over network (slower than function calls)

**Distributed system challenges**: Debugging, monitoring, and testing are harder

**Data consistency**: Managing transactions across services is complex

### When to Use

- Large, complex applications
- Teams working on different features
- Need for independent scaling
- Long-term projects with evolving requirements

**Note**: For small projects, a monolith is usually simpler and better!

---

## Core Components of Microservices Architecture

### API Gateway

**Definition**: A server that acts as a single entry point for all client requests to your microservices. Think of it as a "front door" or "receptionist" for your backend services.

#### Architecture Comparison

**Without API Gateway**:
```
Mobile App ──→ User Service (port 8001)
          ──→ Order Service (port 8002)
          ──→ Payment Service (port 8003)
          ──→ Product Service (port 8004)
```
- Client needs to know all service addresses and ports
- Complex client-side logic

**With API Gateway**:
```
Mobile App ──→ API Gateway (port 80) ──→ User Service
                    ↓                 ──→ Order Service
                    ↓                 ──→ Payment Service
                    ↓                 ──→ Product Service
```
- Client talks to single endpoint
- Gateway handles routing

#### Core Functions

**1. Routing/Request Forwarding**

Routes requests to appropriate backend services based on URL path:
```
GET /api/users/123    → Routes to User Service
GET /api/orders/456   → Routes to Order Service
GET /api/products/789 → Routes to Product Service
```

**2. Authentication & Authorization**
- Validates API keys, JWT tokens
- Checks user permissions
- Centralizes security logic
- Backend services don't need individual auth

**3. Rate Limiting**
- Limits requests per user (e.g., 100 requests/hour)
- Prevents abuse and DDoS attacks
- Protects backend services from overload

**4. Load Balancing**

Distributes traffic across multiple service instances:
```
Request → Gateway → User Service Instance 1
                 → User Service Instance 2
                 → User Service Instance 3
```

**5. Request/Response Transformation**
- Converts data formats (XML ↔ JSON)
- Aggregates responses from multiple services
- Adds/removes headers
- Protocol translation

**6. Caching**
- Stores frequent responses
- Reduces backend load
- Faster response times
- TTL-based cache invalidation

**7. Logging & Monitoring**
- Tracks all requests in centralized location
- Monitors performance metrics
- Error tracking and debugging
- Analytics and insights

**8. SSL/TLS Termination**
- Handles HTTPS encryption at gateway
- Backend services can use plain HTTP internally
- Simplifies certificate management

#### Real-World Example: E-commerce Order Details

**Without Gateway**:
1. Client calls User Service → Get user info
2. Client calls Order Service → Get order details
3. Client calls Product Service → Get product info
4. Client calls Payment Service → Get payment status
5. Client combines all data

**With Gateway**:
1. Client makes single call: `GET /api/orders/123`
2. Gateway:
    - Authenticates request
    - Calls User Service
    - Calls Order Service
    - Calls Product Service
    - Calls Payment Service
    - Combines responses
    - Returns unified response to client

#### Popular API Gateway Tools

| Tool | Type | Best For |
|------|------|----------|
| Kong | Open source | Feature-rich, extensible |
| AWS API Gateway | Cloud | AWS ecosystem |
| NGINX | Open source | High performance, lightweight |
| Traefik | Open source | Docker, Kubernetes |
| Spring Cloud Gateway | Open source | Java/Spring applications |
| Express Gateway | Open source | Node.js applications |
| Azure API Management | Cloud | Azure ecosystem |
| Apigee | Enterprise | Large-scale enterprise |

#### Benefits

**Simplified client code**: Single endpoint instead of many

**Centralized security**: Authentication and authorization in one place

**Reduced latency**: Response aggregation and caching

**Flexibility**: Change backend without affecting clients

**Monitoring**: Single point for tracking all traffic

**Cross-cutting concerns**: Handle logging, security, etc. once

#### Drawbacks

**Single point of failure**: Gateway down = everything down

**Performance bottleneck**: All traffic through one point

**Added complexity**: Another component to manage

**Latency overhead**: Extra network hop

**Configuration complexity**: Routing rules can become complex

### Service Registry/Discovery

**Purpose**: Keeps track of active service instances and enables dynamic service discovery

**How it works**:
- Services register themselves when they start
- Other services query registry to find available instances
- Enables dynamic scaling and service location

**Tools**: Consul, Eureka, etcd

### DNS (Domain Name System)

**Purpose**: Translates human-readable domain names (like myapp.com) into IP addresses (like 192.168.1.2)

**How It Works**:
1. Client requests myapp.com
2. DNS resolver queries:
   - Root Server → TLD Server (.com) → Authoritative Server
3. Returns IP address (e.g., 54.12.32.18)
4. Browser connects to that IP

**In Cloud Context**: Services like AWS Route 53, Azure DNS, or Cloudflare manage DNS zones with load balancing, failover, and latency-based routing

**Integration**: Often integrated with service discovery for microservices

### Load Balancers

**Purpose**: A load balancer evenly distributes incoming requests among multiple backend servers to ensure:
- High availability
- Fault tolerance
- Scalability

#### Types of Load Balancers

| Type | Operates On | Example | Use Case |
|------|-------------|---------|----------|
| L4 (Transport) | TCP/UDP | AWS NLB, HAProxy | Fast, for network-level routing |
| L7 (Application) | HTTP/HTTPS | AWS ALB, Nginx, Traefik | Smarter, inspects URLs, headers, cookies |

#### Features
- Health checks
- Sticky sessions
- SSL termination
- Auto-scaling integration

---

## Communication Patterns

### Synchronous vs Asynchronous Communication

| Aspect | Synchronous | Asynchronous |
|--------|-------------|--------------|
| Definition | Caller waits for response | Caller sends message and continues |
| Example | REST API call | Message queue (Kafka, RabbitMQ) |
| Protocol | HTTP/HTTPS | AMQP, MQTT, Kafka |
| Use Case | Real-time responses (e.g., login) | Decoupled processing (e.g., notifications, billing) |
| Drawback | Tight coupling, latency-sensitive | Complex to debug, eventual consistency |

**Visual**:
```
SYNC: Client → Service A → waits → Response
ASYNC: Client → Service A → sends to Queue → Service B processes later
```

### Synchronous Communication

**Definition**: Request-response communication between services, typically over HTTP/REST or gRPC

**Protocol**: HTTP/HTTPS

**Characteristics**: Easy to implement but can increase latency

**Use Case**: Real-time responses (e.g., login, checkout)

**Drawback**: Tight coupling, latency-sensitive

### Asynchronous Communication

**Definition**: Event-driven communication via message brokers

**Protocol**: AMQP, MQTT, Kafka

**Benefits**: Decouples services and improves scalability and resilience

**Use Case**: Decoupled processing (e.g., notifications, billing)

**Drawback**: Complex to debug, eventual consistency

### Message Broker

**Purpose**: Handles async communication, message queues, pub/sub, and event streaming

**Examples**: Kafka, RabbitMQ, AWS SQS

**How it works**: Services publish events to broker, other services subscribe to events for reactive workflows

**Pattern**: Supports eventual consistency

### Event Bus/Event Sync

**Purpose**: Services publish events to an event bus. Other services subscribe to events for reactive workflows

**Tools**: Kafka, NATS

**Benefit**: Supports eventual consistency

---

## Data Storage

### Database per Service

**Concept**: Each microservice owns its database to maintain loose coupling

**Approach**: Can be SQL (PostgreSQL, MySQL) or NoSQL (MongoDB, DynamoDB, Cassandra) depending on requirements

**Pattern**: Polyglot persistence - use different databases based on service needs

### SQL vs NoSQL Databases

| Feature | SQL (Relational) | NoSQL (Non-relational) |
|---------|------------------|------------------------|
| Structure | Tables, rows, columns | Key-value, Document, Graph, Wide-column |
| Schema | Fixed | Dynamic |
| Scaling | Vertical (add more CPU/RAM) | Horizontal (add more nodes) |
| Examples | MySQL, PostgreSQL | MongoDB, DynamoDB, Cassandra |
| Use Cases | Transactions, structured data | High scalability, flexible schema |
| Query Language | SQL | Custom (JSON-like) |

#### SQL Databases

**Structure**: Structured data storage, supports transactions and complex queries

**Best for**: Relational data and consistency needs

**Scaling**: Vertical (add more CPU/RAM)

**Examples**: MySQL, PostgreSQL

#### NoSQL Databases

**Structure**: Flexible schema, horizontal scalability, high throughput

**Suitable for**: Unstructured data, caching, and fast lookups

**Scaling**: Horizontal (add more nodes)

**Examples**: MongoDB, DynamoDB, Cassandra

**Note**: Microservices often use both SQL and NoSQL, depending on the service's need (polyglot persistence)

---

## Supporting Infrastructure

### API Documentation

**Tool**: Swagger/OpenAPI

**Purpose**: API documentation and contract generation

**Benefits**: Helps teams understand endpoints, request/response models, and test APIs easily

### Externalized Configuration

**Purpose**: Centralized configuration management

**Tools**: Spring Cloud Config, Consul

**Benefits**: Easier management across services, environment-specific settings without code changes

### Externalized Logs

#### Why Externalize?

Each microservice runs in containers/pods — their local logs vanish when the container restarts. So logs are centralized in an external system.

#### Common Setup

| Tool | Role |
|------|------|
| Fluentd / Fluent Bit / Logstash | Collect logs |
| Elasticsearch / Loki | Store & index logs |
| Kibana / Grafana | Visualize & search logs |
| CloudWatch (AWS), Stackdriver (GCP), Dynatrace | Cloud-native logging |

#### Benefits

**Unified view**: See system behavior across all services

**Easier debugging & auditing**: Centralized troubleshooting

**Log-based alerting**: Via Prometheus + Alertmanager

### Monitoring/Tracing

**Purpose**: Observability tools to track service health, performance, and errors

**Examples**: Prometheus, Grafana, Jaeger, Zipkin

**Function**: Track distributed requests across multiple services

### Reporting/Analytics

**Purpose**: Aggregates data from multiple services for business intelligence and reporting

**Tools**: Often built on ELK, Redshift, Snowflake, or custom analytics pipelines

---

## 12-Factor App Methodology

A design philosophy for building scalable, cloud-ready microservices.

| Factor | Description |
|--------|-------------|
| 1. Codebase | One codebase tracked in version control, many deploys |
| 2. Dependencies | Explicitly declare via dependency manager (pip, npm) |
| 3. Config | Store in environment variables, not code |
| 4. Backing Services | Treat DBs, queues, caches as attached resources |
| 5. Build, Release, Run | Strictly separate stages |
| 6. Processes | Execute app as stateless processes |
| 7. Port Binding | Expose services via port binding |
| 8. Concurrency | Scale out via process model |
| 9. Disposability | Fast startup/shutdown |
| 10. Dev/Prod Parity | Keep environments similar |
| 11. Logs | Treat logs as event streams |
| 12. Admin Processes | Run admin tasks as one-off processes |

**Goal**: These principles ensure portability, scalability, and resilience — ideal for microservices, containers and Kubernetes

---

## Simple Implementation Example

### Express.js API Gateway

```javascript
const express = require('express');
const axios = require('axios');

const app = express();

// Route to User Service
app.get('/api/users/:id', async (req, res) => {
  const response = await axios.get(
    `http://user-service:8001/users/${req.params.id}`
  );
  res.json(response.data);
});

// Route to Order Service
app.get('/api/orders/:id', async (req, res) => {
  const response = await axios.get(
    `http://order-service:8002/orders/${req.params.id}`
  );
  res.json(response.data);
});

// Aggregate multiple services
app.get('/api/dashboard/:userId', async (req, res) => {
  const [user, orders, stats] = await Promise.all([
    axios.get(`http://user-service:8001/users/${req.params.userId}`),
    axios.get(`http://order-service:8002/orders/user/${req.params.userId}`),
    axios.get(`http://analytics-service:8003/stats/${req.params.userId}`)
  ]);
  
  res.json({
    user: user.data,
    orders: orders.data,
    stats: stats.data
  });
});

app.listen(80, () => {
  console.log('API Gateway running on port 80');
});
```

---

## Architecture Flow Summary

**Typical Request Flow**:
1. Client request hits API Gateway
2. Gateway performs authentication and authorization
3. Gateway routes request to appropriate service
4. Load Balancer distributes request to service instances
5. Service processes request (queries own database)
6. Response returned to gateway
7. Gateway aggregates responses if needed
8. Gateway caches response
9. Response sent to client
10. All activity logged to centralized logging system

**Communication Patterns**:
- Synchronous: Direct HTTP/REST calls with immediate response
- Asynchronous: Message broker for decoupled, event-driven workflows