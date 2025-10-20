# AI-Assisted Development - Quick Reference Card

## Before You Start Coding

✅ Copy `.cursorrules` to project root
✅ Update ARCHITECTURE.md with your structure  
✅ Install dependencies
✅ Configure TypeScript paths

---

## Architecture at a Glance

```
Pages (UI orchestration)
    ↓
Components (JSX only)
    ↓
Hooks (state + logic)
    ↓
Services (Supabase + utilities)
```

---

## File Organization Quick Map

| What | Where | Max Lines | Language |
|------|-------|-----------|----------|
| Pages | `src/pages/` | 300 | .tsx |
| Components | `src/components/[role]/` | 150 | .tsx |
| Hooks | `src/hooks/[role]/` | 100 | .ts |
| Services | `src/services/` | 200 | .ts |
| Types | `src/types/` | - | .ts |
| Contexts | `src/contexts/` | 150 | .tsx |

---

## Component Template

```typescript
import type { ComponentProps } from '@/types/Component.types';

interface Props {
  title: string;
  onAction: () => void;
}

export function MyComponent({ title, onAction }: Props) {
  return (
    <button onClick={onAction}>
      {title}
    </button>
  );
}
```

**Rules:**
- ✅ Props interface
- ✅ JSX only
- ✅ No logic
- ✅ No Supabase

---

## Hook Template

```typescript
import { useEffect, useState } from 'react';
import type { MyData } from '@/types/MyData.types';
import { myService } from '@/services/myService';

export function useMyData() {
  const [data, setData] = useState<MyData | null>(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);

  useEffect(() => {
    const fetch = async () => {
      try {
        const result = await myService.getData();
        setData(result);
      } catch (err) {
        setError(err.message);
      } finally {
        setLoading(false);
      }
    };
    fetch();
  }, []);

  return { data, loading, error };
}
```

**Rules:**
- ✅ Return `{ data, loading, error }`
- ✅ Always handle errors
- ✅ Start with "use"
- ✅ Under 100 lines

---

## Service Template

```typescript
import { supabase } from '@/lib/supabase';
import type { MyData } from '@/types/MyData.types';

export const myService = {
  async getData(): Promise<MyData> {
    const { data, error } = await supabase
      .from('table_name')
      .select();
    
    if (error) throw new Error(error.message);
    return data;
  },
};
```

**Rules:**
- ✅ Export object with functions
- ✅ All error handling here
- ✅ All Supabase calls here
- ✅ No React imports

---

## Type Template

```typescript
// src/types/MyData.types.ts

export type MyData = {
  id: string;
  name: string;
  email: string;
  created_at: string;
};

export type CreateMyDataInput = Omit<MyData, 'id' | 'created_at'>;
```

**Rules:**
- ✅ Always define
- ✅ Always export
- ✅ Use Omit/Pick for derived types

---

## Import Order (Always!)

```typescript
// 1. React/libraries
import { useState, useEffect } from 'react';

// 2. Types
import type { MyData } from '@/types/MyData.types';

// 3. Hooks
import { useMyData } from '@/hooks/useMyData';

// 4. Services
import { myService } from '@/services/myService';

// 5. Components
import { MyComponent } from '@/components/MyComponent';

// 6. UI components
import { Button } from '@/components/ui/button';
```

---

## Common Patterns

### Fetch Data Pattern
```typescript
const { data, loading, error } = useMyData();

if (loading) return <div>Loading...</div>;
if (error) return <div>Error: {error}</div>;

return <div>{/* render data */}</div>;
```

### Form Submission Pattern
```typescript
const [loading, setLoading] = useState(false);
const [error, setError] = useState<string | null>(null);

const handleSubmit = async (formData) => {
  try {
    setLoading(true);
    setError(null);
    await myService.create(formData);
    // Success - redirect or show toast
  } catch (err) {
    setError(err.message);
  } finally {
    setLoading(false);
  }
};
```

### Multiple Hooks Pattern
```typescript
export function Dashboard() {
  const { courses } = useStudentCourses();
  const { progress } = useStudentProgress();
  const { user } = useAuth();
  
  return (
    <div>
      <CourseSection courses={courses} />
      <ProgressSection progress={progress} />
    </div>
  );
}
```

---

## ❌ Common Mistakes to Avoid

### Mistake 1: Logic in Component
```typescript
// ❌ WRONG
export function StudentCourses() {
  const [courses, setCourses] = useState([]);
  useEffect(() => {
    supabase.from('courses').select().then(data => setCourses(data));
  }, []);
  return <div>{courses.map(...)}</div>;
}

// ✅ CORRECT
const { courses } = useStudentCourses();
return <div>{courses.map(...)}</div>;
```

### Mistake 2: Component Importing Hook Importing Component
```typescript
// ❌ WRONG
// hook.ts imports Component.tsx
// Component.tsx imports hook.ts (circular!)

// ✅ CORRECT
// Only components import hooks, never the reverse
```

