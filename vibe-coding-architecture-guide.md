# Perfect Vibe Coded App Architecture - Complete Guide

## Overview
This document explains the complete architecture of a properly structured vibe-coded application. It includes all layers, tools, and how they connect together.

---

## 🖥️ Development Environment Layer

### What Sits Here
- **Cursor IDE** - Your AI-assisted code editor
  - Configured with `.cursorrules` file
  - Contains shared coding guidelines
  - Automatically enforces architecture patterns
  
- **Git/GitHub** - Version control
  - Keeps all code changes tracked
  - Enables team collaboration
  
- **Terminal** - Command line
  - Runs `npm run dev`, `npm run build`, `npm run lint`, `npm run type-check`

### Purpose
This is where developers work. The IDE, with proper Cursor Rules, guides the AI to always generate code in the correct structure.

---

## ⚛️ Frontend Architecture Layer

### A. Project Root (Documentation & Configuration)

**Documentation** - The "source of truth" for the project:
```
├── README.md              # How to get started
├── ARCHITECTURE.md        # System design decisions
├── guidelines.md          # Coding guidelines (for Cursor)
└── .cursorrules          # Cursor-specific rules
```

**Configuration** - Tools that control how code is built/linted:
```
├── vite.config.ts        # Build tool configuration
├── tsconfig.json         # TypeScript settings
├── eslint.config.js      # Code quality rules
└── tailwind.config.ts    # CSS utility framework
```

### B. src/ - The Actual Application Code

#### 📄 Pages Layer (Full Page Components)
- `StudentPortal.tsx` - Student dashboard page
- `TrainerDashboard.tsx` - Trainer dashboard page
- `AdminPanel.tsx` - Admin dashboard page

**Purpose:** Each page represents a full screen/route in your app. Pages are just containers that orchestrate everything else.

#### 🧩 Components Layer (Reusable UI)

