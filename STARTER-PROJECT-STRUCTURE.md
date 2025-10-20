# Starter Project Structure
## Copy this folder structure for your new React + Vite + TypeScript + Supabase project

```
project-name/
│
├── 📚 DOCUMENTATION & CONFIG
│   ├── .cursorrules                    ← Copy from example.cursorrules
│   ├── README.md                       ← Project overview
│   ├── ARCHITECTURE.md                 ← Folder structure & rules
│   ├── CODING_GUIDELINES.md            ← Detailed standards (optional)
│   ├── .gitignore
│   ├── .env.example                    ← Template for env vars
│   ├── .env.local                      ← YOUR actual env vars (don't commit)
│   │
│   ├── 🔧 BUILD & TOOLING
│   ├── package.json
│   ├── package-lock.json
│   ├── vite.config.ts
│   ├── tsconfig.json
│   ├── tsconfig.app.json
│   ├── tsconfig.node.json
│   ├── eslint.config.js
│   ├── tailwind.config.ts
│   ├── postcss.config.js
│   └── components.json                 ← shadcn/ui config
│
├── 📁 SRC - APPLICATION CODE
│   │
│   ├── 📄 CORE
│   │   ├── main.tsx                    ← React entry point
│   │   ├── App.tsx                     ← Root component (router + providers)
│   │   ├── App.css
│   │   ├── index.css                   ← Global styles
│   │   └── vite-env.d.ts
│   │
│   ├── 📄 PAGES
│   │   ├── Index.tsx                   ← Landing page
│   │   ├── NotFound.tsx                ← 404 page
│   │   ├── StudentPortal.tsx           ← Student dashboard
│   │   ├── TrainerDashboard.tsx        ← Trainer dashboard
│   │   └── AdminPanel.tsx              ← Admin dashboard
│   │
│   ├── 🧩 COMPONENTS
│   │   │
│   │   ├── student/
│   │   │   ├── CourseList.tsx
│   │   │   ├── CourseCard.tsx
│   │   │   ├── EnrollmentFlow.tsx
│   │   │   ├── StudentProgress.tsx
│   │   │   └── StudentSidebar.tsx
│   │   │
│   │   ├── trainer/
│   │   │   ├── SessionsTable.tsx
│   │   │   ├── CreateSessionForm.tsx
│   │   │   ├── StudentsList.tsx
│   │   │   └── TrainerSidebar.tsx
│   │   │
│   │   ├── admin/
│   │   │   ├── AnalyticsDashboard.tsx
│   │   │   ├── UsersTable.tsx
│   │   │   ├── UserFormModal.tsx
│   │   │   └── AdminSidebar.tsx
│   │   │
│   │   └── ui/                         ← shadcn/ui base components
│   │       ├── button.tsx
│   │       ├── input.tsx
│   │       ├── modal.tsx
│   │       ├── card.tsx
│   │       ├── table.tsx
│   │       ├── form.tsx
│   │       ├── dropdown-menu.tsx
│   │       ├── alert.tsx
│   │       ├── badge.tsx
│   │       ├── toast.tsx
│   │       └── ... (40+ more)
│   │
│   ├── 🪝 HOOKS - BUSINESS LOGIC
│   │   │
│   │   ├── student/
│   │   │   ├── useStudentData.ts       ← Fetch student profile
│   │   │   ├── useStudentCourses.ts    ← Fetch enrolled courses
│   │   │   ├── useEnrollment.ts        ← Enroll in course
│   │   │   ├── useStudentProgress.ts   ← Track progress
│   │   │   └── usePRDGeneration.ts     ← AI PRD generation
│   │   │
│   │   ├── trainer/
│   │   │   ├── useTrainerData.ts
│   │   │   ├── useTrainerSessions.ts
│   │   │   ├── useSessionManagement.ts
│   │   │   ├── useBilling.ts
│   │   │   └── useCertification.ts
│   │   │
│   │   ├── admin/
│   │   │   ├── useAnalytics.ts
│   │   │   ├── useUserManagement.ts
│   │   │   ├── useReporting.ts
│   │   │   └── useRevenue.ts
│   │   │
│   │   └── shared/
│   │       ├── useAuth.ts              ← Authentication state
│   │       ├── useLocalStorage.ts      ← LocalStorage wrapper
│   │       ├── useDebounce.ts          ← Debounce utility
│   │       └── useFetch.ts             ← Generic fetch wrapper
│   │
│   ├── 🛠️ SERVICES - UTILITIES
│   │   │
│   │   ├── authService.ts              ← Login/logout/JWT refresh
│   │   ├── courseService.ts            ← Course CRUD operations
│   │   ├── studentService.ts           ← Student operations
│   │   ├── trainerService.ts           ← Trainer operations
│   │   ├── apiService.ts               ← Centralized API wrapper
│   │   │
│   │   └── utils/
│   │       ├── formatters.ts           ← Date, currency formatting
│   │       ├── validators.ts           ← Email, phone validation
│   │       ├── calculations.ts         ← Math operations
│   │       └── helpers.ts              ← General utilities
│   │
│   ├── 📋 TYPES - TYPESCRIPT DEFINITIONS
│   │   │
│   │   ├── database.types.ts           ← Auto-generated from Supabase
│   │   ├── Student.types.ts
│   │   ├── Course.types.ts
│   │   ├── Trainer.types.ts
│   │   ├── Certificate.types.ts
│   │   ├── Session.types.ts
│   │   └── common.types.ts             ← Shared types (API responses, etc)
│   │
│   ├── 🔄 CONTEXTS - GLOBAL STATE
│   │   │
│   │   ├── AuthContext.tsx             ← Authentication state
│   │   ├── UserContext.tsx             ← Current user data
│   │   └── AppContext.tsx              ← App settings
│   │
│   ├── 🔗 LIB - EXTERNAL LIBRARY SETUP
│   │   │
│   │   └── supabase.ts                 ← Supabase client initialization
│   │
│   └── 🎨 ASSETS (optional)
│       ├── images/
│       ├── icons/
│       └── fonts/
│
├── 📱 PUBLIC - STATIC ASSETS
│   ├── favicon.ico
│   ├── robots.txt
│   └── placeholder.svg
│
├── 🚀 SUPABASE - BACKEND
│   │
│   └── functions/
│       ├── generate-prd/
│       │   ├── index.ts                ← Main function code
│       │   └── deno.json               ← Dependencies
│       │
│       ├── generate-frontend/
│       │   ├── index.ts
│       │   └── deno.json
│       │
│       └── process-payment/
│           ├── index.ts
│           └── deno.json
│
└── 📦 BUILD OUTPUT (generated)
    └── dist/                           ← Production build (do not commit)
```

