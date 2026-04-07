# Example: Order Management System

This is a **complete working example** demonstrating how the Platform Engineering framework generates production-ready applications.

## What's Included

This example shows what you get when you run:
```
@workspace /scaffold-web-api order-service
@workspace /scaffold-frontend order-admin
```

## Architecture

```
┌──────────────┐
│  Order Admin │  (React/TypeScript SPA)
│   Frontend   │  - Azure AD B2C auth
└──────┬───────┘  - React Query
       │          - Tailwind CSS
       │ HTTPS    - Form validation
       v
┌──────────────┐
│ API Gateway  │  (Azure API Management)
│              │  - Authentication
└──────┬───────┘  - Rate limiting
       │          - Routing
       │ mTLS
       v
┌──────────────┐
│Order Service │  (Spring Boot REST API)
│              │  - Azure AD OAuth2
│              │  - PostgreSQL
│              │  - OpenTelemetry
│              │  - Health checks
└──────┬───────┘
       │
       v
┌──────────────┐
│  PostgreSQL  │  (Azure Database)
│   Database   │  - Encrypted at rest
└──────────────┘  - Managed identity auth
```

## File Structure

### Backend (order-service/)

```
order-service/
├── src/main/java/com/organization/orderservice/
│   ├── OrderServiceApplication.java          # Main application
│   ├── config/
│   │   ├── SecurityConfig.java               # Azure AD OAuth2 config
│   │   ├── DatabaseConfig.java               # Connection pool, JPA
│   │   ├── ObservabilityConfig.java          # OpenTelemetry setup
│   │   └── CorsConfig.java                   # CORS policy
│   ├── controller/
│   │   ├── HealthController.java             # Custom health checks
│   │   └── OrderController.java              # REST endpoints
│   ├── service/
│   │   └── OrderService.java                 # Business logic
│   ├── repository/
│   │   └── OrderRepository.java              # JPA repository
│   ├── model/
│   │   ├── entity/
│   │   │   └── Order.java                    # JPA entity
│   │   └── dto/
│   │       ├── OrderRequest.java             # Create/update DTO
│   │       └── OrderResponse.java            # Response DTO
│   ├── exception/
│   │   ├── GlobalExceptionHandler.java       # Standard error handling
│   │   ├── BusinessException.java            # Business exceptions
│   │   └── OrderNotFoundException.java       # Not found exception
│   └── util/
│       └── OrderMapper.java                  # Entity<->DTO mapping
├── src/main/resources/
│   ├── application.yml                       # Base configuration
│   ├── application-dev.yml                   # Dev settings
│   ├── application-prod.yml                  # Production settings
│   └── db/migration/
│       ├── V1__create_orders_table.sql       # Initial schema
│       └── V2__add_order_items.sql           # Schema evolution
├── src/test/java/
│   ├── integration/
│   │   └── OrderControllerIntegrationTest.java
│   └── unit/
│       └── OrderServiceTest.java
├── .github/workflows/
│   ├── ci.yml                                # CI pipeline
│   └── cd.yml                                # Deployment pipeline
├── k8s/
│   ├── deployment.yml                        # Kubernetes deployment
│   ├── service.yml                           # Kubernetes service
│   ├── ingress.yml                           # Ingress rules
│   └── hpa.yml                               # Horizontal autoscaler
├── Dockerfile                                # Multi-stage Docker build
├── pom.xml                                   # Maven dependencies
└── README.md                                 # Setup instructions
```

### Frontend (order-admin/)

```
order-admin/
├── src/
│   ├── main.tsx                              # Entry point
│   ├── App.tsx                               # Root component
│   ├── config/
│   │   ├── auth.ts                           # MSAL configuration
│   │   ├── api.ts                            # Axios client
│   │   └── observability.ts                 # OpenTelemetry
│   ├── features/
│   │   ├── auth/
│   │   │   ├── components/
│   │   │   │   ├── LoginButton.tsx
│   │   │   │   └── UserProfile.tsx
│   │   │   ├── hooks/
│   │   │   │   └── useAuth.ts
│   │   │   └── AuthProvider.tsx
│   │   └── orders/
│   │       ├── components/
│   │       │   ├── OrderList.tsx             # List view
│   │       │   ├── OrderForm.tsx             # Create/edit form
│   │       │   ├── OrderDetails.tsx          # Detail view
│   │       │   └── OrderFilters.tsx          # Filter controls
│   │       ├── hooks/
│   │       │   └── useOrders.ts              # React Query hooks
│   │       ├── api/
│   │       │   └── ordersApi.ts              # API client
│   │       └── types/
│   │           └── order.ts                  # TypeScript types
│   ├── shared/
│   │   ├── components/
│   │   │   ├── Layout.tsx                    # App layout
│   │   │   ├── ErrorBoundary.tsx             # Error boundary
│   │   │   ├── LoadingSpinner.tsx            # Loading state
│   │   │   └── ErrorMessage.tsx              # Error display
│   │   ├── hooks/
│   │   │   ├── useApi.ts                     # API hook
│   │   │   └── useToast.ts                   # Toast notifications
│   │   └── utils/
│   │       ├── logger.ts                     # Structured logging
│   │       ├── validators.ts                 # Validation helpers
│   │       └── formatters.ts                 # Data formatters
│   └── styles/
│       └── globals.css                       # Global styles
├── public/
│   └── assets/
├── tests/
│   ├── unit/
│   └── e2e/
├── .github/workflows/
│   └── deploy.yml                            # Static Web App deployment
├── vite.config.ts                            # Vite configuration
├── tsconfig.json                             # TypeScript config
├── tailwind.config.js                        # Tailwind CSS config
├── eslint.config.js                          # ESLint rules
├── package.json                              # Dependencies
└── README.md                                 # Setup instructions
```

