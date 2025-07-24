# NextJS Implementation Mode Guide

This guide provides specific patterns and best practices for implementing features in the ISE Center NextJS application.

## Tech Stack & Architecture

### Core Technologies
- **Framework**: Next.js 15 with App Router and TypeScript 5
- **Styling**: Tailwind CSS 4 with shadcn/ui components
- **State Management**: Zustand for global state, TanStack Query for server state
- **Authentication**: Multi-tier auth system (admin, teacher, student)
- **Database**: Supabase PostgreSQL with TypeScript types
- **Forms**: React Hook Form with Zod validation
- **Testing**: Vitest (unit), Playwright (E2E)

### Project Structure
```
web-app/src/
├── app/                    # Next.js App Router pages
├── components/             # Reusable UI components
├── lib/                   # Core libraries and utilities
│   ├── auth/              # Authentication system
│   └── supabase/          # Supabase client configuration
├── hooks/                 # Custom React hooks
├── contexts/              # React contexts
└── types/                 # TypeScript type definitions
```

## Development Workflow

### Pre-Implementation Setup
1. **Navigate to web-app directory**:
   ```bash
   cd web-app
   ```

2. **Install dependencies if needed**:
   ```bash
   yarn install
   ```

3. **Start development server**:
   ```bash
   yarn dev
   ```

### Code Quality Commands
Always run these before committing:
```bash
yarn lint                   # ESLint checks
yarn type-check            # TypeScript validation
yarn format                # Prettier formatting
yarn test                  # Unit tests
```

## Implementation Patterns

### 1. Authentication Integration

For features requiring authentication, follow these patterns:

```typescript
// Import appropriate auth service
import { adminAuthService } from '@/lib/auth/admin-auth-service'
import { studentAuthService } from '@/lib/auth/student-auth-service'
import { teacherAuthService } from '@/lib/auth/teacher-auth-service'

// Use auth guards for route protection
import { AuthGuard } from '@/components/auth/auth-guard'
import { PermissionGuard } from '@/components/auth/permission-guard'

// Component with auth protection
export default function ProtectedPage() {
  return (
    <AuthGuard requiredRole="admin">
      <PermissionGuard permission="manage_users">
        {/* Your component content */}
      </PermissionGuard>
    </AuthGuard>
  )
}
```

### 2. Component Structure

Follow this pattern for new components:

```typescript
// components/feature/ComponentName.tsx
import { cn } from '@/lib/utils'
import { Button } from '@/components/ui/button'

interface ComponentNameProps {
  className?: string
  // Define props with proper TypeScript types
}

export function ComponentName({ 
  className,
  ...props 
}: ComponentNameProps) {
  return (
    <div className={cn("default-classes", className)}>
      {/* Component implementation */}
    </div>
  )
}
```

### 3. Database Operations

Use Supabase client with proper TypeScript types:

```typescript
import { createClient } from '@/lib/supabase/client'
import type { Database } from '@/types/supabase'

// For client components
const supabase = createClient()

// For server components/actions
import { createServerClient } from '@/lib/supabase/server'

// Example query with proper typing
async function getStudents() {
  const { data, error } = await supabase
    .from('students')
    .select('*')
    .returns<Database['public']['Tables']['students']['Row'][]>()
  
  if (error) throw error
  return data
}
```

### 4. Form Handling

Use React Hook Form with Zod validation:

```typescript
import { useForm } from 'react-hook-form'
import { zodResolver } from '@hookform/resolvers/zod'
import { z } from 'zod'
import { Form, FormControl, FormField, FormItem, FormLabel } from '@/components/ui/form'

const formSchema = z.object({
  name: z.string().min(1, 'Name is required'),
  email: z.string().email('Invalid email'),
})

type FormData = z.infer<typeof formSchema>

export function MyForm() {
  const form = useForm<FormData>({
    resolver: zodResolver(formSchema),
    defaultValues: {
      name: '',
      email: '',
    },
  })

  const onSubmit = async (data: FormData) => {
    // Handle form submission
  }

  return (
    <Form {...form}>
      <form onSubmit={form.handleSubmit(onSubmit)}>
        {/* Form fields */}
      </form>
    </Form>
  )
}
```

### 5. State Management

Use Zustand for global state:

