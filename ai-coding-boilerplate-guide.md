# Complete Boilerplate Guide: Coding with AI Assistants
## What It Is, How to Use It, When to Use It, and Why

---

## 📋 Table of Contents
1. [Project Structure Foundation](#project-structure-foundation)
2. [Documentation & Guidelines](#documentation--guidelines)
3. [Configuration Files](#configuration-files)
4. [Frontend Architecture](#frontend-architecture)
5. [Backend Architecture](#backend-architecture)
6. [How to Prompt Cursor Effectively](#how-to-prompt-cursor-effectively)
7. [Common Mistakes & How to Avoid Them](#common-mistakes--how-to-avoid-them)

---

# PROJECT STRUCTURE FOUNDATION

## 1️⃣ Root Directory `.cursorrules` File

### What It Is
A special configuration file that tells Cursor (your AI IDE) what architectural patterns and coding guidelines to follow automatically when generating code.

### How to Use It
```bash
# Create at project root
project-root/
├── .cursorrules          ← Create this file
├── src/
└── package.json
```

**Example `.cursorrules` content:**
```
# AI Coding Guidelines

## Architecture Principles
- Use feature-based organization
- Keep logic in hooks/ and services/, NOT in components
- One component per file
- One responsibility per function

## Folder Structure Rules
- Components ONLY handle UI (JSX, styling, user events)
- All business logic goes in hooks/ or services/
- All data types go in types/
- All utilities go in utils/

## React Component Guidelines
- Make components small and focused
- Use custom hooks for data fetching (never direct Supabase calls in components)
- Props should be simple types, not complex logic
- Always add TypeScript types to props

## Custom Hooks Guidelines
- Name all hooks with "use" prefix (usePostsQuery, useBilling, etc.)
- Keep hooks under 100 lines
- Each hook has ONE responsibility
- Document with TSDoc comments

## File Naming
- Components: PascalCase (PostsList.tsx)
- Hooks: camelCase starting with "use" (usePostsQuery.ts)
- Services: camelCase (authService.ts)
- Types: descriptive PascalCase (User.types.ts)

## Imports
- Never import UI components into hooks
- Never import hooks into other hooks (except built-in React hooks)
- Keep import paths relative and clear

## Error Handling
- Always handle errors in hooks
- Return error state from hooks
- Show user-friendly error messages in components
- Log errors for debugging

## Performance
- Use React.memo for expensive components
- Implement useCallback for event handlers
- Don't fetch data on every render (use useEffect)
```

### When to Use It
✅ **Use this EVERY single project**
- From day 1
- Never skip it
- Update it as the project grows

### Why
When Cursor has clear guidelines:
- **Consistency** - All AI-generated code follows the same pattern
- **Predictability** - You know where to find things
- **Maintainability** - New team members understand the structure
- **Fewer conflicts** - AI doesn't generate contradictory code
- **Less review time** - Generated code already follows your standards

**Without `.cursorrules`:** AI generates code randomly placed, mixed logic/UI, inconsistent patterns
**With `.cursorrules`:** AI automatically puts logic in hooks, UI in components, types in types/ folder

---

## 2️⃣ ARCHITECTURE.md Documentation File

### What It Is
A markdown file that explains your project's folder structure and design decisions. It's the "source of truth" for how your project is organized.

### How to Use It
```bash
project-root/
├── ARCHITECTURE.md       ← Create this
├── .cursorrules
└── src/
```

**Example ARCHITECTURE.md:**
```markdown
# Project Architecture

## Folder Structure
```
src/
├── pages/              # Full-page components (one per route)
├── components/         # Reusable UI components
│   ├── student/       # Student-specific components
│   ├── trainer/       # Trainer-specific components
│   ├── admin/         # Admin-specific components
│   └── ui/            # Base UI components (buttons, modals, etc.)
├── hooks/             # Custom React hooks (business logic)
│   ├── student/       # Student-specific hooks
│   ├── trainer/       # Trainer-specific hooks
│   └── admin/         # Admin-specific hooks
├── services/          # Non-React utilities and helpers
│   ├── authService.ts # Authentication utilities
│   ├── apiService.ts  # API call wrapper
│   └── utils/         # General utilities
├── types/             # TypeScript type definitions
│   ├── database.types.ts  # Auto-generated from Supabase
│   └── custom.types.ts    # App-specific types
├── contexts/          # React Context for global state
│   ├── AuthContext.tsx    # Auth state
│   └── UserContext.tsx    # User data state
└── App.tsx            # Main app component with routing
```

## Core Rules

### Rule 1: Components Do ONLY UI
- Render JSX
- Handle user interactions (onClick, onChange, etc.)
- Show/hide elements
- Apply styling

❌ DON'T do this in components:
```typescript
// WRONG - Logic in component
export function StudentPortal() {
  const [posts, setPosts] = useState([]);
  
  useEffect(() => {
    supabase.from('posts').select().then(data => setPosts(data));
  }, []);
  
  return <div>{posts.map(p => <p>{p.title}</p>)}</div>;
}
```

✅ DO this instead:
```typescript
// CORRECT - Logic in hook
export function StudentPortal() {
  const { posts } = useStudentPosts();
  
  return <div>{posts.map(p => <p>{p.title}</p>)}</div>;
}
```

### Rule 2: Hooks Handle Logic
- Fetch data from Supabase
- Manage complex state
- Handle business logic
- Return data and functions to components

### Rule 3: Services Handle Utilities
- API call wrappers
- Authentication
- Formatting/validation
- Calculations

## When Adding a New Feature

1. **Create the hook** in `hooks/[role]/use[Feature].ts`
2. **Create the component** in `components/[role]/[Feature].tsx`
3. **Add types** in `types/custom.types.ts`
4. **Use in page** in `pages/[PageName].tsx`
```

### When to Use It
✅ **Create this at project start**
- First day of the project
- Update as architecture evolves
- Reference when confused

### Why
Cursor and other AI assistants read this file. When you say "follow our architecture," they:
- Know exactly how to structure new code
- Understand your naming conventions
- Know where each type of code belongs
- Can make consistent decisions automatically

---

## 3️⃣ README.md Project Overview

### What It Is
Entry point documentation explaining what the project does, how to set it up, and how to get started.

### How to Use It
```bash
project-root/
├── README.md          ← Create this
├── ARCHITECTURE.md
└── .cursorrules
```

**Example README.md:**
```markdown
# NoCodeLab Training Platform

## Quick Start

### Prerequisites
- Node.js 18+
- npm or pnpm
- Supabase account

### Installation
```bash
# 1. Clone repo
git clone [repo-url]
cd project-name

# 2. Install dependencies
npm install

# 3. Setup environment
cp .env.example .env.local
# Edit .env.local with your Supabase credentials

# 4. Start development
npm run dev
# Open http://localhost:5173
```

## Project Overview

This is a training platform with three portals:
- **Student Portal** - Take courses, generate PRDs, build prototypes
- **Trainer Portal** - Manage sessions, issue certificates
- **Admin Portal** - Platform analytics and configuration

## Tech Stack
- Frontend: React 18 + TypeScript + Vite
- Backend: Supabase (PostgreSQL + Edge Functions)
- Styling: Tailwind CSS + shadcn/ui
- AI: OpenAI GPT-4o-mini + V0.dev SDK

## Development Commands
```bash
npm run dev          # Start dev server
npm run build        # Build for production
npm run lint         # Check code quality
npm run type-check   # Check TypeScript types
```

## Architecture
See `ARCHITECTURE.md` for detailed folder structure.

## Coding Guidelines
See `.cursorrules` for AI assistant guidelines.
```

### When to Use It
✅ **Create immediately**
- Day 1 of project
- Keep it updated
- Use it to onboard new developers

### Why
- **Clarity** - Everyone (human and AI) knows the project purpose
- **Onboarding** - New developers get started quickly
- **Maintenance** - Commands and setup are documented
- **AI context** - Cursor understands the project from day 1

---

# DOCUMENTATION & GUIDELINES

## 4️⃣ Feature PRD (Product Requirements Document) Files

### What It Is
A markdown file inside each feature folder that documents what that feature should do, why it exists, and what the requirements are.

### How to Use It
```bash
src/features/[feature-name]/
├── prd.md              ← Create this
├── [Feature].tsx       # Component
├── internal/           # Private implementation
│   └── use[Feature].ts # Hook
└── index.ts
```

**Example Feature PRD (`src/features/certification/prd.md`):**
```markdown
# Certification Quiz Feature

## Objective
Allow trainers to assess trainer knowledge through quizzes before issuing certificates.

## User Stories

### As a Trainer
- I want to take certification quizzes
- I want to see my quiz scores
- I want to retake failed quizzes
- So that I can prove my expertise

### As an Admin
- I want to create/edit certification quizzes
- I want to track quiz attempts
- So that I can manage certification standards

## Requirements

### Functional Requirements
1. Display quiz questions one at a time
2. Show progress (Q3/Q10)
3. Show score after completion
4. Allow retakes (up to 3 times)
5. Track all attempts for analytics

### Non-Functional Requirements
1. Quiz loads in under 2 seconds
2. Supports up to 50 questions
3. Works offline (stores answer locally)
4. Mobile responsive

## Acceptance Criteria
- [ ] Quiz displays questions in random order
- [ ] User can't go back after answering
- [ ] Results show score as percentage
- [ ] Results are saved to database
- [ ] User sees "Pass" or "Fail" with score

## Out of Scope
- Proctoring/cheating detection
- AI-generated question suggestions (future feature)
- Question banks/random selection

## Implementation Notes
- Use React Hook Form for quiz state management
- Store progress in localStorage for offline support
- Call `/save-quiz-result` edge function to persist results
```

### When to Use It
✅ **Create for each major feature**
- Before writing code
- Before prompting Cursor
- Reference during development

### Why
- **Clarity** - Everyone knows exactly what to build
- **Scope control** - Prevents "creeping scope" where features grow unexpectedly
- **AI guidance** - Cursor reads this and generates code that meets requirements
- **QA** - Clear acceptance criteria for testing
- **Documentation** - Future reference for what was implemented and why

---

## 5️⃣ CODING_GUIDELINES.md (Detailed Standards)

### What It Is
An extended version of `.cursorrules` that explains the "why" behind each guideline. More detailed than `.cursorrules`.

### How to Use It
```bash
project-root/
├── CODING_GUIDELINES.md   ← Optional detailed version
├── .cursorrules           # Quick reference
└── ARCHITECTURE.md
```

**Example CODING_GUIDELINES.md:**
```markdown
# Coding Guidelines for AI-Assisted Development

## 1. Component Organization

### Rule: One Component Per File
```typescript
// ✅ CORRECT - Each component in its own file
src/components/student/
├── CourseList.tsx
├── CourseCard.tsx
└── CourseFilter.tsx
```

### Why?
- AI tools can focus on one thing at a time
- File size stays manageable (easier for AI to read)
- Reusability increases
- Testing becomes simpler

### When to Apply?
- Always, no exceptions
- Even if a component is only used once

---

## 2. Hook Naming Convention

### Rule: Always Start with "use"
```typescript
// ✅ CORRECT
export function useStudentCourses() { }
export function useBillingCalculations() { }
export function useTrainerNotifications() { }

// ❌ WRONG
export function getStudentCourses() { }
export function calculateBilling() { }
export function getNotifications() { }
```

### Why?
- React convention that signals "this has state/side effects"
- AI recognizes this pattern immediately
- Makes code self-documenting
- Enables custom hook linting rules

### When to Apply?
- Every custom hook, every time
- No exceptions

---

## 3. Types: Define Everything

### Rule: Export Types from Every File That Uses Them
```typescript
// ✅ CORRECT - types.ts file
export type Student = {
  id: string;
  email: string;
  enrolledCourses: Course[];
};

export type Course = {
  id: string;
  title: string;
  duration: number;
};

// Then import where needed
import type { Student, Course } from '@/types/Student.types';
```

### Why?
- Type safety catches errors before runtime
- AI can generate better code with type hints
- Autocomplete works better
- Documentation is built into types

### When to Apply?
- Every function parameter
- Every component prop
- Every return value
- Every state variable

---

## 4. Error Handling: Always Include

### Rule: Return Error State from Hooks
```typescript
// ✅ CORRECT - Hook returns error state
export function useStudentCourses() {
  const [courses, setCourses] = useState<Course[]>([]);
  const [error, setError] = useState<string | null>(null);
  const [loading, setLoading] = useState(false);

  useEffect(() => {
    const fetch = async () => {
      try {
        setLoading(true);
        const { data, error: err } = await supabase
          .from('courses')
          .select();
        if (err) throw err;
        setCourses(data);
      } catch (err) {
        setError(err.message);
      } finally {
        setLoading(false);
      }
    };
    fetch();
  }, []);

  return { courses, error, loading };
}
```

### Why?
- Components can show loading states
- Users see error messages
- AI knows what error scenarios to handle

### When to Apply?
- Every hook that fetches data
- Every async operation
- Every external API call

---

## 5. Component Props: Be Explicit

### Rule: Always Define Props Interface
```typescript
// ✅ CORRECT - Explicit prop types
interface CourseCardProps {
  course: Course;
  onEnroll: (courseId: string) => void;
  isEnrolled?: boolean;
}

export function CourseCard({ course, onEnroll, isEnrolled }: CourseCardProps) {
  return (
    <div>
      <h3>{course.title}</h3>
      <button onClick={() => onEnroll(course.id)}>
        {isEnrolled ? 'Enrolled' : 'Enroll'}
      </button>
    </div>
  );
}

// ❌ WRONG - Vague props
export function CourseCard(props: any) {
  return <div>{props.data?.course?.info?.title}</div>;
}
```

### Why?
- AI knows exactly what data the component needs
- TypeScript prevents passing wrong data
- Autocomplete helps developers use the component
- Self-documenting code

### When to Apply?
- Every component, every time
- Even simple components

---

## 6. Service Layer: Keep Supabase Behind Abstraction

### Rule: Never Call Supabase Directly from Components

```typescript
// ❌ WRONG - Direct Supabase in component
export function StudentCourses() {
  const [courses, setCourses] = useState([]);
  
  useEffect(() => {
    supabase.from('courses').select().then(data => setCourses(data));
  }, []);
}

// ✅ CORRECT - Via service layer
// services/courseService.ts
export const courseService = {
  async getCourses() {
    return await supabase.from('courses').select();
  }
};

// hooks/useStudentCourses.ts
export function useStudentCourses() {
  const [courses, setCourses] = useState([]);
  
  useEffect(() => {
    courseService.getCourses().then(data => setCourses(data));
  }, []);
  
  return { courses };
}

// components/StudentCourses.tsx
export function StudentCourses() {
  const { courses } = useStudentCourses();
  return <div>{courses.map(c => <p>{c.title}</p>)}</div>;
}
```

### Why?
- Can change backend without touching components
- Easy to add caching, retry logic, error handling in ONE place
- Easy to test (mock the service)
- Multiple components share same logic

### When to Apply?
- Every external API call
- Every Supabase query
- Every database operation

---

## 7. File Size Limits

### Rule: Keep Files Under 200 Lines
```
Good file sizes:
- Components: 50-150 lines
- Hooks: 30-100 lines
- Services: 50-200 lines
- Pages: 100-300 lines (orchestrates multiple components)

Red flags:
- 500+ line component = split into smaller components
- 300+ line hook = extract logic to service
- Single file doing multiple things = refactor
```

### Why?
- AI can see entire file context in one view
- Easier to understand at a glance
- Easier to reuse pieces
- Better for version control diffs

### When to Apply?
- As you write code
- During code review
- Before committing

---

## 8. Folder Organization by Role/Feature

### Rule: Group by Business Domain, Not Technology

```typescript
// ✅ CORRECT - Organized by role/feature
src/
├── components/student/
│   ├── CourseList.tsx
│   ├── CourseCard.tsx
│   └── EnrollmentFlow.tsx
├── hooks/student/
│   ├── useStudentCourses.ts
│   ├── useEnrollment.ts
│   └── useStudentProgress.ts
├── pages/StudentPortal.tsx

// ❌ WRONG - Organized by technology
src/
├── components/
│   ├── CourseList.tsx
│   ├── StudentForm.tsx
│   └── AdminTable.tsx
├── containers/
│   ├── CourseContainer.tsx
├── hooks/
│   ├── useData.ts
│   ├── useFetch.ts
```

### Why?
- Feature-based makes it easier to find related code
- Adding features is clear (create new folder)
- Easier to move/delete features
- AI understands business domains

### When to Apply?
- Foundational architecture decision
- Never mix both approaches

---

## 9. Imports: Keep Them Clean

### Rule: Use Absolute Imports, Organize by Layer

```typescript
// ✅ CORRECT - Organized imports
import { Student, Course } from '@/types/Student.types';
import { courseService } from '@/services/courseService';
import { useStudentCourses } from '@/hooks/student/useStudentCourses';
import { CourseCard } from '@/components/student/CourseCard';
import { Button } from '@/components/ui/button';

// ❌ WRONG - Mix of import styles
import Student from '../../../../types/Student.types';
import courseService from '../../../services/courseService';
import useStudentCourses from '../../hooks/student';
```

### Why?
- Absolute imports don't break when files move
- Organized imports show code structure
- AI can find imports quickly
- Easier to understand dependencies

### When to Apply?
- Configure TypeScript `baseUrl` and `paths`
- Every import, every time

---

## 10. Avoid These Anti-Patterns

### ❌ Don't Mix Logic and UI
```typescript
// WRONG
export function Dashboard() {
  const [data, setData] = useState();
  const calculateTotal = () => { /* 20 lines of logic */ };
  const fetchData = async () => { /* 15 lines */ };
  const validateForm = () => { /* 10 lines */ };
  
  return <div>{/* 100 lines of JSX */}</div>;
}
```

### ❌ Don't Create Hooks that Call Other Hooks
```typescript
// WRONG
export function useStudentData() {
  const courses = useStudentCourses();  // ❌ Hooks shouldn't call hooks
  const progress = useStudentProgress();
  return { courses, progress };
}

// RIGHT
// Let the component call both hooks
export function StudentDashboard() {
  const { courses } = useStudentCourses();
  const { progress } = useStudentProgress();
}
```

### ❌ Don't Hardcode Strings
```typescript
// WRONG
return <div>Error: Something went wrong</div>;

// RIGHT - Use constants
const ERROR_MESSAGES = {
  FETCH_FAILED: 'Failed to load data',
  NETWORK_ERROR: 'Check your internet connection'
};
return <div>Error: {ERROR_MESSAGES.FETCH_FAILED}</div>;
```

### ❌ Don't Skip Types
```typescript
// WRONG
export function formatDate(date: any): any {
  return date.toLocaleDateString();
}

// RIGHT
export function formatDate(date: Date): string {
  return date.toLocaleDateString();
}
```

```

### When to Use It
✅ **Create as project grows**
- After first 10+ files
- When patterns emerge
- Reference during code review

### Why
- **Details** - Explains the "why" behind each rule
- **Examples** - Shows right vs wrong code
- **Learning** - Helps team understand architectural decisions
- **AI guidance** - More context for Cursor to work with

---

# CONFIGURATION FILES

## 6️⃣ tsconfig.json (TypeScript Configuration)

### What It Is
Configuration file that tells TypeScript how to compile your code and enables path aliases for cleaner imports.

### How to Use It
```json
{
  "compilerOptions": {
    "target": "ES2020",
    "useDefineForClassFields": true,
    "lib": ["ES2020", "DOM", "DOM.Iterable"],
    "module": "ESNext",
    "skipLibCheck": true,
    "esModuleInterop": true,
    "allowSyntheticDefaultImports": true,
    
    // ✅ IMPORTANT - Enable absolute imports
    "baseUrl": ".",
    "paths": {
      "@/*": ["./src/*"],
      "@/components/*": ["./src/components/*"],
      "@/hooks/*": ["./src/hooks/*"],
      "@/services/*": ["./src/services/*"],
      "@/types/*": ["./src/types/*"],
      "@/utils/*": ["./src/utils/*"]
    },
    
    "resolveJsonModule": true,
    "isolatedModules": true,
    "noEmit": true,
    "jsx": "react-jsx",
    "strict": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noFallthroughCasesInSwitch": true
  },
  "include": ["src"],
  "references": [{ "path": "./tsconfig.node.json" }]
}
```

### When to Use It
✅ **Set this up immediately**
- Before writing any code
- Never change after starting unless necessary

### Why
- **Path aliases** - Write `import { X } from '@/types/X'` instead of `import { X } from '../../../../types/X'`
- **Type safety** - `strict: true` catches more errors
- **AI friendly** - Cursor understands the import structure
- **Consistency** - Everyone imports the same way

---

## 7️⃣ vite.config.ts (Build Configuration)

### What It Is
Configuration for Vite, the build tool that bundles your React code for production.

### How to Use It
```typescript
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'
import path from 'path'

export default defineConfig({
  plugins: [react()],
  resolve: {
    alias: {
      '@': path.resolve(__dirname, './src'),
    },
  },
  server: {
    port: 5173,
    open: true,
  },
})
```

### When to Use It
✅ **Setup once at project start**
- Configure path alias to match tsconfig.json
- Usually doesn't need changes after

### Why
- **Development** - Aligns with tsconfig for consistent imports
- **Production** - Optimizes code for fastest loading
- **HMR** - Hot reload while developing (see changes instantly)

---

## 8️⃣ eslint.config.js (Code Quality)

### What It Is
Configuration that automatically checks your code for quality issues and catches bugs before they happen.

### How to Use It
```javascript
import js from '@eslint/js'
import globals from 'globals'
import react from 'eslint-plugin-react'
import reactHooks from 'eslint-plugin-react-hooks'
import typescript from 'typescript-eslint'

export default [
  { ignores: ['dist'] },
  {
    files: ['**/*.{js,jsx,ts,tsx}'],
    languageOptions: {
      ecmaVersion: 2020,
      globals: globals.browser,
      parserOptions: {
        ecmaFeatures: { jsx: true },
      },
    },
    settings: { react: { version: '18.3' } },
    plugins: {
      react,
      'react-hooks': reactHooks,
      '@typescript-eslint': typescript,
    },
    rules: {
      ...js.configs.recommended.rules,
      ...react.configs.recommended.rules,
      ...reactHooks.configs.recommended.rules,
      'react/react-in-jsx-scope': 'off',
      'react/prop-types': 'off',
    },
  },
]
```

### When to Use It
✅ **Create immediately**
- Catch mistakes early
- Enforce code standards

### Why
- **Catches bugs** - Unused variables, missing dependencies in useEffect
- **Consistency** - Everyone's code looks the same
- **Best practices** - Enforces React rules (hooks dependencies, etc.)
- **Prevents issues** - Finds problems before code review

---

## 9️⃣ tailwind.config.ts (Styling Configuration)

### What It Is
Configuration for Tailwind CSS, a utility-first CSS framework that makes styling fast.

### How to Use It
```typescript
import type { Config } from 'tailwindcss'

export default {
  content: [
    "./index.html",
    "./src/**/*.{js,ts,jsx,tsx}",
  ],
  theme: {
    extend: {
      colors: {
        primary: '#3b82f6',
        secondary: '#8b5cf6',
      },
      spacing: {
        '128': '32rem',
      },
    },
  },
  plugins: [],
} satisfies Config
```

### When to Use It
✅ **Setup at project start**
- Configure custom colors/spacing if needed
- Usually works out of the box

### Why
- **Fast styling** - Use utility classes instead of writing CSS
- **Consistency** - Shared design tokens
- **Responsive** - Built-in responsive design
- **Smaller bundle** - Only includes used styles

---

# FRONTEND ARCHITECTURE

## 🔟 Pages Folder Structure

### What It Is
React components that represent full pages/routes in your application. Each page typically orchestrates multiple smaller components.

### How to Use It
```bash
src/pages/
├── StudentPortal.tsx          # /student route
├── StudentLogin.tsx           # /student/login route
├── TrainerDashboard.tsx       # /trainer route
├── AdminPanel.tsx             # /admin route
└── NotFound.tsx               # 404 page
```

**Example page component:**
```typescript
// src/pages/StudentPortal.tsx
import { useAuth } from '@/hooks/useAuth';
import { StudentSidebar } from '@/components/student/StudentSidebar';
import { CourseList } from '@/components/student/CourseList';
import { StudentHeader } from '@/components/student/StudentHeader';

export function StudentPortal() {
  const { user } = useAuth();

  if (!user) return <div>Loading...</div>;

  return (
    <div className="flex h-screen">
      <StudentSidebar />
      <div className="flex-1 flex flex-col">
        <StudentHeader user={user} />
        <main className="flex-1 overflow-auto">
          <CourseList />
        </main>
      </div>
    </div>
  );
}
```

### When to Use It
✅ **One page per route**
- StudentPortal for student dashboard
- TrainerDashboard for trainer dashboard
- Create new page for new route

### Why
- **Organization** - Clear mapping between routes and components
- **Navigation** - Know which page to edit for which route
- **AI clarity** - Cursor knows this is a page-level component
- **Reusability** - Pages use components, not vice versa

---

## 1️⃣1️⃣ Components Folder Structure

### What It Is
Reusable UI components organized by role/feature. Components handle rendering only—NO business logic.

### How to Use It
```bash
src/components/
├── student/
│   ├── CourseList.tsx
│   ├── CourseCard.tsx
│   ├── EnrollmentFlow.tsx
│   ├── CertificateSection.tsx
│   └── StudentProgress.tsx
├── trainer/
│   ├── SessionsTable.tsx
│   ├── CreateSessionForm.tsx
│   ├── StudentDetailsModal.tsx
│   └── BillingCard.tsx
├── admin/
│   ├── AnalyticsDashboard.tsx
│   ├── UsersTable.tsx
│   ├── UserFormModal.tsx
│   └── ReportGenerator.tsx
└── ui/
    ├── button.tsx            # shadcn/ui components
    ├── input.tsx
    ├── modal.tsx
    ├── card.tsx
    ├── table.tsx
    └── ... (40+ more)
```

**Example component (UI ONLY):**
```typescript
// src/components/student/CourseCard.tsx
interface CourseCardProps {
  course: Course;
  onEnroll: (courseId: string) => void;
  isEnrolled?: boolean;
}

export function CourseCard({ course, onEnroll, isEnrolled }: CourseCardProps) {
  return (
    <div className="border rounded-lg p-4">
      <h3 className="font-bold text-lg">{course.title}</h3>
      <p className="text-gray-600">{course.description}</p>
      <button
        onClick={() => onEnroll(course.id)}
        className="mt-4 bg-blue-500 text-white px-4 py-2 rounded"
      >
        {isEnrolled ? 'Enrolled' : 'Enroll'}
      </button>
    </div>
  );
}
```

### When to Use It
✅ **Use INSTEAD of putting logic in pages**
- Break large pages into smaller components
- Create reusable components (e.g., buttons, forms)
- One component per file

❌ **Don't**:
- Put Supabase calls in components
- Have components over 200 lines
- Mix logic and UI

### Why
- **Reusability** - Use same component in multiple pages
- **Testability** - Mock data and test rendering
- **Clarity** - Component has ONE job: display UI
- **AI quality** - Cursor generates better UI code without logic

---

## 1️⃣2️⃣ Hooks Folder Structure

### What It Is
Custom React hooks that contain business logic, state management, and data fetching. Components call hooks to get data.

### How to Use It
```bash
src/hooks/
├── student/
│   ├── useStudentData.ts
│   ├── useStudentCourses.ts
│   ├── useEnrollment.ts
│   ├── usePRDGeneration.ts
│   └── useStudentProgress.ts
├── trainer/
│   ├── useTrainerData.ts
│   ├── useTrainerSessions.ts
│   ├── useBilling.ts
│   ├── useCertification.ts
│   └── useTrainerStudents.ts
├── admin/
│   ├── useAnalytics.ts
│   ├── useUserManagement.ts
│   ├── useReporting.ts
│   └── useRevenue.ts
└── shared/
    ├── useAuth.ts
    ├── useLocalStorage.ts
    └── useDebounce.ts
```

**Example custom hook (LOGIC ONLY):**
```typescript
// src/hooks/student/useStudentCourses.ts
import { useEffect, useState } from 'react';
import type { Course } from '@/types/Course.types';
import { courseService } from '@/services/courseService';

interface UseStudentCoursesReturn {
  courses: Course[];
  loading: boolean;
  error: string | null;
  refetch: () => Promise<void>;
}

export function useStudentCourses(): UseStudentCoursesReturn {
  const [courses, setCourses] = useState<Course[]>([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);

  const fetchCourses = async () => {
    try {
      setLoading(true);
      setError(null);
      const data = await courseService.getStudentCourses();
      setCourses(data);
    } catch (err) {
      setError(err instanceof Error ? err.message : 'Unknown error');
    } finally {
      setLoading(false);
    }
  };

  useEffect(() => {
    fetchCourses();
  }, []);

  return { courses, loading, error, refetch: fetchCourses };
}
```

### When to Use It
✅ **Always for data fetching**
- Fetch from Supabase
- Manage complex state
- Handle side effects
- Transform data

**Never** call Supabase directly from components. Use a hook.

### Why
- **Single source of truth** - Change logic once, all components benefit
- **Reusable** - Multiple components can use same hook
- **Testable** - Test hook independently
- **Easy updates** - Add loading states, error handling in ONE place

---

## 1️⃣3️⃣ Services Folder Structure

### What It Is
Non-React utility functions that handle external APIs, Supabase queries, and business calculations.

### How to Use It
```bash
src/services/
├── authService.ts       # Authentication
├── courseService.ts     # Course-related queries
├── billingService.ts    # Billing calculations
├── apiService.ts        # Centralized API calls
└── utils/
    ├── formatters.ts    # Date, currency formatting
    ├── validators.ts    # Email, phone validation
    └── calculations.ts  # Math operations
```

**Example service:**
```typescript
// src/services/courseService.ts
import { supabase } from '@/lib/supabase';
import type { Course } from '@/types/Course.types';

export const courseService = {
  async getStudentCourses(studentId: string): Promise<Course[]> {
    const { data, error } = await supabase
      .from('courses')
      .select('*')
      .eq('student_id', studentId);

    if (error) throw new Error(error.message);
    return data || [];
  },

  async enrollStudent(studentId: string, courseId: string): Promise<void> {
    const { error } = await supabase
      .from('enrollments')
      .insert([{ student_id: studentId, course_id: courseId }]);

    if (error) throw new Error(error.message);
  },

  async updateCourseProgress(courseId: string, progress: number): Promise<void> {
    const { error } = await supabase
      .from('courses')
      .update({ progress })
      .eq('id', courseId);

    if (error) throw new Error(error.message);
  },
};
```

### When to Use It
✅ **For all database operations**
- Create `get` methods for queries
- Create `create`/`update`/`delete` for mutations
- Handle errors in the service
- Return clean data to hooks

❌ **Don't**:
- Call Supabase directly from components
- Put React hooks in services
- Have services import from components

### Why
- **Abstraction** - Hide Supabase behind interface
- **Easy testing** - Mock services in tests
- **Easy updates** - Add caching, retry logic in ONE place
- **Backend flexibility** - Switch from Supabase to Firebase later with minimal changes

---

## 1️⃣4️⃣ Types Folder Structure

### What It Is
TypeScript type definitions for your application. All data types live here.

### How to Use It
```bash
src/types/
├── database.types.ts    # Auto-generated from Supabase
├── Student.types.ts
├── Course.types.ts
├── Trainer.types.ts
├── Certificate.types.ts
└── common.types.ts      # Shared types
```

**Example types file:**
```typescript
// src/types/Course.types.ts
export type Course = {
  id: string;
  title: string;
  description: string;
  duration: number; // minutes
  difficulty: 'beginner' | 'intermediate' | 'advanced';
  instructor_id: string;
  created_at: string;
  updated_at: string;
};

export type CourseProgress = {
  course_id: string;
  student_id: string;
  progress_percentage: number;
  completed_at: string | null;
};

export type CreateCourseInput = Omit<Course, 'id' | 'created_at' | 'updated_at'>;

export interface CourseFilters {
  difficulty?: Course['difficulty'];
  instructor_id?: string;
  search?: string;
}
```

### When to Use It
✅ **Define types for everything**
- Every API response
- Every component prop
- Every hook return value
- Every service parameter

### Why
- **Type safety** - Catch errors at compile time, not runtime
- **Autocomplete** - IDEs can suggest correct properties
- **Documentation** - Types document the data structure
- **AI quality** - Cursor generates better code with types

---

## 1️⃣5️⃣ Contexts Folder Structure

### What It Is
React Context for global state that needs to be accessed by many components (auth state, user data, app settings).

### How to Use It
```bash
src/contexts/
├── AuthContext.tsx      # Authentication state
├── UserContext.tsx      # Global user data
└── AppContext.tsx       # App settings
```

**Example context:**
```typescript
// src/contexts/AuthContext.tsx
import { createContext, useContext, useEffect, useState } from 'react';
import type { User } from '@supabase/supabase-js';
import { supabase } from '@/lib/supabase';

interface AuthContextType {
  user: User | null;
  loading: boolean;
  signIn: (email: string, password: string) => Promise<void>;
  signUp: (email: string, password: string) => Promise<void>;
  signOut: () => Promise<void>;
}

const AuthContext = createContext<AuthContextType | undefined>(undefined);

export function AuthProvider({ children }: { children: React.ReactNode }) {
  const [user, setUser] = useState<User | null>(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    // Check if user is logged in
    supabase.auth.getSession().then(({ data: { session } }) => {
      setUser(session?.user ?? null);
      setLoading(false);
    });

    // Listen for auth changes
    const { data: { subscription } } = supabase.auth.onAuthStateChange(
      (_event, session) => {
        setUser(session?.user ?? null);
      }
    );

    return () => subscription?.unsubscribe();
  }, []);

  return (
    <AuthContext.Provider value={{ user, loading, signIn, signUp, signOut }}>
      {children}
    </AuthContext.Provider>
  );
}

export function useAuth() {
  const context = useContext(AuthContext);
  if (!context) throw new Error('useAuth must be used within AuthProvider');
  return context;
}
```

### When to Use It
✅ **For truly global state only**
- Authentication (user login state)
- Current user profile
- App-wide settings
- Theme/language preference

❌ **Don't use for**:
- Feature-specific state (use hooks instead)
- Temporary UI state (use component state)
- Too many contexts (max 3-4)

### Why
- **Avoid prop drilling** - Don't pass props through 5 levels of components
- **Global access** - Any component can access `useAuth()`
- **Centralized** - One place to manage shared state
- **Performance** - Only re-render components that need updates

---

# BACKEND ARCHITECTURE

## 1️⃣6️⃣ Supabase Edge Functions

### What It Is
Serverless functions that run on Supabase servers. Use for AI APIs, payments, and complex logic you don't want exposed to the client.

### How to Use It
```bash
supabase/
└── functions/
    ├── generate-prd/
    │   ├── index.ts          # Main function code
    │   └── deno.json         # Dependencies
    ├── generate-frontend/
    │   ├── index.ts
    │   └── deno.json
    └── process-payment/
        ├── index.ts
        └── deno.json
```

**Example Edge Function:**
```typescript
// supabase/functions/generate-prd/index.ts
import { serve } from "https://deno.land/std@0.192.0/http/server.ts";
import { createClient } from "https://esm.sh/@supabase/supabase-js";

const openAiApiKey = Deno.env.get("OPENAI_API_KEY");
const supabaseUrl = Deno.env.get("SUPABASE_URL");
const supabaseKey = Deno.env.get("SUPABASE_SERVICE_ROLE_KEY");

serve(async (req) => {
  // Only allow POST requests
  if (req.method !== "POST") {
    return new Response("Method not allowed", { status: 405 });
  }

  try {
    const { prompt, courseId, userId } = await req.json();

    // Call OpenAI API
    const response = await fetch("https://api.openai.com/v1/chat/completions", {
      method: "POST",
      headers: {
        "Authorization": `Bearer ${openAiApiKey}`,
        "Content-Type": "application/json",
      },
      body: JSON.stringify({
        model: "gpt-4o-mini",
        messages: [{ role: "user", content: prompt }],
        max_tokens: 3000,
      }),
    });

    const data = await response.json();
    const generatedPRD = data.choices[0].message.content;

    // Save to database
    const supabase = createClient(supabaseUrl, supabaseKey);
    await supabase.from("generated_prds").insert({
      user_id: userId,
      course_id: courseId,
      content: generatedPRD,
      created_at: new Date(),
    });

    return new Response(JSON.stringify({ success: true, prd: generatedPRD }), {
      headers: { "Content-Type": "application/json" },
    });
  } catch (error) {
    return new Response(
      JSON.stringify({ error: error.message }),
      { status: 500, headers: { "Content-Type": "application/json" } }
    );
  }
});
```

### When to Use It
✅ **Use for:**
- OpenAI/AI API calls (keep API key secret)
- Stripe/payment processing (keep secret key secret)
- Complex calculations
- Administrative operations
- Webhook handlers

❌ **Don't use for:**
- Simple database CRUD (use RLS in database)
- User authentication (Supabase handles this)
- Simple queries (client can do with RLS)

### Why
- **Security** - API keys stay on server, never exposed to client
- **Scalability** - Serverless runs on demand, auto-scales
- **Isolation** - External API errors don't crash client
- **Privacy** - Server-side logic stays private

---

## 1️⃣7️⃣ Row Level Security (RLS) Policies

### What It Is
Database security rules that automatically filter data based on who's querying it. Students only see their own data, trainers see their sessions, etc.

### How to Use It
```sql
-- Example RLS policy for courses table
-- Students can only see courses they're enrolled in

CREATE POLICY "Students can view their courses"
ON public.courses
FOR SELECT
USING (
  auth.uid() = instructor_id OR
  EXISTS (
    SELECT 1 FROM enrollments
    WHERE enrollments.course_id = courses.id
    AND enrollments.student_id = auth.uid()
  )
);

-- Trainers can only see/edit their own sessions
CREATE POLICY "Trainers can manage their sessions"
ON public.sessions
FOR ALL
USING (trainer_id = auth.uid());

-- Admins can see everything
CREATE POLICY "Admins have full access"
ON public.sessions
FOR ALL
USING (
  EXISTS (
    SELECT 1 FROM user_roles
    WHERE user_id = auth.uid()
    AND role = 'admin'
  )
);
```

### When to Use It
✅ **Enable on EVERY table**
- Even with authenticated users
- Especially sensitive data
- No exceptions

### Why
- **Automatic filtering** - Database enforces permissions
- **Prevent data leaks** - Users can't query other users' data
- **Client-side independent** - Works even if frontend is hacked
- **Performance** - Database filters at source (faster queries)

---

## 1️⃣8️⃣ Database Schema & Migrations

### What It Is
The structure of your PostgreSQL database. Define tables, relationships, and migrations as code.

### How to Use It
```bash
supabase/
└── migrations/
    ├── 20240101000000_create_tables.sql
    ├── 20240102000000_add_rls_policies.sql
    └── 20240103000000_create_indexes.sql
```

**Example migration:**
```sql
-- supabase/migrations/20240101000000_create_courses_table.sql
CREATE TABLE public.courses (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  title TEXT NOT NULL,
  description TEXT,
  duration_minutes INTEGER NOT NULL,
  difficulty TEXT CHECK (difficulty IN ('beginner', 'intermediate', 'advanced')),
  instructor_id UUID NOT NULL REFERENCES auth.users(id),
  created_at TIMESTAMP DEFAULT now(),
  updated_at TIMESTAMP DEFAULT now()
);

-- Create index for faster queries
CREATE INDEX courses_instructor_idx ON public.courses(instructor_id);

-- Enable RLS
ALTER TABLE public.courses ENABLE ROW LEVEL SECURITY;

-- RLS Policy: Instructors can see their own courses
CREATE POLICY "Instructors can view own courses"
ON public.courses FOR SELECT
USING (instructor_id = auth.uid());

-- RLS Policy: Students can view public courses
CREATE POLICY "Students can view published courses"
ON public.courses FOR SELECT
USING (published = true);
```

### When to Use It
✅ **Design database schema early**
- Before building frontend
- Document in migrations
- Version control migrations
- Never edit tables directly in UI

### Why
- **Version control** - Track all database changes
- **Reproducibility** - Run migrations in dev/staging/production
- **Documentation** - Migrations explain changes
- **Rollback** - Can revert changes if needed

---

# HOW TO PROMPT CURSOR EFFECTIVELY

## 19️⃣ Prompt Template for New Features

### What It Is
A structured way to ask Cursor to build features that follow your architecture.

### How to Use It
When adding a new feature, use this prompt template:

```
I want to add a [FEATURE] feature. Here's what I need:

## Requirements
- [Requirement 1]
- [Requirement 2]
- [Requirement 3]

## Tech Details
- Frontend: React component in src/components/[role]/[Feature].tsx
- Logic: Custom hook in src/hooks/[role]/use[Feature].ts
- Data: Service in src/services/[feature]Service.ts
- Types: Add to src/types/[Feature].types.ts
- Backend: Use existing Supabase table [table_name]

## Implementation Steps
1. Create the TypeScript types in src/types/[Feature].types.ts
2. Create the service in src/services/[feature]Service.ts
3. Create the custom hook in src/hooks/[role]/use[Feature].ts
4. Create the component in src/components/[role]/[Feature].tsx
5. Add the component to the appropriate page

Please follow our ARCHITECTURE.md and .cursorrules guidelines.
```

### Real Example:
```
I want to add a "Certificate Download" feature for students to download their certificates as PDFs.

## Requirements
- Show a "Download Certificate" button on the CertificateSection
- When clicked, generate a PDF with student name, course title, date, and trainer signature
- Track downloads in database for analytics
- Show success toast message

## Tech Details
- Frontend: CertificateDownloadButton.tsx in src/components/student/
- Logic: useCertificateDownload.ts in src/hooks/student/
- Service: certificateService.ts in src/services/ with generatePDF() method
- Types: Add CertificateDownloadData type to src/types/Certificate.types.ts
- Backend: Use `certificates` table (already exists)
- Edge Function: Create generate-certificate-pdf edge function (or use client-side library)

## Implementation Steps
1. Create types in src/types/Certificate.types.ts for PDF data
2. Create certificateService.ts with downloadCertificatePDF() method
3. Create useCertificateDownload hook that handles loading/error states
4. Create CertificateDownloadButton component that calls the hook
5. Add button to CertificateSection.tsx

Please follow our ARCHITECTURE.md and .cursorrules guidelines.
```

### When to Use It
✅ **Use for every new feature**
- Before clicking "Create"
- Include specific folder paths
- Reference your architecture

### Why
- **Clarity** - Cursor knows exactly where to put code
- **Consistency** - Generated code follows your patterns
- **Speed** - Reduces back-and-forth corrections
- **Quality** - Properly structured code first time

---

## 2️⃣0️⃣ Prompt Template for Bug Fixes

### What It Is
How to ask Cursor to find and fix bugs while maintaining your architecture.

### How to Use It
```
I have a bug in [FEATURE]. Here's the issue:

## Problem
- [What's broken]
- [Expected behavior]
- [Actual behavior]

## Relevant Files
- [File 1]
- [File 2]

## Constraints
- Don't move logic from the hook to the component
- Keep the service layer unchanged
- No new dependencies

Please fix this while following our architecture guidelines.
```

### Real Example:
```
I have a bug in the course enrollment. When a user clicks "Enroll", the course list doesn't update.

## Problem
- User clicks "Enroll" on CourseCard
- Component shows success toast
- But CourseList doesn't show the enrolled course badge

## Relevant Files
- src/components/student/CourseList.tsx
- src/components/student/CourseCard.tsx
- src/hooks/student/useStudentCourses.ts
- src/hooks/student/useEnrollment.ts

## Constraints
- Fix must be in the hook (useEnrollment or useStudentCourses)
- Don't move API calls to component
- Don't refactor the CourseCard component structure

The problem is likely that CourseList doesn't refetch after enrollment. Please check the hook implementations and fix.
```

### When to Use It
✅ **Use when fixing bugs**
- Specify files involved
- Explain constraints
- Reference your architecture

### Why
- **Prevents regressions** - Cursor knows not to move code around
- **Focus** - Only fixes the specific issue
- **Consistency** - Maintains your structure

---

## 2️⃣1️⃣ Prompt Template for Refactoring

### What It Is
How to ask Cursor to improve code without breaking functionality.

### How to Use It
```
I want to refactor [FEATURE] to improve [ASPECT].

## Current State
- [What exists now]
- [What's working]
- [What could be better]

## Goals
- [Goal 1]
- [Goal 2]

## Files to Touch
- [File 1]
- [File 2]

## Don't Break
- Keep the same props interface
- Don't change hook return types
- Components should look the same to users

Please refactor while maintaining our architecture.
```

### Real Example:
```
I want to refactor the student course fetching to add caching and reduce API calls.

## Current State
- useStudentCourses hook fetches all courses every time it mounts
- Multiple components use this hook, causing duplicate requests
- No caching between component unmount/remount

## Goals
- Implement React Query or simple cache to avoid duplicate requests
- Keep courses fresh (5 minute cache)
- Provide manual refetch function

## Files to Touch
- src/hooks/student/useStudentCourses.ts
- src/services/courseService.ts

## Don't Break
- Keep same return type: { courses, loading, error, refetch }
- Components shouldn't need to change
- User experience should be faster, not different

Please refactor using React Query for efficient caching.
```

### When to Use It
✅ **Use for improvements**
- Clarify what should change
- What should stay the same
- Reference architecture

### Why
- **Safe refactoring** - Cursor doesn't break things
- **Clear goals** - Understands the improvement target
- **Maintains compatibility** - Other code still works

---

# COMMON MISTAKES & HOW TO AVOID THEM

## ❌ Mistake 1: Logic in Components

### Problem
```typescript
// WRONG - All logic in component
export function StudentCourses() {
  const [courses, setCourses] = useState([]);
  const [loading, setLoading] = useState(false);
  
  useEffect(() => {
    setLoading(true);
    supabase
      .from('courses')
      .select()
      .eq('difficulty', 'beginner')
      .order('created_at', { ascending: false })
      .then(({ data }) => {
        setCourses(data);
        setLoading(false);
      })
      .catch(err => console.error(err));
  }, []);

  if (loading) return <div>Loading...</div>;

  return (
    <div>
      {courses.map(course => (
        <div key={course.id}>
          <h3>{course.title}</h3>
          <p>{course.description}</p>
        </div>
      ))}
    </div>
  );
}
```

### Why It's Bad
- Logic lives in multiple components → duplicate code
- Hard to test (need to mock Supabase in component)
- Hard to change (update 5 places if you change the query)
- Makes component huge and hard to understand

### Solution
```typescript
// src/hooks/student/useBeginnertCourses.ts
export function useBeginnerCourses() {
  const [courses, setCourses] = useState([]);
  const [loading, setLoading] = useState(false);
  
  useEffect(() => {
    const fetch = async () => {
      try {
        setLoading(true);
        const data = await courseService.getBeginnerCourses();
        setCourses(data);
      } finally {
        setLoading(false);
      }
    };
    fetch();
  }, []);

  return { courses, loading };
}

// src/components/student/StudentCourses.tsx
export function StudentCourses() {
  const { courses, loading } = useBeginnerCourses();

  if (loading) return <div>Loading...</div>;

  return (
    <div>
      {courses.map(course => (
        <CourseCard key={course.id} course={course} />
      ))}
    </div>
  );
}
```

### How to Avoid
- ✅ Ask Cursor: "Extract the data fetching logic into a custom hook"
- ✅ Reference `.cursorrules`: "Logic must be in hooks, not components"
- ✅ Code review: Check that components have no Supabase calls

---

## ❌ Mistake 2: Multiple Hooks Calling Other Hooks

### Problem
```typescript
// WRONG - Hooks calling hooks
export function useStudentDashboard() {
  const { courses } = useStudentCourses();      // ❌ Hook calling hook
  const { progress } = useStudentProgress();    // ❌ Hook calling hook
  const { certificates } = useCertificates();   // ❌ Hook calling hook
  
  return { courses, progress, certificates };
}

// Component
export function StudentDashboard() {
  const { courses, progress, certificates } = useStudentDashboard();
}
```

### Why It's Bad
- Breaks React hooks rules
- Makes data flow unclear (hooks depend on hooks)
- Harder to test each hook individually
- Can cause unexpected re-renders

### Solution
```typescript
// ✅ CORRECT - Let component call multiple hooks
export function StudentDashboard() {
  const { courses } = useStudentCourses();
  const { progress } = useStudentProgress();
  const { certificates } = useCertificates();
  
  return (
    <div>
      <CourseSection courses={courses} />
      <ProgressSection progress={progress} />
      <CertificateSection certificates={certificates} />
    </div>
  );
}
```

### How to Avoid
- ✅ Guideline: "Hooks don't call other hooks"
- ✅ Code review: Check hook implementations
- ✅ Prompt Cursor: "Multiple hooks, don't combine them"

---

## ❌ Mistake 3: Inconsistent Error Handling

### Problem
```typescript
// Inconsistent - Some hooks handle errors, some don't
export function useStudentCourses() {
  const [courses, setCourses] = useState([]);
  
  useEffect(() => {
    courseService.getCourses()
      .then(data => setCourses(data))
      // ❌ No error handling
  }, []);

  return { courses };
}

export function useEnrollment() {
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState(null);
  
  const enroll = async (courseId) => {
    try {
      await courseService.enroll(courseId);
    } catch (err) {
      setError(err.message);
    } finally {
      setLoading(false);
    }
  };

  return { enroll, loading, error };
}
```

### Why It's Bad
- Components don't know when errors happen
- Users don't see error messages
- Some features work, some don't
- Debugging is harder

### Solution
```typescript
// Consistent - All hooks return { data, loading, error }
export function useStudentCourses() {
  const [courses, setCourses] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);
  
  useEffect(() => {
    const fetch = async () => {
      try {
        const data = await courseService.getCourses();
        setCourses(data);
      } catch (err) {
        setError(err.message);
      } finally {
        setLoading(false);
      }
    };
    fetch();
  }, []);

  return { courses, loading, error };
}

export function useEnrollment() {
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState(null);
  
  const enroll = async (courseId) => {
    setLoading(true);
    setError(null);
    try {
      await courseService.enroll(courseId);
    } catch (err) {
      setError(err.message);
    } finally {
      setLoading(false);
    }
  };

  return { enroll, loading, error };
}
```

### How to Avoid
- ✅ Coding guideline: "All hooks return { data, loading, error }"
- ✅ Template: Copy error handling from existing hooks
- ✅ Prompt: "Add error handling following the pattern in [existing hook]"

---

## ❌ Mistake 4: No TypeScript Types

### Problem
```typescript
// WRONG - No types
export function useCourses() {
  const [courses, setCourses] = useState([]);
  return { courses };
}

export function CourseCard(props) {  // ❌ No prop types
  return <div>{props.course.title}</div>;
}

export function createCourse(data) {  // ❌ No param types
  return supabase.from('courses').insert(data);
}
```

### Why It's Bad
- No autocomplete in IDE
- Passing wrong data goes unnoticed
- Refactoring is risky (might break things)
- Documentation is missing

### Solution
```typescript
// src/types/Course.types.ts
export type Course = {
  id: string;
  title: string;
  description: string;
  difficulty: 'beginner' | 'intermediate' | 'advanced';
};

// src/hooks/useStudentCourses.ts
export function useStudentCourses(): { courses: Course[] } {
  const [courses, setCourses] = useState<Course[]>([]);
  return { courses };
}

// src/components/student/CourseCard.tsx
interface CourseCardProps {
  course: Course;
  onSelect: (id: string) => void;
}

export function CourseCard({ course, onSelect }: CourseCardProps) {
  return <div onClick={() => onSelect(course.id)}>{course.title}</div>;
}

// src/services/courseService.ts
export async function createCourse(data: Omit<Course, 'id'>): Promise<Course> {
  return supabase.from('courses').insert(data).single();
}
```

### How to Avoid
- ✅ `.cursorrules`: "Every function and component must have TypeScript types"
- ✅ ESLint rule: Enable `noImplicitAny`
- ✅ Prompt: "Add full TypeScript types to this code"

---

## ❌ Mistake 5: Storing Unrelated Data in One Hook

### Problem
```typescript
// WRONG - One hook does too much
export function useStudentData() {
  const [student, setStudent] = useState(null);
  const [courses, setCourses] = useState([]);
  const [enrollments, setEnrollments] = useState([]);
  const [progress, setProgress] = useState(null);
  const [certificates, setCertificates] = useState([]);
  const [notifications, setNotifications] = useState([]);
  // 200+ lines of code
}
```

### Why It's Bad
- Hook does too many things
- Can't use hook for just one piece of data
- Large file size confuses AI
- Hard to test
- Changes to one thing might break another

### Solution
```typescript
// ✅ CORRECT - Separate hooks for separate concerns
export function useStudentProfile() {
  const [student, setStudent] = useState(null);
  // 30 lines
}

export function useStudentCourses() {
  const [courses, setCourses] = useState([]);
  // 30 lines
}

export function useStudentProgress() {
  const [progress, setProgress] = useState(null);
  // 30 lines
}

export function useStudentCertificates() {
  const [certificates, setCertificates] = useState([]);
  // 30 lines
}

// Use multiple hooks in components
export function StudentDashboard() {
  const { student } = useStudentProfile();
  const { courses } = useStudentCourses();
  const { progress } = useStudentProgress();
  const { certificates } = useStudentCertificates();
}
```

### How to Avoid
- ✅ Guideline: "Each hook has ONE responsibility"
- ✅ File size check: "Hooks should be under 100 lines"
- ✅ Prompt: "Create separate hooks for each data type"

---

## ❌ Mistake 6: Not Updating Documentation

### Problem
- You change folder structure but don't update ARCHITECTURE.md
- You change a hook return value but don't update types
- New developer doesn't know what changed
- Cursor gets confused about new patterns

### Solution
- ✅ Update ARCHITECTURE.md when structure changes
- ✅ Update `.cursorrules` when guidelines change
- ✅ Update README.md when setup changes
- ✅ Commit changes together: code + docs

### How to Avoid
- ✅ Include docs in your PR
- ✅ Prompt: "Also update ARCHITECTURE.md with these changes"
- ✅ Code review checklist: Verify docs match code

---

## ❌ Mistake 7: Too Many Contexts

### Problem
```typescript
// WRONG - Context overload
const AuthContext = createContext(null);
const UserContext = createContext(null);
const ThemeContext = createContext(null);
const NotificationContext = createContext(null);
const SettingsContext = createContext(null);  // Too many!

export function App() {
  return (
    <AuthProvider>
      <UserProvider>
        <ThemeProvider>
          <NotificationProvider>
            <SettingsProvider>
              <Main />
            </SettingsProvider>
          </NotificationProvider>
        </ThemeProvider>
      </UserProvider>
    </AuthProvider>
  );
}
```

### Why It's Bad
- Context hell - too many wrappers
- Hard to understand data flow
- Performance issues - unnecessary re-renders
- Context meant for occasionally-used global data

### Solution
```typescript
// ✅ CORRECT - Max 3-4 contexts
const AuthContext = createContext(null);
const AppContext = createContext(null);  // Theme, settings, notifications

export function App() {
  return (
    <AuthProvider>
      <AppProvider>
        <Main />
      </AppProvider>
    </AuthProvider>
  );
}

// Combine into AppContext
interface AppContextType {
  theme: 'light' | 'dark';
  setTheme: (theme: 'light' | 'dark') => void;
  notifications: Notification[];
  addNotification: (n: Notification) => void;
  settings: UserSettings;
  updateSettings: (s: UserSettings) => void;
}
```

### How to Avoid
- ✅ Guideline: "Max 3-4 contexts, use hooks for feature state"
- ✅ Code review: Check context nesting depth
- ✅ Prompt: "Is this global state or feature state?"

---

## ✅ The Golden Rules Summary

1. **Components** = UI only (JSX, styling)
2. **Hooks** = Business logic (state, API calls)
3. **Services** = Utilities (Supabase, calculations)
4. **Types** = Data definitions
5. **Contexts** = Global state (auth, settings only)
6. **Pages** = Container components for routes
7. **Docs** = Keep ARCHITECTURE.md and .cursorrules updated
8. **Types** = Always add TypeScript types
9. **Errors** = Always handle and return errors
10. **Files** = Keep them small (under 200 lines)

Follow these, and Cursor will generate beautiful, maintainable code every single time.

---

## 🎯 Quick Checklist: Before Asking Cursor for Code

- [ ] Is your `.cursorrules` file created and specific?
- [ ] Is your `ARCHITECTURE.md` updated?
- [ ] Are your requirements clear (what, not how)?
- [ ] Did you specify folder paths?
- [ ] Did you mention types?
- [ ] Did you say "follow .cursorrules"?
- [ ] Is your project structure already set up?
- [ ] Did you reference existing similar code?

If all ✅, Cursor will generate perfect code first time.

---

*Last updated: January 2025*
*For questions about this boilerplate, see ARCHITECTURE.md or .cursorrules*
