# ADR-003: Database Per Service

**Status**: Accepted  
**Date**: 2026-01-22  
**Deciders**: Architecture Review Board  
**Well-Architected Pillars**: Reliability, Performance Efficiency, Operational Excellence

## Context

In a microservices architecture, we need to decide how services access data while maintaining service autonomy, preventing coupling, and ensuring independent scalability.

## Decision

Each microservice MUST own its data store. No direct database access across service boundaries.

### Core Principles

1. **Exclusive Ownership**: Each service exclusively owns its database schema
2. **Data Access**: Other services access data only via APIs or events
3. **Technology Flexibility**: Services can choose appropriate database types
4. **Independent Scaling**: Database scales independently with service needs

### Approved Database Types

| Use Case | Database Type | Example Services |
|----------|---------------|------------------|
| Transactional | PostgreSQL | Orders, Inventory, Payments |
| Document/Flexible Schema | MongoDB | Product Catalog, User Profiles |
| Cache | Redis | Session, Rate Limiting |
| Time Series | InfluxDB/TimescaleDB | Metrics, Logs, Events |
| Graph | Neo4j | Social, Recommendations |
| Search | Elasticsearch | Product Search, Logs |

### DO ✅

**Data Ownership**
- Each service owns its schema migrations
- Service controls all CRUD operations on its data
- Database credentials are service-specific (not shared)
- Use managed database services (Azure Database for PostgreSQL/MongoDB)

**Cross-Service Data Access**
- Use REST APIs or GraphQL for reads
- Use events for data changes (eventual consistency)
- Implement API caching where appropriate
- Use correlation IDs for tracing cross-service queries

**Data Consistency**
- Embrace eventual consistency between services
- Use Saga pattern for distributed transactions
- Implement compensating transactions for rollbacks
- Maintain idempotency for all operations

**Schema Evolution**
- Use database migration tools (Flyway for Java, Alembic for Python)
- Version your schemas
- Support backward compatibility during migrations
- Blue-green deployments for major schema changes

### DON'T ❌

**Anti-Patterns**
- ❌ Shared database across services
- ❌ Direct database-to-database synchronization
- ❌ Cross-service foreign keys
- ❌ Database views spanning multiple service schemas
- ❌ Shared database users/credentials
- ❌ Ad-hoc queries across service boundaries

**Data Duplication Misconceptions**
- ❌ Treating data duplication as inherently bad
- ❌ Attempting to normalize across service boundaries
- ❌ Using distributed transactions (2PC) instead of sagas

## Architecture Patterns

### Pattern 1: API-Based Data Access
```
┌──────────┐                  ┌──────────┐
│ Service A│──── REST API ────▶│ Service B│
└──────────┘                  └────┬─────┘
                                   │
                              ┌────▼─────┐
                              │  DB-B    │
                              └──────────┘
```

**Use When**: Synchronous read operations, strong consistency needed

### Pattern 2: Event-Based Data Sync
```
┌──────────┐    Event: OrderCreated    ┌──────────┐
│ Orders   │─────────▶ Event Bus ──────▶│ Shipping │
│ Service  │                            │ Service  │
└────┬─────┘                            └────┬─────┘
     │                                       │
┌────▼─────┐                           ┌────▼─────┐
│ Orders   │                           │ Shipping │
│ DB       │                           │ DB (copy)│
└──────────┘                           └──────────┘
```

**Use When**: Eventual consistency acceptable, high performance needed

### Pattern 3: CQRS with Shared Read Model
```
┌─────────┐  writes  ┌──────────┐
│Service A│─────────▶│  DB-A    │
└─────────┘          └────┬─────┘
                          │ events
┌─────────┐  writes  ┌───▼──────┐       ┌──────────────┐
│Service B│─────────▶│  DB-B    │──────▶│  Read Model  │
└─────────┘          └────┬─────┘       │ (Materialized│
                          │ events       │    View)     │
                          └─────────────▶└──────────────┘
                                                 ▲
                                                 │ reads
                                           ┌─────┴────┐
                                           │  Clients │
                                           └──────────┘
```

**Use When**: Complex queries across services, reporting needs

## Data Consistency Strategies

### Saga Pattern for Distributed Transactions

