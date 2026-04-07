---
name: scaffold-frontend

applyTo:
  - '**/*.{ts,tsx,json}'
  - '**/package.json'
  - '**/tsconfig.json'
  - '**/*.config.{js,ts}'
  
description: >
  Scaffold a production-ready React/TypeScript SPA following organizational standards:
  Azure AD B2C authentication, React Query, React Router, Tailwind CSS, form validation,
  security baselines, and CI/CD for Azure Static Web Apps. Based on: mcp-servers/layer2-goldenpaths/frontend-spa

tools:
  allow:
    - create_file
    - replace_string_in_file
    - multi_replace_string_in_file
    - run_in_terminal
  deny: []
---

# Scaffold Frontend Skill

You are an expert at scaffolding production-ready React/TypeScript Single Page Applications following organizational standards.

## Your Mission

When the user invokes this skill with a command like:
- `@workspace /scaffold-frontend my-app`
- `scaffold a new React frontend called admin-portal`
- `create a SPA for customer management`

You will generate a complete, production-ready React application based on the **Golden Path: React/TypeScript SPA**.

## What You Generate

### 1. Project Structure

```
<app-name>/
├── src/
│   ├── main.tsx
│   ├── App.tsx
│   ├── config/
│   │   ├── auth.ts              # Azure AD B2C MSAL config
│   │   ├── api.ts               # Axios client with auth
│   │   └── observability.ts    # OpenTelemetry
│   ├── features/
│   │   ├── auth/
│   │   │   ├── components/
│   │   │   ├── hooks/useAuth.ts
│   │   │   └── AuthProvider.tsx
│   │   └── <resource>/
│   │       ├── components/
│   │       ├── hooks/use<Resource>s.ts
│   │       ├── api/<resource>Api.ts
│   │       └── types/<resource>.ts
│   ├── shared/
│   │   ├── components/
│   │   │   ├── Layout.tsx
│   │   │   ├── ErrorBoundary.tsx
│   │   │   └── LoadingSpinner.tsx
│   │   ├── hooks/
│   │   │   └── useApi.ts
│   │   └── utils/
│   │       └── logger.ts
│   └── styles/
│       └── globals.css
├── public/
├── tests/
├── .github/workflows/
├── vite.config.ts
├── tsconfig.json
├── tailwind.config.js
├── eslint.config.js
├── package.json
└── README.md
```

### 2. Required Dependencies

MUST include:
- React 18+
- TypeScript 5+
- Vite (build tool)
- Azure MSAL (@azure/msal-browser, @azure/msal-react)
- React Query (@tanstack/react-query)
- React Router (react-router-dom)
- React Hook Form + Zod (validation)
- Axios (API client)
- Tailwind CSS
- OpenTelemetry browser SDK
- Testing: Vitest, Testing Library, Playwright

**Reference**: See `mcp-servers/layer2-goldenpaths/frontend-spa/index.md` for exact versions.

### 3. Azure AD B2C Authentication (MUST)

```typescript
// config/auth.ts
export const msalConfig: Configuration = {
  auth: {
    clientId: import.meta.env.VITE_AZURE_CLIENT_ID,
    authority: `https://<tenant>.b2clogin.com/...`,
    knownAuthorities: [...],
    redirectUri: window.location.origin,
  },
  cache: {
    cacheLocation: "sessionStorage",
  },
  system: {
    loggerOptions: {
      loggerCallback: (level, message, containsPii) => {
        if (containsPii) return; // NEVER log PII
      },
    },
  },
};
```

**Requirements**:
- Authorization Code Flow with PKCE
- Token stored in sessionStorage (not localStorage for sensitive apps)
- Automatic token refresh
- Never log tokens or PII

### 4. Secure API Client

```typescript
// config/api.ts
export function createApiClient(
  msalInstance: PublicClientApplication
): AxiosInstance {
  const client = axios.create({
    baseURL: import.meta.env.VITE_API_BASE_URL,
    timeout: 30000,
    withCredentials: true,
  });
  
  // Request interceptor: Add auth token
  client.interceptors.request.use(async (config) => {
    const response = await msalInstance.acquireTokenSilent({...});
    config.headers.Authorization = `Bearer ${response.accessToken}`;
    config.headers["X-Request-ID"] = crypto.randomUUID();
    return config;
  });
  
  // Response interceptor: Handle 401, retry logic
  client.interceptors.response.use(
    (response) => response,
    async (error) => {
      if (error.response?.status === 401) {
        // Refresh token and retry
      }
    }
  );
  
  return client;
}
```

### 5. React Query Hooks Pattern

```typescript
// features/<resource>/hooks/use<Resource>s.ts
export function use<Resource>s() {
  const api = useApi();
  const resourceApi = new <Resource>Api(api);
  const queryClient = useQueryClient();
  
  const { data, isLoading } = useQuery({
    queryKey: ["<resources>"],
    queryFn: () => resourceApi.list(),
    staleTime: 5 * 60 * 1000,
  });
  
  const createMutation = useMutation({
    mutationFn: (data) => resourceApi.create(data),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ["<resources>"] });
      toast.success("Created successfully");
    },
  });
  
  return { data, isLoading, create: createMutation.mutate };
}
```

### 6. Form Validation with Zod

```typescript
// Validation schema
const resourceSchema = z.object({
  name: z.string()
    .min(3, "Name must be at least 3 characters")
    .max(100)
    .regex(/^[a-zA-Z0-9\s-]+$/, "Invalid characters"),
  amount: z.number().min(0.01).max(9999999.99),
  description: z.string().max(500),
});

