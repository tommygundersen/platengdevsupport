# Golden Path: React/TypeScript SPA Frontend

**Status**: Approved  
**Last Updated**: 2026-04-07  
**Maintainer**: Platform Engineering Team  
**Well-Architected Pillars**: All

## Overview

This is the **authoritative template** for building Single Page Applications (SPAs) using React, TypeScript, and modern tooling. When you scaffold a new frontend application, this is what gets generated.

## 🎯 What You Get

A production-ready SPA with:
- ✅ React 18+ with TypeScript
- ✅ Vite for fast development and build
- ✅ Azure AD B2C authentication (MSAL)
- ✅ React Query for server state management
- ✅ React Router for navigation
- ✅ Tailwind CSS for styling
- ✅ Form validation with React Hook Form + Zod
- ✅ OpenTelemetry browser tracing
- ✅ Error boundaries and logging
- ✅ CI/CD pipeline for Azure Static Web Apps
- ✅ Comprehensive ESLint and security rules

## 🏗️ Project Structure

```
my-frontend/
├── src/
│   ├── main.tsx                  # Application entry point
│   ├── App.tsx                   # Root component
│   ├── config/
│   │   ├── auth.ts               # Azure AD B2C configuration
│   │   ├── api.ts                # API client configuration
│   │   └── observability.ts     # OpenTelemetry setup
│   ├── features/
│   │   ├── auth/
│   │   │   ├── components/
│   │   │   │   ├── LoginButton.tsx
│   │   │   │   └── UserProfile.tsx
│   │   │   ├── hooks/
│   │   │   │   └── useAuth.ts
│   │   │   └── AuthProvider.tsx
│   │   └── resources/
│   │       ├── components/
│   │       │   ├── ResourceList.tsx
│   │       │   ├── ResourceForm.tsx
│   │       │   └── ResourceDetails.tsx
│   │       ├── hooks/
│   │       │   └── useResources.ts
│   │       ├── api/
│   │       │   └── resourcesApi.ts
│   │       └── types/
│   │           └── resource.ts
│   ├── shared/
│   │   ├── components/
│   │   │   ├── Layout.tsx
│   │   │   ├── ErrorBoundary.tsx
│   │   │   ├── LoadingSpinner.tsx
│   │   │   └── ErrorMessage.tsx
│   │   ├── hooks/
│   │   │   ├── useApi.ts
│   │   │   └── useToast.ts
│   │   └── utils/
│   │       ├── logger.ts
│   │       ├── validators.ts
│   │       └── formatters.ts
│   ├── styles/
│   │   └── globals.css
│   └── types/
│       └── index.ts
├── public/
│   └── assets/
├── tests/
│   ├── unit/
│   └── e2e/
├── .github/
│   └── workflows/
│       └── deploy.yml
├── index.html
├── vite.config.ts
├── tsconfig.json
├── tailwind.config.js
├── eslint.config.js
├── package.json
└── README.md
```

## 📦 Required Dependencies (package.json)

```json
{
  "name": "my-frontend",
  "version": "1.0.0",
  "type": "module",
  "scripts": {
    "dev": "vite",
    "build": "tsc && vite build",
    "preview": "vite preview",
    "lint": "eslint . --ext ts,tsx",
    "lint:fix": "eslint . --ext ts,tsx --fix",
    "type-check": "tsc --noEmit",
    "test": "vitest",
    "test:e2e": "playwright test",
    "audit": "npm audit --audit-level=moderate"
  },
  "dependencies": {
    "react": "^18.2.0",
    "react-dom": "^18.2.0",
    "react-router-dom": "^6.20.0",
    
    "@azure/msal-browser": "^3.5.0",
    "@azure/msal-react": "^2.0.10",
    
    "@tanstack/react-query": "^5.12.0",
    "@tanstack/react-query-devtools": "^5.12.0",
    
    "react-hook-form": "^7.48.0",
    "zod": "^3.22.0",
    "@hookform/resolvers": "^3.3.0",
    
    "axios": "^1.6.0",
    
    "@opentelemetry/api": "^1.7.0",
    "@opentelemetry/sdk-trace-web": "^1.18.0",
    "@opentelemetry/exporter-trace-otlp-http": "^0.45.0",
    "@opentelemetry/instrumentation": "^0.45.0",
    "@opentelemetry/instrumentation-fetch": "^0.45.0",
    
    "clsx": "^2.0.0",
    "lucide-react": "^0.294.0",
    "sonner": "^1.2.0"
  },
  "devDependencies": {
    "@types/react": "^18.2.0",
    "@types/react-dom": "^18.2.0",
    "@types/node": "^20.10.0",
    
    "@vitejs/plugin-react": "^4.2.0",
    "vite": "^5.0.0",
    
    "typescript": "^5.3.0",
    
    "eslint": "^8.55.0",
    "@typescript-eslint/eslint-plugin": "^6.14.0",
    "@typescript-eslint/parser": "^6.14.0",
    "eslint-plugin-react": "^7.33.0",
    "eslint-plugin-react-hooks": "^4.6.0",
    "eslint-plugin-security": "^1.7.1",
    
    "tailwindcss": "^3.3.0",
    "postcss": "^8.4.0",
    "autoprefixer": "^10.4.0",
    
    "vitest": "^1.0.0",
    "@testing-library/react": "^14.1.0",
    "@testing-library/jest-dom": "^6.1.0",
    "@testing-library/user-event": "^14.5.0",
    
    "@playwright/test": "^1.40.0"
  }
}
```

