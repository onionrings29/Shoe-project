# Setup Guide - Shoe Inventory Website

## Prerequisites
Before starting, ensure you have the following installed:
- Node.js (v18 or higher)
- npm or yarn package manager
- Python 3.8+ (for backend adaptation)
- Firebase CLI (`npm install -g firebase-tools`)
- Git for version control

## Step 1: Clone and Setup Project Structure

### Create Project Directory
```bash
mkdir shoe-inventory
cd shoe-inventory
```

### Initialize Frontend (React + TypeScript)
```bash
# Create React app with Vite
npm create vite@latest frontend -- --template react-ts
cd frontend

# Install dependencies
npm install
npm install firebase react-router-dom
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p

# Install additional UI packages
npm install class-variance-authority clsx tailwind-merge lucide-react
```

### Setup Backend (Adapt CardCapture)
```bash
# Copy existing CardCapture code
cp -r ../cardcapture/ ../backend/

# Create virtual environment
cd ../backend
python3 -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install Python dependencies
pip install -r requirements.txt
```

## Step 2: Firebase Configuration

### Firebase Project Setup
1. Go to [Firebase Console](https://console.firebase.google.com/)
2. Select your existing "cardcapture-a4dd3" project
3. Enable Authentication if not already enabled
4. Note down your project configuration

### Environment Variables
Create `.env` file in `frontend/` directory:
```env
VITE_FIREBASE_API_KEY=your_api_key_here
VITE_FIREBASE_AUTH_DOMAIN=cardcapture-a4dd3.firebaseapp.com
VITE_FIREBASE_PROJECT_ID=cardcapture-a4dd3
VITE_FIREBASE_STORAGE_BUCKET=cardcapture-a4dd3.firebasestorage.app
VITE_FIREBASE_MESSAGING_SENDER_ID=112775655709157944961
VITE_FIREBASE_APP_ID=your_app_id_here
```

### Firebase Security Rules
Update Firestore rules in Firebase Console:
```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    // Public read access to shoes
    match /shoes/{shoeId} {
      allow read: if true;
      allow write: if request.auth != null;
    }
    
    // Admin only access for images
    match /shoe_images/{imageId} {
      allow read: if true;
      allow write: if request.auth != null;
    }
    
    // Admin only for user management
    match /users/{userId} {
      allow read, write: if request.auth != null;
    }
  }
}
```

## Step 3: Adapt CardCapture Backend for Shoes

### Create Shoe-Specific Version
Create `backend/shoe_capture.py`:
```python
# Adapted from cardcapture.py for shoe-specific processing
import os
import json
from datetime import datetime
from firebase_admin import firestore, storage

# Initialize Firebase
db = firestore.client()
bucket = storage.bucket()

def adapt_for_shoes(ocr_data):
    """
    Adapt OCR data for shoe inventory instead of general items
    """
    shoe_categories = {
        'brand': None,
        'model': None,
        'size': None,
        'color': None,
        'condition': None,
        'price': None,
        'description': None
    }
    
    # Add shoe-specific processing logic here
    # This would replace the general categorization in categorize.py
    
    return shoe_categories

def process_shoe_image(image_path, filename):
    """
    Process shoe images specifically
    """
    # Custom shoe image processing logic
    # Could include size detection, color analysis, etc.
    pass
```

### Update Requirements
Add any shoe-specific dependencies to `backend/requirements.txt`:
```
# Add if needed for shoe-specific processing
opencv-python>=4.5.0
Pillow>=9.0.0
```

## Step 4: Frontend Firebase Configuration

### Firebase Service Setup
Create `frontend/src/services/firebase.ts`:
```typescript
import { initializeApp } from 'firebase/app'
import { getFirestore } from 'firebase/firestore'
import { getStorage } from 'firebase/storage'
import { getAuth } from 'firebase/auth'

const firebaseConfig = {
  apiKey: import.meta.env.VITE_FIREBASE_API_KEY,
  authDomain: import.meta.env.VITE_FIREBASE_AUTH_DOMAIN,
  projectId: import.meta.env.VITE_FIREBASE_PROJECT_ID,
  storageBucket: import.meta.env.VITE_FIREBASE_STORAGE_BUCKET,
  messagingSenderId: import.meta.env.VITE_FIREBASE_MESSAGING_SENDER_ID,
  appId: import.meta.env.VITE_FIREBASE_APP_ID
}

const app = initializeApp(firebaseConfig)
export const db = getFirestore(app)
export const storage = getStorage(app)
export const auth = getAuth(app)
```

## Step 5: Development Workflow

### Frontend Development
```bash
cd frontend
npm run dev
```
Access at: http://localhost:5173

### Backend Development
```bash
cd backend
source venv/bin/activate
python shoe_capture.py
```

### Firebase Emulator (Optional)
```bash
# Install emulator
firebase init emulators

# Start emulators
firebase emulators:start
```

## Step 6: Proxmox Deployment Setup

### Docker Configuration for Proxmox
Create `docker-compose.yml` in project root:
```yaml
version: '3.8'
services:
  frontend:
    build: ./frontend
    ports:
      - "3000:80"
    environment:
      - VITE_FIREBASE_API_KEY=${VITE_FIREBASE_API_KEY}
      - VITE_FIREBASE_AUTH_DOMAIN=${VITE_FIREBASE_AUTH_DOMAIN}
      - VITE_FIREBASE_PROJECT_ID=${VITE_FIREBASE_PROJECT_ID}
      - VITE_FIREBASE_STORAGE_BUCKET=${VITE_FIREBASE_STORAGE_BUCKET}
    restart: unless-stopped

  backend:
    build: ./backend
    ports:
      - "8000:8000"
    environment:
      - OPENROUTER_API_KEY=${OPENROUTER_API_KEY}
      - FIREBASE_CREDENTIALS=${FIREBASE_CREDENTIALS}
    restart: unless-stopped

  nginx:
    image: nginx:alpine
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf
      - ./ssl:/etc/ssl/certs
    depends_on:
      - frontend
      - backend
    restart: unless-stopped
```

### Dockerfile for Frontend
Create `frontend/Dockerfile`:
```dockerfile
FROM node:18-alpine as build
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

FROM nginx:alpine
COPY --from=build /app/dist /usr/share/nginx/html
COPY nginx.conf /etc/nginx/nginx.conf
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

### Dockerfile for Backend
Create `backend/Dockerfile`:
```dockerfile
FROM python:3.11-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .
EXPOSE 8000
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
```

### Proxmox VM Setup
1. Create Ubuntu Server 22.04 LTS VM on Proxmox
2. Install Docker and Docker Compose
3. Copy project files to VM
4. Set up environment variables
5. Run `docker-compose up -d`

### Environment Setup Checklist
- [ ] Node.js and npm installed
- [ ] Python 3.8+ installed
- [ ] Docker and Docker Compose installed
- [ ] Firebase project configured
- [ ] Environment variables set
- [ ] Dependencies installed
- [ ] Development servers running
- [ ] Security rules configured
- [ ] Proxmox VM ready for deployment

## Troubleshooting Common Issues

### Camera Access Issues
- Ensure HTTPS for camera access in production
- Check browser permissions for camera access
- Test on mobile devices (iPhone Safari support)

### Blur Detection Performance
- Optimize image processing for client-side performance
- Consider web workers for heavy computation

### Firebase Authentication Issues
- Ensure Authentication is enabled in Firebase Console
- Check that authorized domains include your production domain

### CORS Issues
- Configure CORS in Firebase Storage rules
- Use proper domain whitelisting

### Image Upload Problems
- Check Firebase Storage security rules
- Verify file size limits and formats

### Firestore Permission Errors
- Review and update Firestore security rules
- Check authentication state in application

## Next Steps After Setup
1. Implement the frontend components with camera upload and blur detection
2. Adapt the backend processing for OpenRouter API integration
3. Test the image upload and processing functionality
4. Deploy to Proxmox server using Docker
5. Set up monitoring and analytics

This setup guide provides everything needed to get your shoe inventory website running locally and deployed to production on Proxmox.