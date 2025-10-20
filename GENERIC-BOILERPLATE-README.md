# Generic AI-Assisted Development Boilerplate
## Works with ANY React + Vite + TypeScript + Supabase App

---

## ⚠️ Important Note

This is a **completely generic version** of the boilerplate that works for ANY application.

- ✅ Works for: Todo apps, blogs, e-commerce, SaaS, games, social apps, dashboards
- ✅ No project-specific references
- ✅ Pure architectural patterns
- ✅ Generic examples (users, items, features)
- ✅ Easy to adapt to your specific project

**If you want to see how to adapt this to your specific project type, see [ADAPTATION-GUIDE.md](./ADAPTATION-GUIDE.md)**

---

## 📦 What's Different

### Original Boilerplate (Project-Specific)
```
src/components/
├── student/          ← Role-specific
├── trainer/          ← Role-specific
└── admin/            ← Role-specific

src/hooks/
├── student/
├── trainer/
└── admin/
```

### Generic Boilerplate (Any App)
```
src/components/
├── features/         ← Feature-based, any app
├── ui/               ← Base UI components
└── layout/           ← Shared layout components

src/hooks/
├── useFeatureData.ts    ← Generic naming
├── useFeatureMutations.ts
└── shared/              ← Truly shared hooks
```

---

## 🎯 How to Use This Generic Boilerplate

### Step 1: Choose Your Project Type
- ✅ Todo app
- ✅ Blog/CMS
- ✅ E-commerce store
- ✅ Social media
- ✅ SaaS application
- ✅ Admin dashboard
- ✅ Portfolio site
- ✅ Any other app

### Step 2: Read This Entire File
Understand the generic patterns

### Step 3: See [ADAPTATION-GUIDE.md](./ADAPTATION-GUIDE.md)
Learn how to customize for your specific project

### Step 4: Build Your Project
Use the generic templates as a starting point

---

## 📁 Generic Folder Structure

```
my-awesome-app/
│
├── 📚 DOCUMENTATION & CONFIG
│   ├── .cursorrules                ← Generic AI guidelines
│   ├── README.md
│   ├── ARCHITECTURE.md
│   ├── .env.example
│   ├── package.json
│   ├── tsconfig.json
│   ├── vite.config.ts
│   └── eslint.config.js
│
├── 💻 SRC - APPLICATION CODE
│   │
│   ├── 📄 CORE
│   │   ├── main.tsx
│   │   ├── App.tsx
│   │   ├── App.css
│   │   └── index.css
│   │
│   ├── 📄 PAGES
│   │   ├── Home.tsx              ← Not role-specific
│   │   ├── DashboardPage.tsx      ← Descriptive, not "admin"
│   │   ├── DetailsPage.tsx
│   │   └── NotFound.tsx
│   │
│   ├── 🧩 COMPONENTS
│   │   ├── features/
│   │   │   ├── ItemList.tsx       ← Generic naming
│   │   │   ├── ItemCard.tsx
│   │   │   ├── ItemForm.tsx
│   │   │   └── ItemDetails.tsx
│   │   │
│   │   └── ui/
│   │       ├── button.tsx
│   │       ├── input.tsx
│   │       ├── modal.tsx
│   │       └── ... (shadcn/ui components)
│   │
│   ├── 🪝 HOOKS
│   │   ├── useItems.ts            ← Generic data fetching
│   │   ├── useItemMutations.ts    ← Generic mutations
│   │   ├── useAuth.ts
│   │   └── shared/
│   │       ├── useLocalStorage.ts
│   │       └── useDebounce.ts
│   │
│   ├── 🛠️ SERVICES
│   │   ├── itemService.ts         ← Generic domain
│   │   ├── authService.ts
│   │   ├── apiService.ts
│   │   └── utils/
│   │       ├── formatters.ts
│   │       ├── validators.ts
│   │       └── helpers.ts
│   │
│   ├── 📋 TYPES
│   │   ├── database.types.ts
│   │   ├── Item.types.ts          ← Generic domain types
│   │   ├── User.types.ts
│   │   └── common.types.ts
│   │
│   ├── 🔄 CONTEXTS
│   │   ├── AuthContext.tsx
│   │   └── AppContext.tsx
│   │
│   └── 🔗 LIB
│       └── supabase.ts
│
└── 📱 PUBLIC
    └── assets/
```