## Key Features Demonstrated

### ✅ Security (Layer 1 - Security Baseline)

**Backend**:
- Azure AD OAuth2 JWT authentication
- All secrets in Azure Key Vault (no hardcoded values)
- Parameterized queries (SQL injection prevention)
- Input validation with Bean Validation
- Security headers (HSTS, CSP, X-Frame-Options)
- OWASP dependency scanning
- Security audit logging

**Frontend**:
- Azure AD B2C authentication with MSAL
- Automatic token refresh
- XSS prevention (React automatic escaping)
- Form validation with Zod
- Content Security Policy
- Secure cookie handling
- No PII in logs

### ✅ Reliability (Layer 1 - ADR-001)

**Backend**:
- Circuit breakers (Resilience4j)
- Retry logic with exponential backoff
- Health checks (liveness/readiness)
- Graceful shutdown
- Database connection pooling

**Frontend**:
- Error boundaries
- Retry logic (React Query)
- Offline detection
- Optimistic updates
- Cache invalidation

### ✅ Observability (Layer 2 - Golden Paths)

**Backend**:
- Structured JSON logging (Logstash)
- Distributed tracing (OpenTelemetry)
- Metrics (Micrometer + Prometheus)
- Correlation IDs
- Request/response logging

**Frontend**:
- Browser error tracking
- Performance monitoring
- User interaction tracing
- API call metrics
- Bundle size monitoring

### ✅ Operational Excellence (Layer 2 - Golden Paths)

**Backend**:
- Infrastructure as Code (Kubernetes YAML)
- CI/CD pipeline (GitHub Actions)
- Automated testing (unit + integration)
- Database migrations (Flyway)
- Docker multi-stage builds
- Non-root container user

**Frontend**:
- CI/CD for Azure Static Web Apps
- Automated testing (Vitest + Playwright)
- Code splitting
- Tree shaking
- Asset optimization
- CDN deployment

## Running the Example

### Backend (order-service)

```bash
# Prerequisites
# - JDK 21
# - Maven 3.9+
# - Docker
# - Azure CLI
# - PostgreSQL (local or Azure)

# 1. Set up Azure resources
az login

# Create resource group
az group create \
  --name rg-order-service \
  --location eastus

# Create Key Vault
az keyvault create \
  --name kv-order-service \
  --resource-group rg-order-service \
  --location eastus

# Create PostgreSQL
az postgres flexible-server create \
  --name pg-order-service \
  --resource-group rg-order-service \
  --location eastus \
  --admin-user orderadmin \
  --admin-password <strong-password> \
  --sku-name Standard_B1ms \
  --version 14

# Add secrets to Key Vault
az keyvault secret set \
  --vault-name kv-order-service \
  --name db-password \
  --value <strong-password>

# 2. Configure environment
export AZURE_CLIENT_ID=<your-client-id>
export AZURE_TENANT_ID=<your-tenant-id>
export AZURE_CLIENT_SECRET=<your-client-secret>
export AZURE_KEY_VAULT_ENDPOINT=https://kv-order-service.vault.azure.net/

export DB_HOST=pg-order-service.postgres.database.azure.com
export DB_PORT=5432
export DB_NAME=orders
export DB_USERNAME=orderadmin

# 3. Build and run
cd order-service
mvn clean install
mvn spring-boot:run

# 4. Test
curl http://localhost:8080/actuator/health
curl http://localhost:8080/api/v1/orders \
  -H "Authorization: Bearer <jwt-token>"

# 5. Run tests
mvn test
mvn verify

# 6. Build Docker image
docker build -t order-service:latest .

# 7. Deploy to Kubernetes
kubectl apply -f k8s/
```

### Frontend (order-admin)