## ⚙️ Azure AD B2C Configuration

```typescript
// src/config/auth.ts

import { Configuration, LogLevel } from "@azure/msal-browser";

/**
 * MSAL Configuration for Azure AD B2C
 * 
 * Security requirements:
 * - Use Authorization Code Flow with PKCE
 * - Store tokens in sessionStorage (not localStorage for sensitive apps)
 * - Implement token refresh before expiry
 * - Never log tokens or sensitive user data
 */
export const msalConfig: Configuration = {
  auth: {
    clientId: import.meta.env.VITE_AZURE_CLIENT_ID,
    authority: `https://${import.meta.env.VITE_AZURE_TENANT_NAME}.b2clogin.com/${import.meta.env.VITE_AZURE_TENANT_NAME}.onmicrosoft.com/${import.meta.env.VITE_AZURE_SIGNIN_POLICY}`,
    knownAuthorities: [
      `${import.meta.env.VITE_AZURE_TENANT_NAME}.b2clogin.com`
    ],
    redirectUri: window.location.origin,
    postLogoutRedirectUri: window.location.origin,
    navigateToLoginRequestUrl: false,
  },
  cache: {
    cacheLocation: "sessionStorage", // Use "sessionStorage" for sensitive apps
    storeAuthStateInCookie: false, // Set to true for IE11 support
  },
  system: {
    loggerOptions: {
      loggerCallback: (level, message, containsPii) => {
        if (containsPii) return; // Never log PII
        
        switch (level) {
          case LogLevel.Error:
            console.error(message);
            break;
          case LogLevel.Warning:
            console.warn(message);
            break;
          case LogLevel.Info:
            console.info(message);
            break;
          case LogLevel.Verbose:
            console.debug(message);
            break;
        }
      },
      logLevel: LogLevel.Warning,
    },
  },
};

/**
 * Scopes to request during login
 */
export const loginRequest = {
  scopes: [
    `https://${import.meta.env.VITE_AZURE_TENANT_NAME}.onmicrosoft.com/${import.meta.env.VITE_API_CLIENT_ID}/read`,
    `https://${import.meta.env.VITE_AZURE_TENANT_NAME}.onmicrosoft.com/${import.meta.env.VITE_API_CLIENT_ID}/write`,
  ],
};

/**
 * Scopes for API access tokens
 */
export const apiScopes = [
  `https://${import.meta.env.VITE_AZURE_TENANT_NAME}.onmicrosoft.com/${import.meta.env.VITE_API_CLIENT_ID}/read`,
  `https://${import.meta.env.VITE_AZURE_TENANT_NAME}.onmicrosoft.com/${import.meta.env.VITE_API_CLIENT_ID}/write`,
];
```

## 🔐 Authentication Provider

```typescript
// src/features/auth/AuthProvider.tsx

import React, { createContext, useContext, ReactNode } from "react";
import { 
  MsalProvider, 
  useMsal, 
  useIsAuthenticated,
  AuthenticatedTemplate,
  UnauthenticatedTemplate 
} from "@azure/msal-react";
import { IPublicClientApplication } from "@azure/msal-browser";
import { loginRequest } from "../../config/auth";

interface AuthContextType {
  isAuthenticated: boolean;
  user: any | null;
  login: () => void;
  logout: () => void;
  getAccessToken: () => Promise<string>;
}

const AuthContext = createContext<AuthContextType | undefined>(undefined);

export function AuthProvider({ 
  msalInstance, 
  children 
}: { 
  msalInstance: IPublicClientApplication; 
  children: ReactNode;
}) {
  return (
    <MsalProvider instance={msalInstance}>
      <AuthProviderInner>
        {children}
      </AuthProviderInner>
    </MsalProvider>
  );
}