---

## 🧩 Generic Component Template

```typescript
// src/components/features/Item.tsx
import type { ItemProps } from '@/types/Item.types';

export function Item({ item, onAction }: ItemProps) {
  return (
    <div className="border rounded-lg p-4">
      <h3 className="font-bold">{item.name}</h3>
      <p className="text-gray-600">{item.description}</p>
      <button onClick={() => onAction(item.id)}>
        Action
      </button>
    </div>
  );
}
```

**This works for:**
- Posts (blog)
- Products (e-commerce)
- Todos (todo app)
- Users (social app)
- Tasks (project management)
- Any other "item"

---

## 🪝 Generic Hook Template

```typescript
// src/hooks/useItems.ts
import { useEffect, useState } from 'react';
import type { Item } from '@/types/Item.types';
import { itemService } from '@/services/itemService';

export function useItems() {
  const [items, setItems] = useState<Item[]>([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);

  useEffect(() => {
    const fetch = async () => {
      try {
        const data = await itemService.getItems();
        setItems(data);
      } catch (err) {
        setError(err instanceof Error ? err.message : 'Unknown error');
      } finally {
        setLoading(false);
      }
    };
    fetch();
  }, []);

  return { items, loading, error };
}
```

**Rename "Item" to:**
- Post (blog)
- Product (store)
- Todo (todo app)
- User (social)
- Task (project management)
- Anything else

---

## 🛠️ Generic Service Template

```typescript
// src/services/itemService.ts
import { supabase } from '@/lib/supabase';
import type { Item } from '@/types/Item.types';

export const itemService = {
  async getItems(): Promise<Item[]> {
    const { data, error } = await supabase
      .from('items')
      .select('*');
    
    if (error) throw new Error(error.message);
    return data || [];
  },

  async createItem(item: Omit<Item, 'id' | 'created_at'>): Promise<Item> {
    const { data, error } = await supabase
      .from('items')
      .insert([item])
      .select()
      .single();
    
    if (error) throw new Error(error.message);
    return data;
  },
};
```

**Rename "items" table to:**
- posts
- products
- todos
- users
- tasks
- Whatever your domain is

---

## 📋 Generic Type Template

```typescript
// src/types/Item.types.ts
export type Item = {
  id: string;
  name: string;
  description: string;
  created_at: string;
  updated_at: string;
};

export type CreateItemInput = Omit<Item, 'id' | 'created_at' | 'updated_at'>;

export interface ItemProps {
  item: Item;
  onAction: (id: string) => void;
}
```

---

## 🎯 Generic Page Structure

```typescript
// src/pages/ItemsPage.tsx
import { useItems } from '@/hooks/useItems';
import { ItemList } from '@/components/features/ItemList';

export function ItemsPage() {
  const { items, loading, error } = useItems();

  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error}</div>;

  return (
    <div className="p-8">
      <h1 className="text-2xl font-bold mb-4">Items</h1>
      <ItemList items={items} />
    </div>
  );
}
```

---

## 🔑 Generic Naming Conventions

| What | Example | Rename To |
|------|---------|-----------|
| Components | `Item.tsx` | `Post.tsx`, `Product.tsx`, `Todo.tsx` |
| Hooks | `useItems.ts` | `usePosts.ts`, `useProducts.ts` |
| Services | `itemService.ts` | `postService.ts`, `productService.ts` |
| Types | `Item.types.ts` | `Post.types.ts`, `Product.types.ts` |
| Tables | `items` | `posts`, `products`, `todos` |
| Folders | `features/` | Keep generic or name by domain |