**student/** - Student-specific UI components:
```
├── PRDGenerator.tsx       # UI for PRD generation form
├── CourseProgress.tsx     # Visual progress tracker
└── VideoPlayer.tsx        # Video playback component
```

**trainer/** - Trainer-specific UI components:
```
├── SessionsTable.tsx      # Table showing sessions
├── StudentsList.tsx       # List of students in session
└── CertificateModal.tsx   # Modal for certificates
```

**admin/** - Admin-specific UI components:
```
├── AnalyticsDashboard.tsx # Charts and metrics
├── UserManagement.tsx     # User admin interface
└── InvoicesTable.tsx      # Invoice listing and management
```

**ui/** - Base UI components (shadcn/ui):
```
├── button.tsx            # Reusable button
├── input.tsx             # Reusable input field
├── modal.tsx             # Modal dialog
└── card.tsx              # Card container
```

**Purpose:** Components only handle UI rendering. They receive data as props and emit events. Zero business logic.

#### 🪝 Service Layer (Business Logic & Data Fetching)

**hooks/** - Custom React hooks with logic:

```
student/
├── useStudentData.ts     # Fetch student profile
├── usePRDGeneration.ts   # Handle PRD generation logic
└── useSessionAccess.ts   # Manage session access

trainer/
├── useTrainerSessions.ts # Fetch and manage trainer's sessions
├── useBilling.ts         # Handle billing calculations
└── useCertification.ts   # Manage certification flow

admin/
├── useAnalytics.ts       # Fetch analytics data
├── useUserManagement.ts  # CRUD operations for users
└── useReporting.ts       # Generate reports
```

**services/** - Pure utility functions (not React hooks):

```
├── authService.ts        # JWT refresh, login, logout
├── apiService.ts         # Centralized API call wrapper
└── utils/
    ├── formatDate.ts     # Format dates consistently
    ├── validateEmail.ts  # Email validation logic
    └── calculateMetrics.ts # Math/computation utilities
```

**Purpose:** This is where ALL business logic lives. Components never touch Supabase directly—they call these hooks.

#### 📋 Types Layer (TypeScript Definitions)

```
├── database.types.ts     # Auto-generated from Supabase schema
└── custom.types.ts       # App-specific types
```

**Purpose:** Ensures type safety across the entire app. Prevents bugs before they happen.

#### 🔄 State Management Layer (Global State)

```
contexts/
├── AuthContext.tsx       # Global auth state
├── UserContext.tsx       # Global user data
└── AppContext.tsx        # Global app settings

stores/ (optional - for complex state)
├── userStore.ts
└── sessionStore.ts
```

**Purpose:** Shares data across multiple components without prop drilling.

#### Entry Points

- `main.tsx` - Initializes React and mounts the app
- `App.tsx` - Sets up routing and provides global contexts

---

## 🔨 Build & Deployment Layer

### Build Process Flow
```
Terminal (npm run build)
    ↓
Vite (bundles code)
    ↓
TypeScript (checks types)
    ↓
ESLint (checks code quality)
    ↓
Output: dist/ folder (optimized, minified code)
    ↓
Deployed to: Vercel/Netlify/AWS
    ↓
Served from CDN globally
```

**Purpose:** Converts your React TypeScript code into optimized HTML/CSS/JS that runs in browsers.

---

## 🗄️ Backend Layer (Supabase)

### A. Authentication
- Email/password login
- OAuth (Google, GitHub, etc.)
- JWT token generation
- Session management

### B. Edge Functions (Serverless Code)
These are Deno-based functions that run on Supabase servers:

```
generate-prd.ts
├── Receives: student prompt + session context
├── Calls: OpenAI API
├── Returns: 3000+ word PRD
└── Updates: database with generated PRD

generate-frontend.ts
├── Receives: PRD + requirements
├── Calls: V0.dev API
├── Returns: React component code
└── Stores: in database for later access

process-payment.ts
├── Receives: payment info
├── Calls: Stripe API
├── Updates: invoices in database
└── Sends: email confirmation
```

**Purpose:** Server-side logic that your frontend calls via REST API.

### C. Row Level Security (RLS)
Database policies that enforce who can see what:

```
✅ Students can only see their own data
✅ Trainers can only see their sessions/students
✅ Admins can see everything
```

### D. PostgreSQL Database
Core data storage:

```
Auth Tables:
├── auth.users       # Managed by Supabase
├── profiles         # User profiles
└── user_roles       # Permission assignments

Data Tables:
├── students         # Student records
├── trainers         # Trainer records
├── sessions         # Training sessions
├── posts            # Generated content
└── certificates     # Certification records

Log Tables:
├── prd_access_log   # Track PRD usage
├── audit_log        # Track all changes
└── rate_limits      # Prevent abuse
```

**Purpose:** Single source of truth for all app data.

---

## 🌐 External Services Layer

### Third-Party Integrations

| Service | Purpose | Used In |
|---------|---------|---------|
| **OpenAI** | AI-powered PRD generation | Edge Functions |
| **V0.dev SDK** | Frontend code generation | Edge Functions |
| **Stripe** | Payment processing | Edge Functions |
| **SendGrid** | Email sending | Edge Functions & Authentication |
| **Google Analytics** | User behavior tracking | Frontend Components |

---

## 🔑 Environment Variables & Secrets

### Frontend (.env.local)
```env
VITE_SUPABASE_URL=https://your-project.supabase.co
VITE_SUPABASE_ANON_KEY=your-public-key
```
These are PUBLIC - visible in browser (that's OK with RLS).

### Backend (Supabase Edge Functions)
```env
OPENAI_API_KEY=sk-...
V0_API_KEY=v0-...
STRIPE_SECRET_KEY=sk_live_...
SUPABASE_SERVICE_ROLE_KEY=...
```
These are SECRET - never exposed to client.

---

## 📊 Data Flow Examples

### Example 1: User Logs In
```
1. User enters email/password in StudentLogin.tsx
2. Component calls authService.login()
3. Service sends request to Supabase Auth
4. Supabase Auth validates credentials
5. Returns JWT token
6. Token stored in AuthContext
7. Component redirects to StudentPortal
```

### Example 2: Fetch Student's Posts
```
1. StudentPortal.tsx renders
2. Calls useStudentData() hook (from hooks/student/)
3. Hook calls supabase.from('posts').select()
4. Supabase applies RLS policy (only user's posts)
5. Returns data to hook
6. Hook manages loading state
7. Component receives posts via destructuring
8. Component renders list using PostItem components
```

### Example 3: Generate PRD
```
1. PRDGenerator.tsx renders form
2. User submits prompt
3. Component calls usePRDGeneration() hook
4. Hook sends request to Supabase Edge Function (generate-prd)
5. Edge Function calls OpenAI API
6. OpenAI generates 3000+ word PRD
7. Edge Function stores result in database
8. Hook returns generated PRD to component
9. Component displays PRD to user
10. Usage tracked in prd_access_log table
```

---

## 🎯 Key Principles of This Architecture

### 1. **Separation of Concerns**
- Pages handle routing
- Components handle UI only
- Hooks handle logic & data
- Services handle utilities & external APIs

### 2. **Single Responsibility**
- Each file has ONE purpose
- Each component does ONE thing
- Each hook manages ONE piece of data/logic

### 3. **Testability**
- Can test hooks independently
- Can test components with mock data
- Can test services without Supabase

### 4. **Reusability**
- Hooks can be used in multiple components
- Services can be used across features
- Components can be composed together

### 5. **Maintainability**
- Change business logic in 1 place (the hook)
- All components automatically get the update
- Easy to find what you're looking for

### 6. **AI-Friendly**
- Cursor can follow the structure automatically
- Prompts are simpler ("Add a loading state" works everywhere)
- Less duplicated code for AI to generate

---

## 🏗️ Typical Feature Build Process with This Architecture

When adding a new feature (e.g., "Export Posts as PDF"):

```
1. Create hook: hooks/student/useExportPDF.ts
   └─ Contains all PDF export logic & Supabase calls

2. Create component: components/student/ExportButton.tsx
   └─ UI only - just renders button & calls hook

3. Add type: custom.types.ts
   └─ Add PDFExportOptions interface

4. Update page: pages/StudentPortal.tsx
   └─ Add <ExportButton /> component

5. Update docs: docs/features/EXPORT_FEATURE.md
   └─ Document the new feature
```

All business logic is centralized. Easy to find, easy to test, easy to modify.

---

## ✅ Checklist: Is Your Project Well-Structured?

- [ ] All pages in `pages/` folder
- [ ] All reusable components in `components/`
- [ ] All logic in `hooks/` and `services/` (NOT in components)
- [ ] TypeScript types in `types/` folder
- [ ] State management in `contexts/` or `stores/`
- [ ] `.cursorrules` file exists at project root
- [ ] `ARCHITECTURE.md` documents the structure
- [ ] No direct Supabase calls in components
- [ ] No business logic in component files
- [ ] All external API calls centralized in Edge Functions or services

---

## 🚀 Getting Started with This Structure

1. **Copy the folder structure** from this guide
2. **Create `.cursorrules`** with your architecture guidelines
3. **Add `ARCHITECTURE.md`** to document your decisions
4. **Start building features** - let Cursor handle consistency
5. **Review PRs** - ensure structure stays clean

The investment in proper structure pays massive dividends as your app grows.

---

*Perfect vibe coding = Fast development + Clean code + Easy maintenance*
