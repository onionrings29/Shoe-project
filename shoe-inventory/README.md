# Shoe Inventory Website Project

## Project Overview
A modern web application for displaying shoe inventory with an admin interface for photo uploads and item management. Built on top of your existing CardCapture Firebase infrastructure.

## Architecture
```
shoe-inventory/
├── frontend/                 # React + TypeScript frontend
│   ├── public/              # Static assets
│   ├── src/
│   │   ├── components/      # Reusable UI components
│   │   ├── pages/          # Page components (Home, Admin, etc.)
│   │   ├── services/       # Firebase services
│   │   ├── types/          # TypeScript type definitions
│   │   └── utils/          # Utility functions
│   ├── package.json
│   └── vite.config.ts
├── backend/                 # Python backend (existing CardCapture adapted)
│   ├── shoe_capture.py     # Modified version of cardcapture.py
│   ├── categorize_shoe.py  # Modified categorization for shoes
│   └── requirements.txt
├── docs/                   # Documentation
├── scripts/               # Deployment and utility scripts
└── planning/              # Project planning documents
```

## Technology Stack
- **Frontend**: React 18 + TypeScript + Vite
- **Styling**: Tailwind CSS + Shadcn/ui components
- **Backend**: Python (existing CardCapture adapted)
- **Database**: Firebase Firestore
- **Storage**: Firebase Storage
- **Authentication**: Firebase Auth
- **Deployment**: Firebase Hosting + Cloud Functions

## Data Model (Adapted from CardCapture)
### Firestore Collections:
1. **shoes** collection (modified from 'items')
   - Document ID: Shoe SKU or generated ID
   - Fields: brand, model, size, color, condition, price, description, image_urls, etc.

2. **shoe_images** collection (modified from 'images')
   - Document ID: Generated UUID
   - Fields: shoe_id, image_url, storage_path, uploaded_at, is_primary

## Features
### Public Website:
- Display shoe catalog with filtering
- Individual shoe detail pages
- Responsive design for mobile/desktop
- Search functionality

### Admin Interface:
- Protected login with Firebase Auth
- Photo upload to Firebase Storage
- Shoe item management (CRUD operations)
- Bulk import/export capabilities
- Image cropping and editing tools

## Development Phases
1. **Phase 1**: Project setup and Firebase integration
2. **Phase 2**: Public website development
3. **Phase 3**: Admin interface development
4. **Phase 4**: Testing and deployment
5. **Phase 5**: Documentation and handover

## Getting Started
1. Clone the repository
2. Install dependencies: `npm install` (frontend) and `pip install -r requirements.txt` (backend)
3. Configure Firebase credentials
4. Run development servers
5. Deploy to Firebase Hosting

## Dependencies
See individual `package.json` and `requirements.txt` files for complete lists.