---

## 💡 Customization Examples

### For a Blog App
```
Replace "Item" with "Post"
- Item.tsx → Post.tsx
- useItems.ts → usePosts.ts
- itemService.ts → postService.ts
- items table → posts table
```

### For an E-commerce Store
```
Replace "Item" with "Product"
- Item.tsx → Product.tsx
- useItems.ts → useProducts.ts
- itemService.ts → productService.ts
- items table → products table
```

### For a Todo App
```
Replace "Item" with "Todo"
- Item.tsx → Todo.tsx
- useItems.ts → useTodos.ts
- itemService.ts → todoService.ts
- items table → todos table
```

---

## 🏗️ Generic Architecture Layers

```
Pages (Routes)
    ↓
Components (Generic features - Item, Post, Product, etc.)
    ↓
Hooks (useItems, usePosts, useProducts, etc.)
    ↓
Services (itemService, postService, productService, etc.)
    ↓
Supabase (items, posts, products tables)
```

**This pattern works for ANY app.**

---

## 🚀 Quick Start with Generic Boilerplate

1. **Create your project**
   ```bash
   npm create vite@latest my-app -- --template react-ts
   cd my-app
   npm install
   ```

2. **Copy the folder structure** from [Folder Structure section](#-generic-folder-structure)

3. **Choose your domain name**
   - What is the main "thing" in your app?
   - Blog → "Post"
   - Shop → "Product"
   - Todo → "Todo"
   - Social → "Post" or "User"

4. **Rename everything**
   - Replace "Item" with your chosen name
   - Replace "items" (table) with your chosen name

5. **Create your types** in `src/types/[YourThing].types.ts`

6. **Create your service** in `src/services/[yourThing]Service.ts`

7. **Create your hooks** in `src/hooks/use[YourThings].ts`

8. **Create your components** in `src/components/features/[YourThing].tsx`

9. **Add to your pages** in `src/pages/[Page].tsx`

---

## 📚 See Also

- **[ADAPTATION-GUIDE.md](./ADAPTATION-GUIDE.md)** - How to customize for specific project types
- **[QUICK-REFERENCE-CARD.md](./QUICK-REFERENCE-CARD.md)** - Generic templates to copy
- **[ai-coding-boilerplate-guide.md](./ai-coding-boilerplate-guide.md)** - Detailed explanations (ignore project-specific examples)

---

## ✨ Generic vs Project-Specific

### Generic (This File)
- ✅ Works for any app
- ✅ No role-specific folders (student, trainer, admin)
- ✅ Uses generic names (Item, Feature)
- ✅ Easy to customize
- ✅ Universal patterns

### Project-Specific (Original Boilerplate)
- ✅ Already customized for specific project
- ✅ Role-specific folders (student, trainer, admin)
- ✅ Domain-specific names (Course, PRD, Certificate)
- ✅ Ready to use without modification
- ✅ Faster if building exact same type of app

---

## 🎯 When to Use Each

**Use Generic Boilerplate When:**
- ✅ Building a new type of app
- ✅ Creating a todo app, blog, store, etc.
- ✅ Want to learn the pattern
- ✅ Building for a different team/client

**Use Project-Specific Boilerplate When:**
- ✅ Building similar training/education platforms
- ✅ Need role-based access (student, trainer, admin)
- ✅ Similar business logic
- ✅ Learning from real example

---

## 🔄 Converting Project-Specific to Your App

1. Copy the generic version (this file)
2. Rename all "Item" references to your domain
3. Replace database table names
4. Update folder structure if needed
5. Customize types for your specific needs

**Total time: ~30 minutes**

---

## 💪 You're Ready!

Pick a project type. Customize the names. Start building.

The architecture is the same. Only the names change.

---

**Next: See [ADAPTATION-GUIDE.md](./ADAPTATION-GUIDE.md) for your specific project type** →

---

*Generic Boilerplate - Works with ANY app*
*Created: January 2025*