---

## Step-by-Step Setup Instructions

### 1. Create Project
```bash
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
```

### 2. Copy Configuration Files
Copy these files from example outputs:
- `.cursorrules` → project root
- `example.cursorrules` content

### 3. Create Folder Structure
```bash
mkdir -p src/pages
mkdir -p src/components/{student,trainer,admin,ui}
mkdir -p src/hooks/{student,trainer,admin,shared}
mkdir -p src/services/utils
mkdir -p src/types
mkdir -p src/contexts
mkdir -p src/lib
mkdir -p supabase/functions
```

### 4. Install Dependencies
```bash
npm install @supabase/supabase-js
npm install @supabase/auth-ui-react @supabase/auth-ui-shared
npm install tailwindcss postcss autoprefixer
npm install -D shadcn-ui
npm install react-router-dom
npm install zustand  # optional state management
```

### 5. Setup Supabase
```bash
npm install -D supabase
supabase init
supabase login
supabase link --project-ref your-project-ref
```

### 6. Update TypeScript Config
```json
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@/*": ["./src/*"]
    },
    "strict": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true
  }
}
```

### 7. Create Core Files

**src/main.tsx**
```typescript
import React from 'react'
import ReactDOM from 'react-dom/client'
import App from './App.tsx'
import './index.css'

ReactDOM.createRoot(document.getElementById('root')!).render(
  <React.StrictMode>
    <App />
  </React.StrictMode>,
)
```

**src/App.tsx**
```typescript
import { BrowserRouter, Routes, Route } from 'react-router-dom'
import { AuthProvider } from '@/contexts/AuthContext'
import { Index } from '@/pages/Index'
import { StudentPortal } from '@/pages/StudentPortal'
import { NotFound } from '@/pages/NotFound'

export default function App() {
  return (
    <AuthProvider>
      <BrowserRouter>
        <Routes>
          <Route path="/" element={<Index />} />
          <Route path="/student" element={<StudentPortal />} />
          <Route path="*" element={<NotFound />} />
        </Routes>
      </BrowserRouter>
    </AuthProvider>
  )
}
```

