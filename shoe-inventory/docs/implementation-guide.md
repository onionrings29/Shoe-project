# Implementation Guide - Shoe Inventory Website

## Phase 1: React + TypeScript Frontend Setup

### Step 1: Initialize React Project
```bash
# Create new React project with Vite
npm create vite@latest shoe-inventory-frontend -- --template react-ts
cd shoe-inventory-frontend

# Install dependencies
npm install
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p

# Install Firebase dependencies
npm install firebase
npm install react-router-dom
npm install @types/react-router-dom

# Install UI components
npm install class-variance-authority clsx tailwind-merge lucide-react
```

### Step 2: Configure Tailwind CSS
Update `tailwind.config.js`:
```js
/** @type {import('tailwindcss').Config} */
export default {
  content: [
    "./index.html",
    "./src/**/*.{js,ts,jsx,tsx}",
  ],
  theme: {
    extend: {
      colors: {
        primary: {
          50: '#fef2f2',
          500: '#ef4444',
          900: '#7f1d1d',
        }
      }
    },
  },
  plugins: [],
}
```

### Step 3: Firebase Configuration
Create `src/services/firebase.ts`:
```typescript
import { initializeApp } from 'firebase/app'
import { getFirestore } from 'firebase/firestore'
import { getStorage } from 'firebase/storage'
import { getAuth } from 'firebase/auth'

const firebaseConfig = {
  apiKey: import.meta.env.VITE_FIREBASE_API_KEY,
  authDomain: "cardcapture-a4dd3.firebaseapp.com",
  projectId: "cardcapture-a4dd3",
  storageBucket: "cardcapture-a4dd3.firebasestorage.app",
  messagingSenderId: "112775655709157944961",
  appId: "your-app-id"
}

const app = initializeApp(firebaseConfig)
export const db = getFirestore(app)
export const storage = getStorage(app)
export const auth = getAuth(app)
```

### Step 4: Environment Variables
Create `.env` file:
```
VITE_FIREBASE_API_KEY=your_api_key_here
VITE_FIREBASE_AUTH_DOMAIN=cardcapture-a4dd3.firebaseapp.com
VITE_FIREBASE_PROJECT_ID=cardcapture-a4dd3
VITE_FIREBASE_STORAGE_BUCKET=cardcapture-a4dd3.firebasestorage.app
```

### Step 5: TypeScript Types
Create `src/types/shoe.ts`:
```typescript
export interface Shoe {
  id: string
  brand: string
  model: string
  size: number
  color: string
  condition: 'new' | 'used' | 'refurbished'
  price: number
  description: string
  imageUrls: string[]
  createdAt: Date
  updatedAt: Date
  sku?: string
  inventoryCount: number
  tags: string[]
  category: string
}

export interface ShoeImage {
  id: string
  shoeId: string
  imageUrl: string
  storagePath: string
  isPrimary: boolean
  uploadedAt: Date
  filename: string
  size: number
}
```

### Step 6: Firebase Services
Create `src/services/firestore.ts`:
```typescript
import { 
  collection, 
  doc, 
  getDocs, 
  getDoc, 
  addDoc, 
  updateDoc, 
  deleteDoc,
  query,
  where,
  orderBy,
  limit 
} from 'firebase/firestore'
import { db } from './firebase'
import { Shoe } from '../types/shoe'

export const shoeService = {
  // Get all shoes
  async getShoes(): Promise<Shoe[]> {
    const querySnapshot = await getDocs(collection(db, 'shoes'))
    return querySnapshot.docs.map(doc => ({
      id: doc.id,
      ...doc.data()
    })) as Shoe[]
  },

  // Get shoe by ID
  async getShoe(id: string): Promise<Shoe | null> {
    const docRef = doc(db, 'shoes', id)
    const docSnap = await getDoc(docRef)
    return docSnap.exists() ? { id: docSnap.id, ...docSnap.data() } as Shoe : null
  },

  // Add new shoe
  async addShoe(shoe: Omit<Shoe, 'id'>): Promise<string> {
    const docRef = await addDoc(collection(db, 'shoes'), {
      ...shoe,
      createdAt: new Date(),
      updatedAt: new Date()
    })
    return docRef.id
  },

  // Update shoe
  async updateShoe(id: string, updates: Partial<Shoe>): Promise<void> {
    const docRef = doc(db, 'shoes', id)
    await updateDoc(docRef, {
      ...updates,
      updatedAt: new Date()
    })
  },

  // Delete shoe
  async deleteShoe(id: string): Promise<void> {
    await deleteDoc(doc(db, 'shoes', id))
  }
}
```