function AuthProviderInner({ children }: { children: ReactNode }) {
  const { instance, accounts } = useMsal();
  const isAuthenticated = useIsAuthenticated();

  const login = async () => {
    try {
      await instance.loginPopup(loginRequest);
    } catch (error) {
      console.error("Login failed:", error);
      throw error;
    }
  };

  const logout = async () => {
    try {
      await instance.logoutPopup();
    } catch (error) {
      console.error("Logout failed:", error);
      throw error;
    }
  };

  const getAccessToken = async (): Promise<string> => {
    const account = accounts[0];
    if (!account) {
      throw new Error("No active account");
    }

    try {
      // Try to acquire token silently first
      const response = await instance.acquireTokenSilent({
        scopes: loginRequest.scopes,
        account,
      });
      return response.accessToken;
    } catch (error) {
      // If silent acquisition fails, prompt user
      const response = await instance.acquireTokenPopup({
        scopes: loginRequest.scopes,
        account,
      });
      return response.accessToken;
    }
  };

  const value: AuthContextType = {
    isAuthenticated,
    user: accounts[0] || null,
    login,
    logout,
    getAccessToken,
  };

  return (
    <AuthContext.Provider value={value}>
      {children}
    </AuthContext.Provider>
  );
}

export function useAuth() {
  const context = useContext(AuthContext);
  if (!context) {
    throw new Error("useAuth must be used within AuthProvider");
  }
  return context;
}
```

## 🌐 Secure API Client

```typescript
// src/config/api.ts

import axios, { AxiosInstance, AxiosError } from "axios";
import { PublicClientApplication } from "@azure/msal-browser";
import { apiScopes } from "./auth";
import { logger } from "../shared/utils/logger";

/**
 * Create authenticated API client
 * 
 * Security features:
 * - Automatic token attachment
 * - Token refresh before expiry
 * - Request/response logging (sanitized)
 * - Error handling with retry logic
 * - CSRF protection
 */
export function createApiClient(
  msalInstance: PublicClientApplication
): AxiosInstance {
  const client = axios.create({
    baseURL: import.meta.env.VITE_API_BASE_URL,
    timeout: 30000,
    headers: {
      "Content-Type": "application/json",
    },
    withCredentials: true, // Include cookies for CSRF
  });

  // Request interceptor: Add auth token
  client.interceptors.request.use(
    async (config) => {
      const accounts = msalInstance.getAllAccounts();
      
      if (accounts.length > 0) {
        try {
          const response = await msalInstance.acquireTokenSilent({
            scopes: apiScopes,
            account: accounts[0],
          });
          
          config.headers.Authorization = `Bearer ${response.accessToken}`;
          
          // Add correlation ID for tracing
          config.headers["X-Request-ID"] = crypto.randomUUID();
          
          // Log request (sanitized)
          logger.debug("API Request", {
            method: config.method,
            url: config.url,
            requestId: config.headers["X-Request-ID"],
          });
          
        } catch (error) {
          logger.error("Token acquisition failed", error);
          throw error;
        }
      }
      
      return config;
    },
    (error) => {
      logger.error("Request error", error);
      return Promise.reject(error);
    }
  );

  // Response interceptor: Handle errors
  client.interceptors.response.use(
    (response) => {
      // Log successful response
      logger.debug("API Response", {
        status: response.status,
        requestId: response.config.headers["X-Request-ID"],
      });
      return response;
    },
    async (error: AxiosError) => {
      const originalRequest: any = error.config;
      
      // Handle 401: Token expired, refresh and retry
      if (error.response?.status === 401 && !originalRequest._retry) {
        originalRequest._retry = true;
        
        const accounts = msalInstance.getAllAccounts();
        if (accounts.length > 0) {
          try {
            const response = await msalInstance.acquireTokenPopup({
              scopes: apiScopes,
              account: accounts[0],
            });
            
            originalRequest.headers.Authorization = 
              `Bearer ${response.accessToken}`;
            
            return client(originalRequest);
          } catch (refreshError) {
            logger.error("Token refresh failed", refreshError);
            // Redirect to login
            window.location.href = "/login";
            return Promise.reject(refreshError);
          }
        }
      }
      
      // Log error (sanitized)
      logger.error("API Error", {
        status: error.response?.status,
        message: error.message,
        requestId: error.config?.headers?.["X-Request-ID"],
      });
      
      return Promise.reject(error);
    }
  );

  return client;
}
```

## 🎣 Custom Hook for API Calls

```typescript
// src/features/resources/api/resourcesApi.ts

import { AxiosInstance } from "axios";
import { Resource, ResourceRequest } from "../types/resource";