**src/lib/supabase.ts**
```typescript
import { createClient } from '@supabase/supabase-js'

const supabaseUrl = import.meta.env.VITE_SUPABASE_URL
const supabaseKey = import.meta.env.VITE_SUPABASE_ANON_KEY

if (!supabaseUrl || !supabaseKey) {
  throw new Error('Missing Supabase environment variables')
}

export const supabase = createClient(supabaseUrl, supabaseKey)
```

**src/contexts/AuthContext.tsx** (example)
```typescript
import { createContext, useContext, useEffect, useState } from 'react'
import type { User } from '@supabase/supabase-js'
import { supabase } from '@/lib/supabase'

interface AuthContextType {
  user: User | null
  loading: boolean
}

const AuthContext = createContext<AuthContextType | undefined>(undefined)

export function AuthProvider({ children }: { children: React.ReactNode }) {
  const [user, setUser] = useState<User | null>(null)
  const [loading, setLoading] = useState(true)

  useEffect(() => {
    supabase.auth.getSession().then(({ data: { session } }) => {
      setUser(session?.user ?? null)
      setLoading(false)
    })

    const { data: { subscription } } = supabase.auth.onAuthStateChange(
      (_event, session) => {
        setUser(session?.user ?? null)
      }
    )

    return () => subscription?.unsubscribe()
  }, [])

  return (
    <AuthContext.Provider value={{ user, loading }}>
      {children}
    </AuthContext.Provider>
  )
}

export function useAuth() {
  const context = useContext(AuthContext)
  if (!context) throw new Error('useAuth must be used within AuthProvider')
  return context
}
```

### 8. Create README.md
```markdown
# My App

## Quick Start

### Prerequisites
- Node.js 18+
- Supabase account

### Setup
```bash
npm install
cp .env.example .env.local
# Edit .env.local with your Supabase credentials
npm run dev
```

### Commands
```bash
npm run dev          # Start dev server
npm run build        # Build for production
npm run lint         # Check code quality
npm run type-check   # Check TypeScript
```

## Architecture
See ARCHITECTURE.md for folder structure.

## Coding Guidelines
See .cursorrules for AI guidelines.
```

### 9. Create ARCHITECTURE.md
(Copy from the ARCHITECTURE.md template in outputs)

### 10. Create .cursorrules
(Copy from example.cursorrules in outputs)

---

## Environment Variables Template

**.env.example**
```env
# Supabase
VITE_SUPABASE_URL=https://your-project.supabase.co
VITE_SUPABASE_ANON_KEY=your-public-key

# Optional - Edge Function Secrets (set in Supabase dashboard)
# OPENAI_API_KEY=sk-...
# STRIPE_API_KEY=sk_...
```

**.env.local** (YOUR local copy - never commit)
```env
VITE_SUPABASE_URL=https://abc123.supabase.co
VITE_SUPABASE_ANON_KEY=eyJhbGc...
```

---

## Common Commands

```bash
# Development
npm run dev

# Type checking
npm run type-check

# Linting
npm run lint
npm run lint -- --fix

# Build
npm run build

# Preview production build
npm run preview

# Supabase
supabase functions list
supabase functions deploy generate-prd
supabase db push
```

---

## Next Steps After Setup

1. ✅ Create first feature folder structure
2. ✅ Create types for that feature
3. ✅ Create service layer for API calls
4. ✅ Create custom hook
5. ✅ Create component
6. ✅ Add to page
7. ✅ Test locally

---

## Tips for Success

1. **Always refer to .cursorrules** when prompting Cursor
2. **Keep files small** (under 200 lines)
3. **Separate logic from UI** always
4. **Update ARCHITECTURE.md** when structure changes
5. **Use TypeScript everywhere** - no `any` types
6. **Handle errors** in all async operations
7. **Test locally** before pushing to production

---

## Troubleshooting

### Cursor doesn't follow guidelines
→ Make sure .cursorrules file exists and is in project root

### Type errors everywhere
→ Run `npm run type-check` to find issues

### Components are too big
→ Split into smaller components, extract logic to hooks

### Supabase can't connect
→ Check .env.local has correct URL and key
→ Check RLS policies allow your query

---

*Ready to build with AI! Happy coding!*
