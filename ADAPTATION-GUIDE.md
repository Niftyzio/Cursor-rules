# Adaptation Guide
## How to Customize Generic Boilerplate for Your Specific Project

---

## 📌 Table of Contents

- [Todo App](#-todo-app)
- [Blog/CMS](#-blogcms)
- [E-commerce Store](#-e-commerce-store)
- [Social Media App](#-social-media-app)
- [SaaS Dashboard](#-saas-dashboard)
- [Project Management Tool](#-project-management-tool)
- [Portfolio Website](#-portfolio-website)
- [Your Custom App](#-your-custom-app)

---

## 📝 Todo App

### What to Rename

| Generic | Your App |
|---------|----------|
| `Item` | `Todo` |
| `items` table | `todos` table |
| `ItemList.tsx` | `TodoList.tsx` |
| `useItems.ts` | `useTodos.ts` |
| `itemService.ts` | `todoService.ts` |

### Folder Structure

```
src/components/features/
├── TodoList.tsx           # Show all todos
├── TodoCard.tsx           # Single todo
├── TodoForm.tsx           # Create/edit todo
└── TodoFilters.tsx        # Filter by status

src/hooks/
├── useTodos.ts            # Fetch todos
├── useTodoMutations.ts    # Create/edit/delete
└── useTodoFilters.ts      # Filter logic

src/services/
├── todoService.ts         # Supabase queries
└── utils/
    └── todoHelpers.ts     # Sort, filter logic

src/types/
├── Todo.types.ts
```

### Database Schema

```sql
CREATE TABLE todos (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  title TEXT NOT NULL,
  description TEXT,
  completed BOOLEAN DEFAULT false,
  due_date DATE,
  user_id UUID NOT NULL REFERENCES auth.users(id),
  created_at TIMESTAMP DEFAULT now(),
  updated_at TIMESTAMP DEFAULT now()
);
```

### Example Code

**types/Todo.types.ts**
```typescript
export type Todo = {
  id: string;
  title: string;
  description: string;
  completed: boolean;
  due_date: string | null;
  user_id: string;
  created_at: string;
  updated_at: string;
};

export type CreateTodoInput = Omit<Todo, 'id' | 'created_at' | 'updated_at' | 'user_id'>;
```

**services/todoService.ts**
```typescript
export const todoService = {
  async getTodos(userId: string): Promise<Todo[]> {
    const { data, error } = await supabase
      .from('todos')
      .select('*')
      .eq('user_id', userId)
      .order('created_at', { ascending: false });
    
    if (error) throw new Error(error.message);
    return data || [];
  },

  async createTodo(todo: CreateTodoInput, userId: string): Promise<Todo> {
    const { data, error } = await supabase
      .from('todos')
      .insert([{ ...todo, user_id: userId }])
      .select()
      .single();
    
    if (error) throw new Error(error.message);
    return data;
  },

  async updateTodo(id: string, updates: Partial<Todo>): Promise<Todo> {
    const { data, error } = await supabase
      .from('todos')
      .update(updates)
      .eq('id', id)
      .select()
      .single();
    
    if (error) throw new Error(error.message);
    return data;
  },

  async deleteTodo(id: string): Promise<void> {
    const { error } = await supabase
      .from('todos')
      .delete()
      .eq('id', id);
    
    if (error) throw new Error(error.message);
  },
};
```

**hooks/useTodos.ts**
```typescript
export function useTodos() {
  const { user } = useAuth();
  const [todos, setTodos] = useState<Todo[]>([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);

  useEffect(() => {
    if (!user) return;
    
    const fetch = async () => {
      try {
        const data = await todoService.getTodos(user.id);
        setTodos(data);
      } catch (err) {
        setError(err.message);
      } finally {
        setLoading(false);
      }
    };
    fetch();
  }, [user]);

  return { todos, loading, error };
}
```

---

## 📰 Blog/CMS

### What to Rename

| Generic | Blog |
|---------|------|
| `Item` | `Post` |
| `items` table | `posts` table |
| `ItemList.tsx` | `PostList.tsx` |
| `useItems.ts` | `usePosts.ts` |
| `itemService.ts` | `postService.ts` |

### Folder Structure

```
src/components/features/
├── PostList.tsx           # All posts
├── PostCard.tsx           # Post preview
├── PostDetail.tsx         # Full post
├── PostForm.tsx           # Create/edit
├── PostComments.tsx       # Comments section
└── CommentForm.tsx        # Add comment

src/hooks/
├── usePosts.ts            # Fetch posts
├── usePost.ts             # Single post detail
├── useComments.ts         # Fetch comments
└── usePostMutations.ts    # Create/edit/delete

src/services/
├── postService.ts         # Post queries
├── commentService.ts      # Comment queries
└── utils/
    └── markdownHelpers.ts # Parse markdown
```

### Database Schema

```sql
CREATE TABLE posts (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  title TEXT NOT NULL,
  slug TEXT UNIQUE NOT NULL,
  content TEXT NOT NULL,
  excerpt TEXT,
  featured_image_url TEXT,
  author_id UUID NOT NULL REFERENCES auth.users(id),
  published BOOLEAN DEFAULT false,
  created_at TIMESTAMP DEFAULT now(),
  updated_at TIMESTAMP DEFAULT now()
);

CREATE TABLE comments (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  content TEXT NOT NULL,
  post_id UUID NOT NULL REFERENCES posts(id) ON DELETE CASCADE,
  author_id UUID NOT NULL REFERENCES auth.users(id),
  created_at TIMESTAMP DEFAULT now()
);
```

---

## 🛍️ E-commerce Store

### What to Rename

| Generic | Store |
|---------|-------|
| `Item` | `Product` |
| `items` table | `products` table |
| `ItemList.tsx` | `ProductList.tsx` |
| `useItems.ts` | `useProducts.ts` |
| `itemService.ts` | `productService.ts` |

### Folder Structure

```
src/components/features/
├── ProductList.tsx        # All products
├── ProductCard.tsx        # Product preview
├── ProductDetail.tsx      # Full product details
├── ProductFilters.tsx     # Filter/search
├── ProductReviews.tsx     # Reviews section
├── CartItem.tsx           # Cart item
└── Checkout.tsx           # Checkout form

src/hooks/
├── useProducts.ts         # Fetch products
├── useProduct.ts          # Single product
├── useCart.ts             # Shopping cart state
├── useReviews.ts          # Product reviews
└── useOrder.ts            # Orders

src/services/
├── productService.ts
├── cartService.ts
├── orderService.ts
├── reviewService.ts
└── paymentService.ts      # Stripe integration
```

### Database Schema

```sql
CREATE TABLE products (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  name TEXT NOT NULL,
  description TEXT,
  price DECIMAL(10, 2) NOT NULL,
  stock INTEGER DEFAULT 0,
  image_urls TEXT[],
  category TEXT,
  rating DECIMAL(2, 1),
  created_at TIMESTAMP DEFAULT now(),
  updated_at TIMESTAMP DEFAULT now()
);

CREATE TABLE orders (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID NOT NULL REFERENCES auth.users(id),
  total DECIMAL(10, 2) NOT NULL,
  status TEXT DEFAULT 'pending',
  created_at TIMESTAMP DEFAULT now()
);

CREATE TABLE order_items (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  order_id UUID NOT NULL REFERENCES orders(id),
  product_id UUID NOT NULL REFERENCES products(id),
  quantity INTEGER NOT NULL,
  price DECIMAL(10, 2) NOT NULL
);
```

---

## 👥 Social Media App

### What to Rename

| Generic | Social |
|---------|--------|
| `Item` | `Post` |
| `items` table | `posts` table |
| `ItemCard.tsx` | `PostCard.tsx` |
| Multiple domains: User, Post, Comment, Like |

### Folder Structure

```
src/components/features/
├── PostList.tsx
├── PostCard.tsx
├── PostForm.tsx           # Create post
├── UserProfile.tsx        # User profile
├── UserFollowButton.tsx
├── Comments.tsx
├── LikeButton.tsx
├── Notifications.tsx
└── Feed.tsx

src/hooks/
├── usePosts.ts
├── useUsers.ts
├── useFollows.ts
├── useLikes.ts
├── useComments.ts
├── useNotifications.ts
└── useSearch.ts

src/services/
├── postService.ts
├── userService.ts
├── followService.ts
├── likeService.ts
├── commentService.ts
├── notificationService.ts
└── searchService.ts
```

### Database Schema

```sql
CREATE TABLE users (
  id UUID PRIMARY KEY REFERENCES auth.users(id),
  username TEXT UNIQUE NOT NULL,
  display_name TEXT,
  bio TEXT,
  avatar_url TEXT,
  created_at TIMESTAMP DEFAULT now()
);

CREATE TABLE posts (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID NOT NULL REFERENCES users(id),
  content TEXT NOT NULL,
  image_urls TEXT[],
  created_at TIMESTAMP DEFAULT now()
);

CREATE TABLE follows (
  follower_id UUID NOT NULL REFERENCES users(id),
  following_id UUID NOT NULL REFERENCES users(id),
  created_at TIMESTAMP DEFAULT now()
);

CREATE TABLE likes (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID NOT NULL REFERENCES users(id),
  post_id UUID NOT NULL REFERENCES posts(id),
  created_at TIMESTAMP DEFAULT now()
);

CREATE TABLE comments (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID NOT NULL REFERENCES users(id),
  post_id UUID NOT NULL REFERENCES posts(id),
  content TEXT NOT NULL,
  created_at TIMESTAMP DEFAULT now()
);
```

---

## 📊 SaaS Dashboard

### What to Rename

| Generic | SaaS |
|---------|------|
| `Item` | `Project` or `Organization` |
| Multiple entities: User, Workspace, Project, Team, Metrics |

### Folder Structure

```
src/components/features/
├── ProjectList.tsx
├── ProjectCard.tsx
├── ProjectForm.tsx
├── TeamMembers.tsx
├── MetricsDashboard.tsx
├── SettingsForm.tsx
├── BillingInfo.tsx
└── InviteUser.tsx

src/pages/
├── DashboardPage.tsx      # Main dashboard
├── ProjectsPage.tsx       # Projects list
├── TeamPage.tsx           # Team management
├── SettingsPage.tsx       # Settings
└── BillingPage.tsx        # Billing

src/hooks/
├── useProjects.ts
├── useTeam.ts
├── useMetrics.ts
├── useInvitations.ts
├── useBilling.ts
└── useWorkspace.ts
```

### Database Schema

```sql
CREATE TABLE workspaces (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  name TEXT NOT NULL,
  owner_id UUID NOT NULL REFERENCES auth.users(id),
  plan TEXT DEFAULT 'free',
  created_at TIMESTAMP DEFAULT now()
);

CREATE TABLE projects (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  workspace_id UUID NOT NULL REFERENCES workspaces(id),
  name TEXT NOT NULL,
  description TEXT,
  created_at TIMESTAMP DEFAULT now()
);

CREATE TABLE team_members (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  workspace_id UUID NOT NULL REFERENCES workspaces(id),
  user_id UUID NOT NULL REFERENCES auth.users(id),
  role TEXT DEFAULT 'member',
  created_at TIMESTAMP DEFAULT now()
);

CREATE TABLE metrics (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  workspace_id UUID NOT NULL REFERENCES workspaces(id),
  key TEXT NOT NULL,
  value INTEGER,
  created_at TIMESTAMP DEFAULT now()
);
```

---

## 📋 Project Management Tool

### What to Rename

| Generic | Project Mgmt |
|---------|--------------|
| `Item` | `Task` |
| Multiple: Task, Project, Team, Sprint, Comment |

### Folder Structure

```
src/components/features/
├── TaskList.tsx
├── TaskCard.tsx
├── TaskForm.tsx
├── TaskDetail.tsx
├── ProjectBoard.tsx       # Kanban board
├── TeamAssignment.tsx
├── CommentThread.tsx
└── AttachmentUpload.tsx

src/hooks/
├── useTasks.ts
├── useProject.ts
├── useTeam.ts
├── useComments.ts
├── useAttachments.ts
└── useBoard.ts

src/services/
├── taskService.ts
├── projectService.ts
├── teamService.ts
├── commentService.ts
└── attachmentService.ts
```

---

## 🎨 Portfolio Website

### What to Rename

| Generic | Portfolio |
|---------|-----------|
| `Item` | `Project` |
| Simpler structure - mostly static with a bit of dynamic content |

### Folder Structure

```
src/components/features/
├── ProjectCard.tsx
├── ProjectList.tsx
├── ProjectDetail.tsx
├── SkillsBadge.tsx
└── ContactForm.tsx

src/pages/
├── Home.tsx
├── Projects.tsx
├── ProjectDetail.tsx
├── About.tsx
└── Contact.tsx

src/hooks/
├── useProjects.ts
└── useContactForm.ts
```

### Database Schema

```sql
CREATE TABLE portfolio_projects (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  title TEXT NOT NULL,
  description TEXT,
  image_url TEXT,
  project_url TEXT,
  github_url TEXT,
  technologies TEXT[],
  featured BOOLEAN DEFAULT false,
  created_at TIMESTAMP DEFAULT now()
);

CREATE TABLE contact_messages (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  email TEXT NOT NULL,
  name TEXT NOT NULL,
  message TEXT NOT NULL,
  created_at TIMESTAMP DEFAULT now()
);
```

---

## 🎯 Your Custom App

### Step-by-Step Adaptation

#### 1. Identify the Main Entity
**Question:** What is the main "thing" in my app?
- Blog → Post
- Store → Product
- Social → Post/User
- Todo → Todo
- Project Manager → Task
- CMS → Article/Page

#### 2. Create a Mapping Table

| Generic | Your App |
|---------|----------|
| `Item` | `[YourEntity]` |
| `items` | `[pluralOfYourEntity]` |
| `ItemList.tsx` | `[YourEntity]List.tsx` |
| `useItems.ts` | `use[YourEntities].ts` |
| `itemService.ts` | `[yourEntity]Service.ts` |

#### 3. Rename All Files
```bash
# Component
mv Item.tsx [YourEntity].tsx

# Hook
mv useItems.ts use[YourEntities].ts

# Service
mv itemService.ts [yourEntity]Service.ts

# Types
mv Item.types.ts [YourEntity].types.ts
```

#### 4. Update Content
1. Replace all "Item" → "[YourEntity]"
2. Replace all "items" → "[pluralOfYourEntity]"
3. Update types to match your data structure
4. Update database schema
5. Update service queries

#### 5. Add Additional Entities
If you have multiple main entities:

```
// Example: Social Media
Components: Post, User, Comment
Hooks: usePosts, useUsers, useComments
Services: postService, userService, commentService
Types: Post.types.ts, User.types.ts, Comment.types.ts
```

---

## 📝 Checklist: After Adaptation

- [ ] All files renamed consistently
- [ ] All "Item" references replaced
- [ ] All "items" table names updated
- [ ] Types match your data structure
- [ ] Database schema created
- [ ] Services implement your queries
- [ ] Hooks use the right services
- [ ] Components render the right data
- [ ] Pages import correct components
- [ ] .cursorrules updated if needed

---

## 💡 Pro Tips

### Tip 1: Start with One Entity
Don't rename everything at once. 
1. Pick your main entity
2. Fully implement it
3. Add other entities one by one

### Tip 2: Use Find & Replace
```
Find: Item
Replace: [YourEntity]

Find: items
Replace: [pluralOfYourEntity]

Find: useItems
Replace: use[YourEntities]
```

### Tip 3: Keep Generic Utilities
Don't rename generic utilities:
- `useAuth.ts` → stays the same
- `useLocalStorage.ts` → stays the same
- `formatters.ts` → stays the same
- `validators.ts` → stays the same

### Tip 4: Create Multiple Service Domains
If your app has multiple domains:

```
services/
├── userService.ts          # User operations
├── postService.ts          # Post operations
├── commentService.ts       # Comment operations
├── apiService.ts           # Centralized wrapper
└── utils/
    ├── formatters.ts
    ├── validators.ts
    └── helpers.ts
```

---

## 🚀 Quick Start Template

**For your specific app:**

1. Choose main entity: `[YourEntity]`
2. Create type: `src/types/[YourEntity].types.ts`
3. Create service: `src/services/[yourEntity]Service.ts`
4. Create hook: `src/hooks/use[YourEntities].ts`
5. Create component: `src/components/features/[YourEntity].tsx`
6. Add to page: `src/pages/[Page].tsx`
7. Repeat for other entities

---

## 📚 Related Files

- [GENERIC-BOILERPLATE-README.md](./GENERIC-BOILERPLATE-README.md) - The generic version
- [QUICK-REFERENCE-CARD.md](./QUICK-REFERENCE-CARD.md) - Generic templates
- [ai-coding-boilerplate-guide.md](./ai-coding-boilerplate-guide.md) - Full guide

---

**Pick your project type above and start adapting!** 🚀

---

*Adaptation Guide - Customize Generic Boilerplate for Any App*
*Created: January 2025*