### Mistake 3: Using `any`
```typescript
// ❌ WRONG
const data: any = fetchData();

// ✅ CORRECT
const data: MyData = fetchData();
```

### Mistake 4: No Error Handling
```typescript
// ❌ WRONG
const { data } = await supabase.from('table').select();

// ✅ CORRECT
const { data, error } = await supabase.from('table').select();
if (error) throw new Error(error.message);
```

### Mistake 5: Mixing Contexts
```typescript
// ❌ WRONG - Too many contexts
<Auth><User><Theme><Notification><Settings>

// ✅ CORRECT - Max 3-4, combine related
<Auth><App>
```

---

## Cursor Prompt Checklist

Before asking Cursor to build a feature, have:

- [ ] Clear requirements (what, not how)
- [ ] Specific folder paths
- [ ] Type definitions
- [ ] Reference to .cursorrules
- [ ] Example of similar code (if exists)

**Template:**
```
I want to build [FEATURE].

Requirements: [list them]

Folders:
- Component: src/components/[role]/[Feature].tsx
- Hook: src/hooks/[role]/use[Feature].ts
- Service: src/services/[feature]Service.ts
- Types: src/types/[Feature].types.ts

Follow .cursorrules.
```

---

## Commands You'll Need

```bash
# Start dev server
npm run dev

# Type check
npm run type-check

# Lint
npm run lint

# Fix lint issues
npm run lint -- --fix

# Build
npm run build

# Preview build
npm run preview
```

---

## TypeScript Quick Rules

| Do | Don't |
|----|-------|
| `const` | `var` |
| Arrow functions | `function` |
| Interfaces for objects | `any` |
| `satisfies` keyword | Type casting |
| `Omit<Type, 'field'>` | Manual types |
| Template literals | String concat |
| Destructuring | Direct access |

---

## React Quick Rules

| Do | Don't |
|----|-------|
| `useState` | Mutable state |
| `useEffect` | Direct DOM |
| Functional components | Class components |
| `React.memo` | Inline functions |
| Props drilling alternatives | 5+ levels deep |
| Custom hooks | Logic in components |

---

## File Size Red Flags

- **Component > 150 lines?** → Split it up
- **Hook > 100 lines?** → Extract logic to service
- **Service > 200 lines?** → Multiple services
- **Page > 300 lines?** → Too many responsibilities

---

## Quick Debugging Checklist

**Code not working?**

1. ✅ Check browser console for errors
2. ✅ Run `npm run type-check`
3. ✅ Run `npm run lint`
4. ✅ Check `.env.local` has correct values
5. ✅ Check RLS policies in Supabase
6. ✅ Check Supabase network tab (API working?)
7. ✅ Add console.logs in hook
8. ✅ Check prop types match interface

---

## When to Use What

**useState?** → Local component state only

**useEffect?** → Data fetching, subscriptions

**useContext?** → Global state (auth, user, settings)

**Custom Hook?** → Shared logic between components

**Service?** → All Supabase calls, utilities

---

## Performance Checklist

- [ ] No new objects/arrays created in render
- [ ] useCallback for callbacks passed to children
- [ ] React.memo for expensive components
- [ ] useMemo for expensive calculations (rare)
- [ ] No fetch without dependencies
- [ ] No unnecessary re-renders

---

## Commit Checklist

Before `git push`:

- [ ] No `any` types
- [ ] No `console.log` statements
- [ ] All files under size limits
- [ ] TypeScript passes (`npm run type-check`)
- [ ] Linter passes (`npm run lint`)
- [ ] Updated relevant .md files
- [ ] Tested locally

---

## Emergency: Code Review Checklist

Use this when your code doesn't feel right:

- [ ] Is logic in a component? → Move to hook
- [ ] Is a hook too big? → Split or extract to service
- [ ] No types? → Add them
- [ ] Missing error handling? → Add try/catch
- [ ] File too big? → Split it
- [ ] Props not typed? → Add interface

---

## When to Ask Cursor

✅ "Extract this logic to a hook"
✅ "Create component following our architecture"
✅ "Add error handling to this function"
✅ "Write tests for this hook"
✅ "Refactor this component to be smaller"

❌ "Just build me a dashboard"
❌ "Make this look good"
❌ "Fix my code" (be specific)

---

## Helpful Reminders

> "In vibe coding, clean architecture beats speed."

> "Components are dumb, hooks are smart."

> "When in doubt, make the file smaller."

> "Types are documentation."

> "Service layer is your friend."

> "Test your hooks, not your components."

---

## One Page Reference

```
PAGES (what page)
  ↓
COMPONENTS (show what)
  ↓
HOOKS (get & manage what)
  ↓
SERVICES (talk to Supabase)
  ↓
TYPES (describe what)
```

Follow this flow every time. Never break it.

---

**Print this. Keep it nearby. Reference often. Success guaranteed.**

*Last Updated: January 2025*