### Step 7: Image Upload Service
Create `src/services/storage.ts`:
```typescript
import { 
  ref, 
  uploadBytes, 
  getDownloadURL,
  deleteObject 
} from 'firebase/storage'
import { storage } from './firebase'

export const imageService = {
  // Upload image to Firebase Storage
  async uploadImage(file: File, path: string = 'shoe-images'): Promise<string> {
    const storageRef = ref(storage, `${path}/${Date.now()}_${file.name}`)
    const snapshot = await uploadBytes(storageRef, file)
    return await getDownloadURL(snapshot.ref)
  },

  // Delete image from Storage
  async deleteImage(imageUrl: string): Promise<void> {
    const imageRef = ref(storage, imageUrl)
    await deleteObject(imageRef)
  }
}
```

### Step 8: Authentication Service
Create `src/services/auth.ts`:
```typescript
import { 
  signInWithEmailAndPassword,
  signOut,
  onAuthStateChanged,
  User 
} from 'firebase/auth'
import { auth } from './firebase'

export const authService = {
  // Admin login
  async login(email: string, password: string): Promise<User> {
    const userCredential = await signInWithEmailAndPassword(auth, email, password)
    return userCredential.user
  },

  // Admin logout
  async logout(): Promise<void> {
    await signOut(auth)
  },

  // Auth state listener
  onAuthStateChange(callback: (user: User | null) => void) {
    return onAuthStateChanged(auth, callback)
  }
}
```

## Phase 2: Core Components Implementation

### Main App Component
Create `src/App.tsx`:
```tsx
import { BrowserRouter as Router, Routes, Route } from 'react-router-dom'
import { AuthProvider } from './context/AuthContext'
import { ShoeProvider } from './context/ShoeContext'
import Header from './components/layout/Header'
import Footer from './components/layout/Footer'
import Home from './pages/Home'
import Catalog from './pages/Catalog'
import AdminDashboard from './pages/AdminDashboard'
import AuthGuard from './components/layout/AuthGuard'

function App() {
  return (
    <AuthProvider>
      <ShoeProvider>
        <Router>
          <div className="min-h-screen bg-gray-50">
            <Header />
            <main className="container mx-auto px-4 py-8">
              <Routes>
                <Route path="/" element={<Home />} />
                <Route path="/catalog" element={<Catalog />} />
                <Route path="/admin" element={
                  <AuthGuard>
                    <AdminDashboard />
                  </AuthGuard>
                } />
              </Routes>
            </main>
            <Footer />
          </div>
        </Router>
      </ShoeProvider>
    </AuthProvider>
  )
}

export default App
```

### Authentication Context
Create `src/context/AuthContext.tsx`:
```tsx
import React, { createContext, useContext, useEffect, useState } from 'react'
import { User } from 'firebase/auth'
import { authService } from '../services/auth'

interface AuthContextType {
  user: User | null
  loading: boolean
  login: (email: string, password: string) => Promise<void>
  logout: () => Promise<void>
}

const AuthContext = createContext<AuthContextType | undefined>(undefined)

export const AuthProvider: React.FC<{ children: React.ReactNode }> = ({ children }) => {
  const [user, setUser] = useState<User | null>(null)
  const [loading, setLoading] = useState(true)

  useEffect(() => {
    const unsubscribe = authService.onAuthStateChange((user) => {
      setUser(user)
      setLoading(false)
    })
    return unsubscribe
  }, [])

  const login = async (email: string, password: string) => {
    await authService.login(email, password)
  }

  const logout = async () => {
    await authService.logout()
  }

  return (
    <AuthContext.Provider value={{ user, loading, login, logout }}>
      {children}
    </AuthContext.Provider>
  )
}

export const useAuth = () => {
  const context = useContext(AuthContext)
  if (context === undefined) {
    throw new Error('useAuth must be used within an AuthProvider')
  }
  return context
}
```

## Next Steps for Implementation

1. **Create remaining components** based on the component structure plan
2. **Implement responsive design** using Tailwind CSS
3. **Add error handling** and loading states
4. **Implement image optimization** and lazy loading
5. **Add search and filtering** functionality
6. **Set up testing** with Jest and React Testing Library
7. **Configure deployment** to Firebase Hosting

## Development Commands
```bash
# Start development server
npm run dev

# Build for production
npm run build

# Preview production build
npm run preview

# Run tests
npm run test
```

This implementation guide provides the foundation for your React + TypeScript frontend application. The next phase would involve creating the specific page components and implementing the admin interface for photo uploads.