```typescript
// stores/useFeatureStore.ts
import { create } from 'zustand'
import { devtools } from 'zustand/middleware'

interface FeatureState {
  items: Item[]
  loading: boolean
  setItems: (items: Item[]) => void
  setLoading: (loading: boolean) => void
}

export const useFeatureStore = create<FeatureState>()(
  devtools(
    (set) => ({
      items: [],
      loading: false,
      setItems: (items) => set({ items }),
      setLoading: (loading) => set({ loading }),
    }),
    { name: 'feature-store' }
  )
)
```

### 6. API Routes

For API routes in the app directory:

```typescript
// app/api/feature/route.ts
import { NextRequest, NextResponse } from 'next/server'
import { createServerClient } from '@/lib/supabase/server'

export async function GET(request: NextRequest) {
  try {
    const supabase = createServerClient()
    
    // Your API logic here
    const { data, error } = await supabase
      .from('table_name')
      .select('*')
    
    if (error) throw error
    
    return NextResponse.json({ data })
  } catch (error) {
    return NextResponse.json(
      { error: 'Internal server error' },
      { status: 500 }
    )
  }
}
```

## Language & Internationalization (i18n)

The project supports English and Vietnamese with a robust i18n system.

### Setting Up i18n in Components

```typescript
// Client component with i18n
'use client';

import { useI18n } from '@/lib/i18n/provider';

export function MyComponent() {
  const { t, locale } = useI18n();

  return (
    <div>
      <h1>{t('common.welcome')}</h1>
      <p>{t('auth.loginTitle')}</p>
      <p>{t('common.hello', { name: 'John' })}</p>
    </div>
  );
}
```

### Server Component Translations

```typescript
// Server component with i18n
import { createServerT } from '@/lib/i18n/translations';
import { Locale } from '@/lib/i18n/provider';

interface PageProps {
  params: {
    locale: Locale;
  };
}

export default function ServerPage({ params }: PageProps) {
  const t = createServerT(params.locale);

  return (
    <div>
      <h1>{t('common.welcome')}</h1>
      <p>{t('auth.loginTitle')}</p>
    </div>
  );
}
```

### Translation File Structure

Translation files are located in `public/locales/{locale}/{namespace}.json`:

```json
// public/locales/en/common.json
{
  "nav": {
    "home": "Home",
    "dashboard": "Dashboard"
  },
  "auth": {
    "email": "Email",
    "password": "Password",
    "loginTitle": "Welcome Back"
  },
  "common": {
    "welcome": "Welcome",
    "hello": "Hello {{name}}"
  }
}
```

### Adding New Translations

1. **Add to translation files**:
   ```json
   // public/locales/en/common.json
   {
     "newFeature": {
       "title": "New Feature",
       "description": "Feature description"
     }
   }
   ```

2. **Update translations.ts if needed**:
   ```typescript
   // src/lib/i18n/translations.ts
   const enNewFeature = require('../../../public/locales/en/newFeature.json');
   const viNewFeature = require('../../../public/locales/vi/newFeature.json');
   
   const translations: Record<Locale, Translations> = {
     en: {
       ...enCommon,
       ...enNewFeature,
     },
     vi: {
       ...viCommon,
       ...viNewFeature,
     },
   };
   ```

3. **Use in components**:
   ```typescript
   const { t } = useI18n();
   return <h1>{t('newFeature.title')}</h1>;
   ```

### Language Switcher Integration

```typescript
import { LanguageSwitcher } from '@/components/ui/language-switcher';

export function Header() {
  return (
    <header className="flex items-center justify-between">
      <div>Logo</div>
      <LanguageSwitcher />
    </header>
  );
}
```

## Theme System

The project uses a CSS custom properties-based theme system with light/dark mode support.

### Theme Structure

Themes are defined in `globals.css` with CSS custom properties:

```css
/* Light theme (default) */
:root {
  --background: oklch(1 0 0);
  --foreground: oklch(0.145 0 0);
  --primary: oklch(0.205 0 0);
  --primary-foreground: oklch(0.985 0 0);
  /* ... more variables */
}

/* Dark theme */
.dark {
  --background: oklch(0.145 0 0);
  --foreground: oklch(0.985 0 0);
  --primary: oklch(0.922 0 0);
  --primary-foreground: oklch(0.205 0 0);
  /* ... more variables */
}
```

