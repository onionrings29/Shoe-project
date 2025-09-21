# Component Structure - Shoe Inventory Website

## Frontend Component Architecture

### Core Layout Components
```
src/components/layout/
├── Header.tsx           # Site header with navigation
├── Footer.tsx           # Site footer
├── Layout.tsx          # Main layout wrapper
├── Sidebar.tsx         # Admin sidebar navigation
└── AuthGuard.tsx       # Route protection for admin
```

### Public Website Components
```
src/components/public/
├── ShoeCard.tsx        # Individual shoe display card
├── ShoeGrid.tsx        # Grid layout for shoe listings
├── ShoeFilter.tsx      # Filtering and search interface
├── ShoeDetail.tsx      # Detailed shoe view page
├── ImageGallery.tsx    # Image carousel for shoe details
└── SearchBar.tsx       # Global search functionality
```

### Admin Interface Components
```
src/components/admin/
├── AdminDashboard.tsx  # Admin overview and stats
├── ShoeManager.tsx     # CRUD operations for shoes
├── ImageUploader.tsx   # Drag & drop image upload
├── BulkImporter.tsx    # Bulk CSV/JSON import
├── UserManager.tsx     # Admin user management
└── Analytics.tsx       # Sales and traffic analytics
```

### Shared UI Components
```
src/components/ui/
├── Button.tsx          # Reusable button component
├── Modal.tsx           # Modal dialog component
├── LoadingSpinner.tsx  # Loading indicators
├── ErrorBoundary.tsx   # Error handling
├── Toast.tsx           # Notification system
└── FormElements.tsx    # Input, Select, Textarea
```

## Firebase Services Structure
```
src/services/
├── firebase.ts         # Firebase configuration
├── auth.ts            # Authentication methods
├── firestore.ts       # Firestore database operations
├── storage.ts         # Firebase Storage operations
├── analytics.ts       # Analytics tracking
└── types.ts           # TypeScript type definitions
```

## Page Components
```
src/pages/
├── Home.tsx           # Landing page with featured shoes
├── Catalog.tsx        # Full shoe catalog
├── ShoeDetailPage.tsx # Individual shoe page
├── AdminLogin.tsx     # Admin authentication
├── AdminDashboard.tsx # Admin control panel
├── ManageShoes.tsx    # Shoe management interface
└── UploadImages.tsx   # Image upload interface
```

## TypeScript Type Definitions
```typescript
// src/types/shoe.ts
interface Shoe {
  id: string;
  brand: string;
  model: string;
  size: number;
  color: string;
  condition: 'new' | 'used' | 'refurbished';
  price: number;
  description: string;
  imageUrls: string[];
  sku?: string;
  inventoryCount: number;
  createdAt: Timestamp;
  updatedAt: Timestamp;
  tags: string[];
  category: string;
}

// src/types/image.ts
interface ShoeImage {
  id: string;
  shoeId: string;
  imageUrl: string;
  storagePath: string;
  isPrimary: boolean;
  uploadedAt: Timestamp;
  filename: string;
  size: number;
}

// src/types/user.ts
interface AdminUser {
  uid: string;
  email: string;
  displayName: string;
  role: 'admin' | 'editor';
  createdAt: Timestamp;
  lastLogin: Timestamp;
}
```

## State Management Structure
```
src/context/
├── AuthContext.tsx     # Authentication state
├── ShoeContext.tsx     # Shoe data state
├── UIContext.tsx       # UI state (modals, loading)
└── CartContext.tsx     # Shopping cart state (if e-commerce)
```

## Utility Functions
```
src/utils/
├── formatters.ts       # Date, currency formatting
├── validators.ts       # Form validation
├── imageUtils.ts       # Image processing helpers
├── firebaseUtils.ts    # Firebase-specific helpers
├── errorHandling.ts    # Error handling utilities
└── constants.ts        # App constants and config
```

## Routing Structure
```typescript
// Public routes
const publicRoutes = [
  { path: '/', component: Home },
  { path: '/catalog', component: Catalog },
  { path: '/shoe/:id', component: ShoeDetailPage },
  { path: '/about', component: About },
  { path: '/contact', component: Contact }
];

// Admin routes (protected)
const adminRoutes = [
  { path: '/admin', component: AdminDashboard },
  { path: '/admin/shoes', component: ManageShoes },
  { path: '/admin/upload', component: UploadImages },
  { path: '/admin/users', component: UserManager }
];
```

## Responsive Breakpoints
```css
/* Tailwind CSS breakpoints */
sm: 640px    /* Mobile */
md: 768px    /* Tablet */
lg: 1024px   /* Laptop */
xl: 1280px   /* Desktop */
2xl: 1536px  /* Large desktop */
```

## Component Props Interface Examples
```typescript
// ShoeCard component props
interface ShoeCardProps {
  shoe: Shoe;
  onEdit?: (shoe: Shoe) => void;
  onDelete?: (id: string) => void;
  showAdminControls?: boolean;
  className?: string;
}

// ImageUploader component props
interface ImageUploaderProps {
  shoeId?: string;
  onUploadComplete: (imageUrl: string) => void;
  multiple?: boolean;
  maxSize?: number;
  acceptedTypes?: string[];
}
```

## File Structure Summary
```
shoe-inventory/
├── frontend/
│   ├── public/                 # Static assets
│   ├── src/
│   │   ├── components/         # Reusable components
│   │   ├── pages/             # Page components
│   │   ├── services/          # Firebase services
│   │   ├── context/           # React context
│   │   ├── utils/             # Utility functions
│   │   ├── types/             # TypeScript types
│   │   └── styles/            # Global styles
│   ├── package.json
│   └── vite.config.ts
├── backend/                   # Python backend
├── docs/                     # Documentation
├── scripts/                  # Deployment scripts
└── planning/                 # Project planning
```

This component structure provides a solid foundation for your shoe inventory website, ensuring scalability, maintainability, and a clean separation of concerns between different parts of the application.