// Form component
export function <Resource>Form() {
  const { register, handleSubmit, formState: { errors } } = useForm({
    resolver: zodResolver(resourceSchema),
  });
  
  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register("name")} />
      {errors.name && <p>{errors.name.message}</p>}
    </form>
  );
}
```

### 7. Error Boundary

```typescript
// shared/components/ErrorBoundary.tsx
export class ErrorBoundary extends React.Component {
  componentDidCatch(error: Error, errorInfo: React.ErrorInfo) {
    logger.error("Uncaught error", error, errorInfo);
  }
  
  render() {
    if (this.state.hasError) {
      return <ErrorFallback />;
    }
    return this.props.children;
  }
}
```

### 8. Security Configuration

**Environment Variables** (.env.example):
```env
VITE_AZURE_CLIENT_ID=
VITE_AZURE_TENANT_ID=
VITE_AZURE_TENANT_NAME=
VITE_AZURE_SIGNIN_POLICY=
VITE_API_BASE_URL=
VITE_API_CLIENT_ID=
```

**Security Headers** (nginx.conf for production):
```nginx
add_header X-Frame-Options "DENY" always;
add_header X-Content-Type-Options "nosniff" always;
add_header X-XSS-Protection "1; mode=block" always;
add_header Referrer-Policy "strict-origin-when-cross-origin" always;
add_header Content-Security-Policy "default-src 'self'..." always;
```

### 9. Vite Configuration

```typescript
// vite.config.ts
export default defineConfig({
  plugins: [react()],
  build: {
    sourcemap: true,
    rollupOptions: {
      output: {
        manualChunks: {
          vendor: ["react", "react-dom"],
          msal: ["@azure/msal-browser", "@azure/msal-react"],
        },
      },
    },
  },
});
```

### 10. ESLint Configuration

```javascript
// eslint.config.js
export default [
  {
    plugins: {
      react: reactPlugin,
      security: securityPlugin,
    },
    rules: {
      "react-hooks/rules-of-hooks": "error",
      "react-hooks/exhaustive-deps": "warn",
      "security/detect-object-injection": "warn",
      "@typescript-eslint/no-explicit-any": "warn",
    },
  },
];
```

### 11. CI/CD Pipeline

```yaml
# .github/workflows/deploy.yml
name: Deploy to Azure Static Web Apps
on:
  push:
    branches: [main]
jobs:
  build_and_deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
      - run: npm ci
      - run: npm run lint
      - run: npm run type-check
      - run: npm audit --audit-level=moderate
      - run: npm test
      - run: npm run build
      - uses: Azure/static-web-apps-deploy@v1
```

## Parameters

When scaffolding, ask the user:

1. **App Name**: What is the application called?
   - Example: "admin-portal", "customer-dashboard"
   - Used for: package name, build output

2. **Primary Resource**: What does this app manage?
   - Example: "Order", "Product", "User"
   - Used for: feature folder names, API calls

3. **Authentication**: Azure AD or Azure AD B2C?
   - Default: Azure AD B2C (for external users)
   - Affects: MSAL configuration

4. **Styling**: Tailwind CSS or Material-UI?
   - Default: Tailwind CSS
   - Affects: dependencies and component styling

## Mandatory Checklist

Before completing scaffolding, ensure:

- [ ] Azure AD B2C authentication configured (MSAL)
- [ ] All API calls use authenticated axios client
- [ ] Environment variables for all secrets (never hardcoded)
- [ ] Form validation with Zod
- [ ] Error boundary implemented
- [ ] Loading states for all async operations
- [ ] Security headers configured (nginx.conf)
- [ ] ESLint with security rules
- [ ] Input sanitization (prevent XSS)
- [ ] CSRF protection (withCredentials: true)
- [ ] Dependency vulnerability scanning (npm audit)
- [ ] TypeScript strict mode enabled
- [ ] React Query for server state management
- [ ] OpenTelemetry browser tracing configured
- [ ] Structured error logging
- [ ] Responsive design (mobile-first)
- [ ] Accessibility (ARIA labels, keyboard navigation)
- [ ] CI/CD pipeline

## Well-Architected Framework Alignment

- **Reliability**: Error boundaries, retry logic, offline handling
- **Security**: Azure AD B2C, token management, XSS prevention, CSP
- **Cost Optimization**: Code splitting, lazy loading, CDN caching
- **Operational Excellence**: Structured logging, tracing, metrics
- **Performance Efficiency**: Bundle optimization, lazy loading, caching

## References

- **Golden Path Specification**: `mcp-servers/layer2-goldenpaths/frontend-spa/index.md`
- **Security Requirements**: `mcp-servers/layer1-context/security-baseline/index.md`
- **Backend API**: `mcp-servers/layer2-goldenpaths/web-api/index.md`

## Example Usage

User: `@workspace /scaffold-frontend customer-portal`

You:
1. Ask clarifying questions (primary resource, auth type)
2. Generate all files following the Golden Path
3. Run `npm install` and `npm run type-check` to validate
4. Provide next steps:
   ```
   Next steps:
   1. Configure Azure AD B2C app registration
   2. Update .env file with Azure credentials
   3. Run: npm run dev
   4. Test: http://localhost:5173
   5. Deploy: Push to main branch for automatic deployment
   ```

## Anti-Patterns to Avoid

❌ DON'T:
- Store tokens in localStorage (use sessionStorage)
- Log sensitive data (tokens, PII)
- Use dangerouslySetInnerHTML without sanitization
- Skip input validation
- Disable ESLint security rules
- Hardcode API URLs or credentials
- Skip error boundaries
- Ignore accessibility
- Use `any` type excessively
- Skip loading/error states

✅ DO:
- Follow the Golden Path exactly
- Implement comprehensive error handling
- Use TypeScript strictly
- Validate all user input
- Sanitize data before rendering
- Use semantic HTML
- Implement proper loading states
- Test accessibility
- Monitor bundle size
- Use React Query for server state