### Using Theme Colors

```typescript
// Use theme colors with Tailwind classes
export function ThemedComponent() {
  return (
    <div className="bg-background text-foreground">
      <button className="bg-primary text-primary-foreground">
        Primary Button
      </button>
      <div className="bg-card text-card-foreground border border-border">
        Card content
      </div>
    </div>
  );
}
```

### Theme Toggle Integration

```typescript
import { ThemeToggle } from '@/components/theme-toggle';

export function Header() {
  return (
    <header className="flex items-center justify-between">
      <div>Logo</div>
      <div className="flex items-center gap-4">
        <LanguageSwitcher />
        <ThemeToggle />
      </div>
    </header>
  );
}
```

### Custom Theme Colors

The project includes additional semantic colors:

```css
/* Additional semantic colors */
:root {
  --success: oklch(0.55 0.15 142);
  --success-foreground: oklch(1 0 0);
  --warning: oklch(0.75 0.15 85);
  --warning-foreground: oklch(0.15 0 0);
  --info: oklch(0.65 0.15 220);
  --info-foreground: oklch(1 0 0);
  --destructive: oklch(0.577 0.245 27.325);
  --destructive-foreground: oklch(1.0000 0 0);
}
```

Use these in components:

```typescript
export function StatusMessage({ type, children }) {
  const colorClasses = {
    success: 'bg-success text-success-foreground',
    warning: 'bg-warning text-warning-foreground',
    info: 'bg-info text-info-foreground',
    error: 'bg-destructive text-destructive-foreground',
  };

  return (
    <div className={`p-4 rounded-md ${colorClasses[type]}`}>
      {children}
    </div>
  );
}
```

### Font System

The project uses Geist fonts configured in the theme:

```css
:root {
  --font-sans: var(--font-geist-sans);
  --font-mono: var(--font-geist-mono);
  --font-serif: ui-serif, Georgia, Cambria, "Times New Roman", Times, serif;
}
```

Use with Tailwind classes:

```typescript
<div className="font-sans">Regular text</div>
<code className="font-mono">Code text</code>
<h1 className="font-serif">Serif heading</h1>
```

### Responsive Design with Theme

```typescript
export function ResponsiveCard() {
  return (
    <div className="bg-card text-card-foreground border border-border rounded-lg p-4 md:p-6 shadow-sm hover:shadow-md transition-shadow">
      <h2 className="text-lg md:text-xl font-semibold mb-2">Card Title</h2>
      <p className="text-muted-foreground">Card description</p>
    </div>
  );
}
```

### Dark Mode Best Practices

1. **Test both themes**: Always test components in both light and dark modes
2. **Use semantic colors**: Use theme variables instead of hardcoded colors
3. **Consider images**: Provide dark mode alternatives for images when needed
4. **Accessibility**: Ensure sufficient contrast in both themes

```typescript
// Good: Uses theme variables
<div className="bg-background text-foreground border-border">

// Bad: Hardcoded colors
<div className="bg-white text-black border-gray-200">
```

## Testing Patterns

### Unit Tests (Vitest)
```typescript
// __tests__/ComponentName.test.tsx
import { describe, it, expect, vi } from 'vitest'
import { render, screen } from '@testing-library/react'
import { ComponentName } from '@/components/feature/ComponentName'

describe('ComponentName', () => {
  it('renders correctly', () => {
    render(<ComponentName />)
    expect(screen.getByText('Expected text')).toBeInTheDocument()
  })
})
```

### E2E Tests (Playwright)
```typescript
// e2e/feature.spec.ts
import { test, expect } from '@playwright/test'

test('feature workflow', async ({ page }) => {
  await page.goto('/feature')
  await expect(page.getByText('Feature Title')).toBeVisible()
  // Test user interactions
})
```

## File Organization Rules

### Page Components
- Place in `app/` directory following App Router conventions
- Use `page.tsx` for route pages
- Use `layout.tsx` for shared layouts
- Use `loading.tsx` for loading states
- Use `error.tsx` for error boundaries

### Reusable Components
- Place in `components/` directory
- Group by feature: `components/students/`, `components/classes/`
- Use `index.ts` files for clean exports

### Utilities and Hooks
- Place utilities in `lib/` directory
- Place custom hooks in `hooks/` directory
- Use descriptive names: `useStudentData`, `formatDate`