export class ResourcesApi {
  constructor(private client: AxiosInstance) {}

  async list(): Promise<Resource[]> {
    const response = await this.client.get<Resource[]>("/api/v1/resources");
    return response.data;
  }

  async getById(id: string): Promise<Resource> {
    const response = await this.client.get<Resource>(
      `/api/v1/resources/${id}`
    );
    return response.data;
  }

  async create(data: ResourceRequest): Promise<Resource> {
    const response = await this.client.post<Resource>(
      "/api/v1/resources",
      data
    );
    return response.data;
  }

  async update(id: string, data: ResourceRequest): Promise<Resource> {
    const response = await this.client.put<Resource>(
      `/api/v1/resources/${id}`,
      data
    );
    return response.data;
  }

  async delete(id: string): Promise<void> {
    await this.client.delete(`/api/v1/resources/${id}`);
  }
}
```

```typescript
// src/features/resources/hooks/useResources.ts

import { useQuery, useMutation, useQueryClient } from "@tanstack/react-query";
import { useApi } from "../../../shared/hooks/useApi";
import { ResourcesApi } from "../api/resourcesApi";
import { Resource, ResourceRequest } from "../types/resource";
import { toast } from "sonner";

export function useResources() {
  const api = useApi();
  const resourcesApi = new ResourcesApi(api);
  const queryClient = useQueryClient();

  // List resources
  const { data: resources, isLoading, error } = useQuery({
    queryKey: ["resources"],
    queryFn: () => resourcesApi.list(),
    staleTime: 5 * 60 * 1000, // 5 minutes
  });

  // Create resource
  const createMutation = useMutation({
    mutationFn: (data: ResourceRequest) => resourcesApi.create(data),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ["resources"] });
      toast.success("Resource created successfully");
    },
    onError: (error: any) => {
      toast.error(error.response?.data?.message || "Failed to create resource");
    },
  });

  // Update resource
  const updateMutation = useMutation({
    mutationFn: ({ id, data }: { id: string; data: ResourceRequest }) =>
      resourcesApi.update(id, data),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ["resources"] });
      toast.success("Resource updated successfully");
    },
    onError: (error: any) => {
      toast.error(error.response?.data?.message || "Failed to update resource");
    },
  });

  // Delete resource
  const deleteMutation = useMutation({
    mutationFn: (id: string) => resourcesApi.delete(id),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ["resources"] });
      toast.success("Resource deleted successfully");
    },
    onError: (error: any) => {
      toast.error(error.response?.data?.message || "Failed to delete resource");
    },
  });

  return {
    resources: resources || [],
    isLoading,
    error,
    create: createMutation.mutate,
    update: updateMutation.mutate,
    delete: deleteMutation.mutate,
    isCreating: createMutation.isPending,
    isUpdating: updateMutation.isPending,
    isDeleting: deleteMutation.isPending,
  };
}
```

## 📝 Form Validation with Zod

```typescript
// src/features/resources/components/ResourceForm.tsx

import { useForm } from "react-hook-form";
import { zodResolver } from "@hookform/resolvers/zod";
import { z } from "zod";
import { ResourceRequest } from "../types/resource";

// Validation schema
const resourceSchema = z.object({
  name: z.string()
    .min(3, "Name must be at least 3 characters")
    .max(100, "Name must be less than 100 characters")
    .regex(/^[a-zA-Z0-9\s-]+$/, "Name can only contain letters, numbers, spaces, and hyphens"),
  
  description: z.string()
    .min(10, "Description must be at least 10 characters")
    .max(500, "Description must be less than 500 characters"),
  
  amount: z.number()
    .min(0.01, "Amount must be greater than 0")
    .max(9999999.99, "Amount is too large"),
  
  category: z.enum(["type-a", "type-b", "type-c"], {
    errorMap: () => ({ message: "Please select a valid category" }),
  }),
});

type ResourceFormData = z.infer<typeof resourceSchema>;

interface ResourceFormProps {
  initialData?: Partial<ResourceFormData>;
  onSubmit: (data: ResourceRequest) => void;
  isSubmitting?: boolean;
}