**Choreography-Based**
```java
// Order Service - starts saga
@Transactional
public Order createOrder(OrderRequest request) {
    Order order = orderRepository.save(new Order(request));
    eventPublisher.publish(new OrderCreated(order.getId()));
    return order;
}

// Payment Service - continues saga
@EventListener
public void handleOrderCreated(OrderCreated event) {
    try {
        Payment payment = processPayment(event.getOrderId());
        eventPublisher.publish(new PaymentCompleted(payment));
    } catch (Exception e) {
        eventPublisher.publish(new PaymentFailed(event.getOrderId()));
    }
}

// Order Service - compensate on failure
@EventListener
public void handlePaymentFailed(PaymentFailed event) {
    orderRepository.cancelOrder(event.getOrderId());
}
```

### Outbox Pattern for Reliable Events

```java
@Transactional
public Order createOrder(OrderRequest request) {
    // 1. Update database
    Order order = orderRepository.save(new Order(request));
    
    // 2. Store event in outbox table (same transaction)
    OutboxEvent outbox = new OutboxEvent(
        "OrderCreated",
        order.getId(),
        objectMapper.writeValueAsString(order)
    );
    outboxRepository.save(outbox);
    
    return order;
}

// Separate process polls outbox and publishes events
@Scheduled(fixedDelay = 1000)
public void publishOutboxEvents() {
    List<OutboxEvent> events = outboxRepository.findPending();
    events.forEach(event -> {
        eventPublisher.publish(event);
        outboxRepository.markPublished(event.getId());
    });
}
```

## Practical Guidelines

### Connection Management

```yaml
# Use connection pooling
spring:
  datasource:
    hikari:
      maximum-pool-size: 20
      minimum-idle: 5
      connection-timeout: 30000
      idle-timeout: 600000
      max-lifetime: 1800000
```

### Database Secrets Management

```bash
# Never hardcode credentials
# Use Azure Key Vault or managed identities

export DB_USERNAME=$(az keyvault secret show \
  --vault-name ${VAULT_NAME} \
  --name db-username \
  --query value -o tsv)

export DB_PASSWORD=$(az keyvault secret show \
  --vault-name ${VAULT_NAME} \
  --name db-password \
  --query value -o tsv)
```

### Migration Best Practices

```sql
-- Flyway migration: V1__create_orders_table.sql
CREATE TABLE IF NOT EXISTS orders (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    customer_id VARCHAR(255) NOT NULL,
    total_amount DECIMAL(10,2) NOT NULL,
    status VARCHAR(50) NOT NULL,
    created_at TIMESTAMP NOT NULL DEFAULT NOW(),
    updated_at TIMESTAMP NOT NULL DEFAULT NOW()
);

CREATE INDEX idx_orders_customer_id ON orders(customer_id);
CREATE INDEX idx_orders_status ON orders(status);
```

## Monitoring and Observability

### Required Metrics

- Connection pool utilization
- Query execution time (P50, P95, P99)
- Database CPU/memory usage
- Replication lag (if applicable)
- Failed connection attempts
- Transaction rollback rate

### Alerting Thresholds

| Metric | Warning | Critical |
|--------|---------|----------|
| Connection pool usage | > 70% | > 90% |
| Query latency P95 | > 500ms | > 1000ms |
| Database CPU | > 70% | > 90% |
| Replication lag | > 10s | > 60s |

## Consequences

### Positive
- ✅ Service autonomy and independence
- ✅ Technology flexibility (polyglot persistence)
- ✅ Independent scaling
- ✅ Fault isolation (DB failure affects one service)
- ✅ Team ownership clarity

### Negative
- ❌ Data duplication across services
- ❌ No ACID transactions across services
- ❌ Increased complexity for cross-service queries
- ❌ Eventual consistency challenges
- ❌ Higher operational overhead

## Migration Strategy

For legacy monolithic databases:

1. **Identify Bounded Contexts**: Map domain boundaries
2. **Extract Schema**: Move tables to service-specific schemas
3. **Create Service APIs**: Build read/write APIs
4. **Migrate Clients**: Update to use APIs instead of direct DB access
5. **Separate Databases**: Move to independent database instances
6. **Remove Dependencies**: Eliminate cross-schema queries

## Exceptions

**When Shared Database May Be Acceptable**:
- Very early startup phase (< 3 services)
- Read-only reporting database (separate from operational DBs)
- Legacy migration in progress
- Must get explicit architecture review approval

## Related

- ADR-001: Microservices Architecture
- ADR-002: API Gateway Pattern
- Platform Assumptions: Azure Database Services
- Security Baseline: Database Access Control