### Types
- Place in `types/` directory
- Generate Supabase types: `yarn generate-types`
- Create domain-specific type files: `types/student.ts`

## Security Best Practices

### 1. Authentication Checks
Always implement proper auth checks:
```typescript
// Server action example
import { createServerClient } from '@/lib/supabase/server'
import { redirect } from 'next/navigation'

export async function protectedAction() {
  const supabase = createServerClient()
  const { data: { user } } = await supabase.auth.getUser()
  
  if (!user) {
    redirect('/login')
  }
  
  // Proceed with protected logic
}
```

### 2. Input Validation
Always validate inputs with Zod:
```typescript
import { z } from 'zod'

const inputSchema = z.object({
  studentId: z.string().uuid(),
  name: z.string().min(1).max(100),
})

// Validate before processing
const validatedInput = inputSchema.parse(input)
```

### 3. Error Handling
Implement comprehensive error handling:
```typescript
try {
  // Operation
} catch (error) {
  console.error('Operation failed:', error)
  // Don't expose internal errors to users
  throw new Error('Operation failed')
}
```

## Performance Considerations

### 1. Code Splitting
Use dynamic imports for large components:
```typescript
import dynamic from 'next/dynamic'

const HeavyComponent = dynamic(
  () => import('@/components/HeavyComponent'),
  { loading: () => <p>Loading...</p> }
)
```

### 2. Database Queries
- Use Supabase RLS policies
- Implement pagination for large datasets
- Use select() to limit returned fields
- Consider using views for complex queries

### 3. Caching
- Use Next.js built-in caching
- Implement TanStack Query for client-side caching
- Use Supabase real-time subscriptions judiciously

## Quality Checklist

Before completing implementation:
- [ ] TypeScript strict mode compliance
- [ ] ESLint rules pass (`yarn lint`)
- [ ] Prettier formatting applied (`yarn format`)
- [ ] Type checking passes (`yarn type-check`)
- [ ] Unit tests written and passing (`yarn test`)
- [ ] Authentication properly implemented
- [ ] Database operations use proper types
- [ ] Error handling implemented
- [ ] Loading states handled
- [ ] Responsive design implemented
- [ ] Accessibility considerations addressed
- [ ] i18n support added for user-facing text
- [ ] Theme support (light/dark mode) tested

## Common Patterns to Follow

### 1. Error Boundaries
Implement error boundaries for robust UX:
```typescript
// components/ErrorBoundary.tsx
'use client'

import { ErrorBoundary as ReactErrorBoundary } from 'react-error-boundary'

function ErrorFallback({ error, resetErrorBoundary }) {
  return (
    <div className="error-boundary">
      <h2>Something went wrong:</h2>
      <pre>{error.message}</pre>
      <button onClick={resetErrorBoundary}>Try again</button>
    </div>
  )
}

export function ErrorBoundary({ children }) {
  return (
    <ReactErrorBoundary FallbackComponent={ErrorFallback}>
      {children}
    </ReactErrorBoundary>
  )
}
```

### 2. Loading States
Always provide loading feedback:
```typescript
export function DataComponent() {
  const { data, isLoading, error } = useQuery({
    queryKey: ['data'],
    queryFn: fetchData,
  })

  if (isLoading) return <LoadingSpinner />
  if (error) return <ErrorMessage error={error} />
  
  return <DataDisplay data={data} />
}
```

### 3. Responsive Design
Use Tailwind responsive classes:
```typescript
<div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3">
  {/* Responsive grid */}
</div>
```

## Integration Points

### With Authentication System
- Always use appropriate auth service
- Implement proper route guards
- Add audit logging for sensitive operations

### With Database
- Use generated TypeScript types
- Follow RLS policies
- Implement proper error handling

### With UI Components
- Use shadcn/ui components as base
- Maintain consistent design system
- Follow accessibility guidelines

### With i18n System
- Use `useI18n()` hook in client components
- Use `createServerT()` for server components
- Include language switcher in layouts
- Test all text content in both languages

### With Theme System
- Use CSS custom properties from `globals.css`
- Include theme toggle in layouts
- Test components in both light and dark modes
- Use semantic color classes

This guide ensures consistency with the ISE Center project's architecture and coding standards while providing clear patterns for common NextJS development tasks.