export function ResourceForm({ 
  initialData, 
  onSubmit, 
  isSubmitting 
}: ResourceFormProps) {
  const {
    register,
    handleSubmit,
    formState: { errors, isValid },
  } = useForm<ResourceFormData>({
    resolver: zodResolver(resourceSchema),
    defaultValues: initialData,
    mode: "onChange",
  });

  return (
    <form onSubmit={handleSubmit(onSubmit)} className="space-y-4">
      <div>
        <label htmlFor="name" className="block text-sm font-medium">
          Name
        </label>
        <input
          {...register("name")}
          id="name"
          type="text"
          className="mt-1 block w-full rounded-md border px-3 py-2"
          disabled={isSubmitting}
        />
        {errors.name && (
          <p className="mt-1 text-sm text-red-600">{errors.name.message}</p>
        )}
      </div>

      <div>
        <label htmlFor="description" className="block text-sm font-medium">
          Description
        </label>
        <textarea
          {...register("description")}
          id="description"
          rows={3}
          className="mt-1 block w-full rounded-md border px-3 py-2"
          disabled={isSubmitting}
        />
        {errors.description && (
          <p className="mt-1 text-sm text-red-600">
            {errors.description.message}
          </p>
        )}
      </div>

      <div>
        <label htmlFor="amount" className="block text-sm font-medium">
          Amount
        </label>
        <input
          {...register("amount", { valueAsNumber: true })}
          id="amount"
          type="number"
          step="0.01"
          className="mt-1 block w-full rounded-md border px-3 py-2"
          disabled={isSubmitting}
        />
        {errors.amount && (
          <p className="mt-1 text-sm text-red-600">{errors.amount.message}</p>
        )}
      </div>

      <div>
        <label htmlFor="category" className="block text-sm font-medium">
          Category
        </label>
        <select
          {...register("category")}
          id="category"
          className="mt-1 block w-full rounded-md border px-3 py-2"
          disabled={isSubmitting}
        >
          <option value="">Select a category</option>
          <option value="type-a">Type A</option>
          <option value="type-b">Type B</option>
          <option value="type-c">Type C</option>
        </select>
        {errors.category && (
          <p className="mt-1 text-sm text-red-600">
            {errors.category.message}
          </p>
        )}
      </div>

      <button
        type="submit"
        disabled={isSubmitting || !isValid}
        className="w-full rounded-md bg-blue-600 px-4 py-2 text-white 
                   hover:bg-blue-700 disabled:opacity-50"
      >
        {isSubmitting ? "Submitting..." : "Submit"}
      </button>
    </form>
  );
}
```

## 🐳 Dockerfile for Azure Static Web Apps

```dockerfile
# Build stage
FROM node:20-alpine AS build
WORKDIR /app

# Copy package files
COPY package*.json ./

# Install dependencies
RUN npm ci

# Copy source
COPY . .

# Build
RUN npm run build

# Production stage
FROM nginx:alpine
COPY --from=build /app/dist /usr/share/nginx/html

# Security headers
COPY nginx.conf /etc/nginx/nginx.conf

EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

```nginx
# nginx.conf
server {
    listen 80;
    server_name _;
    root /usr/share/nginx/html;
    index index.html;

    # Security headers
    add_header X-Frame-Options "DENY" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header X-XSS-Protection "1; mode=block" always;
    add_header Referrer-Policy "strict-origin-when-cross-origin" always;
    add_header Content-Security-Policy "default-src 'self'; script-src 'self' 'unsafe-inline'; style-src 'self' 'unsafe-inline';" always;

    # SPA routing
    location / {
        try_files $uri $uri/ /index.html;
    }

    # Cache static assets
    location ~* \.(js|css|png|jpg|jpeg|gif|ico|svg|woff|woff2)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
    }
}
```

## 📊 Non-Functional Requirements

| Requirement | Target | Enforcement |
|-------------|--------|-------------|
| **Bundle Size** | < 500KB gzipped | CI fails if exceeded |
| **First Paint** | < 1.5s | Lighthouse CI |
| **Time to Interactive** | < 3.5s | Lighthouse CI |
| **Accessibility** | WCAG 2.1 AA | Axe tests |
| **Security Scan** | Zero CVEs | npm audit in CI |
| **Test Coverage** | > 70% | Vitest enforcement |

## ✅ Pre-Production Checklist

- [ ] Azure AD B2C authentication configured
- [ ] Environment variables for all environments
- [ ] API endpoints tested
- [ ] Form validation implemented
- [ ] Error boundaries in place
- [ ] Loading states for all async operations
- [ ] Accessibility tested (screen reader, keyboard navigation)
- [ ] Security headers configured
- [ ] Dependency vulnerabilities scanned
- [ ] Performance tested (Lighthouse score > 90)
- [ ] Browser compatibility tested
- [ ] Mobile responsive design verified

## 🚀 Getting Started

```bash
# Use the scaffold-frontend skill
@workspace /scaffold-frontend my-new-app \
  --auth=azure-ad-b2c \
  --styling=tailwind
```

## 📚 Related

- [Web API Golden Path](../web-api/index.md)
- [Security Baseline](../../layer1-context/security-baseline/index.md)