```bash
# Prerequisites
# - Node.js 20+
# - npm 10+
# - Azure CLI

# 1. Configure Azure AD B2C
# (Follow Azure portal instructions)

# 2. Set up environment
cat > .env.local << EOF
VITE_AZURE_CLIENT_ID=<your-client-id>
VITE_AZURE_TENANT_ID=<your-tenant-id>
VITE_AZURE_TENANT_NAME=<your-tenant-name>
VITE_AZURE_SIGNIN_POLICY=B2C_1_SignUpSignIn
VITE_API_BASE_URL=http://localhost:8080
VITE_API_CLIENT_ID=<api-client-id>
EOF

# 3. Install and run
cd order-admin
npm install
npm run dev

# Visit http://localhost:5173

# 4. Run tests
npm test
npm run test:e2e

# 5. Build for production
npm run build

# 6. Deploy to Azure Static Web Apps
# (Automatic via GitHub Actions on push)
```

## What's Included Out-of-the-Box

### Backend API
✅ Complete CRUD operations for Orders
✅ Azure AD authentication
✅ PostgreSQL database with migrations
✅ OpenAPI/Swagger documentation
✅ Health check endpoints
✅ Circuit breakers
✅ Distributed tracing
✅ Docker image
✅ Kubernetes manifests
✅ CI/CD pipeline
✅ Integration tests

### Frontend SPA
✅ Order list with filtering/sorting
✅ Create/edit order forms
✅ Order detail views
✅ Azure AD B2C login
✅ Responsive design (mobile-first)
✅ Loading states
✅ Error handling
✅ Form validation
✅ API state management (React Query)
✅ Routing (React Router)
✅ Unit tests
✅ E2E tests
✅ CI/CD pipeline

## Customization Examples

### Add a New Field to Order

**Backend** (`src/main/resources/db/migration/V3__add_customer_name.sql`):
```sql
ALTER TABLE orders ADD COLUMN customer_name VARCHAR(255);
```

**Backend** (`model/entity/Order.java`):
```java
@Column(name = "customer_name")
private String customerName;
```

**Frontend** (`features/orders/types/order.ts`):
```typescript
export interface Order {
  id: string;
  // ...existing fields
  customerName: string;  // Add this
}
```

**Frontend** (`features/orders/components/OrderForm.tsx`):
```typescript
const orderSchema = z.object({
  // ...existing fields
  customerName: z.string().min(2).max(255),
});
```

Then:
```
@workspace apply these changes across all files
```

### Add Redis Caching

```
@workspace add Redis caching to OrderService for read operations
```

Copilot will:
1. Add Redis dependency to pom.xml
2. Add Redis configuration
3. Annotate service methods with `@Cacheable`
4. Update Kubernetes deployment with Redis sidecar

### Add Kafka Events

```
@workspace publish OrderCreated events to Kafka when orders are created
```

Copilot will:
1. Add Kafka dependencies
2. Configure Kafka producer
3. Implement event publishing in service
4. Add integration tests for events

## Performance Benchmarks

These are the targets enforced by CI/CD:

### Backend
- Response time P95: < 200ms
- Response time P99: < 500ms
- Throughput: > 1000 req/sec (per instance)
- Error rate: < 0.1%

### Frontend
- Time to First Byte: < 200ms
- First Contentful Paint: < 1.5s
- Time to Interactive: < 3.5s
- Lighthouse Score: > 90

## Security Scan Results

```bash
# Backend
mvn dependency-check:check
# ✅ 0 critical vulnerabilities
# ✅ 0 high vulnerabilities

# Frontend
npm audit
# ✅ 0 vulnerabilities
```

## Cost Estimates (Azure)

Monthly cost for this example (production):

| Resource | SKU | Cost |
|----------|-----|------|
| App Service Plan | P1v3 (2 instances) | $200 |
| PostgreSQL | Standard_B1ms | $30 |
| Static Web Apps | Standard | $9 |
| API Management | Developer | $50 |
| Key Vault | Standard | $5 |
| Application Insights | Per GB | $10 |
| **Total** | | **~$304/month** |

For development environments, use lower SKUs (~$50/month).

## Next Steps

1. **Explore the code**: See how patterns are implemented
2. **Run locally**: Follow setup instructions above
3. **Customize**: Add your business logic
4. **Deploy**: Push to trigger CI/CD

## Questions?

- **Architecture**: See [ADRs](../mcp-servers/layer1-context/architecture-patterns/adr/)
- **Security**: See [Security Baseline](../mcp-servers/layer1-context/security-baseline/)
- **Patterns**: See [Golden Paths](../mcp-servers/layer2-goldenpaths/)
- **Usage**: See [Usage Guide](../docs/usage-guide.md)

---

This example shows **real, production-ready code** generated by our framework. Every service you scaffold will have this same level of completeness and quality! 🚀
