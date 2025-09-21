# Shoe Inventory Website - Project Plan

## Project Overview
Build a modern web application for displaying shoe inventory with admin photo upload capabilities, leveraging existing Firebase infrastructure from CardCapture.

## Current Analysis Summary
- **Database**: Firebase Firestore (cardcapture-a4dd3)
- **Storage**: Firebase Storage (cardcapture-a4dd3.firebasestorage.app)
- **Existing Code**: CardCapture Python backend with OCR and AI categorization
- **Images**: 25+ sample images in IMAGES/ directory

## Architecture Decisions
- **Frontend**: React 18 + TypeScript + Vite (modern, fast development)
- **Styling**: Tailwind CSS + Shadcn/ui (professional, responsive design)
- **State Management**: React Context + Firebase real-time listeners
- **Authentication**: Firebase Auth (seamless integration)
- **Deployment**: Proxmox VM with Docker containers (self-hosted)
- **Camera Access**: HTML5 MediaDevices API for web-based capture
- **Image Processing**: Client-side blur detection with instant feedback

## Data Model Adaptation
### From CardCapture to Shoe Inventory:
- `items` collection → `shoes` collection
- `images` collection → `shoe_images` collection
- Add shoe-specific fields: brand, model, size, color, condition, price

### Firestore Schema:
```typescript
interface Shoe {
  id: string;                 // Generated UUID or SKU
  brand: string;             // e.g., "Nike", "Adidas"
  model: string;             // e.g., "Air Force 1", "Ultraboost"
  size: number;              // US shoe size
  color: string;             // Primary color
  condition: 'new' | 'used' | 'refurbished';
  price: number;             // Current price
  description: string;       // Detailed description
  imageUrls: string[];       // Array of Firebase Storage URLs
  createdAt: Timestamp;
  updatedAt: Timestamp;
}

interface ShoeImage {
  id: string;
  shoeId: string;           // Reference to shoe document
  imageUrl: string;         // Firebase Storage URL
  storagePath: string;      // Storage path
  isPrimary: boolean;       // Main display image
  uploadedAt: Timestamp;
}
```

## Development Phases

### Phase 1: Project Setup & Foundation (Week 1)
- [ ] Initialize React + TypeScript project with Vite
- [ ] Configure Tailwind CSS and Shadcn/ui
- [ ] Set up Firebase SDK configuration
- [ ] Create basic project structure and components
- [ ] Establish development environment and tooling

### Phase 2: Public Website (Week 2-3)
- [ ] Shoe catalog listing page with grid layout
- [ ] Individual shoe detail pages
- [ ] Search and filtering functionality
- [ ] Responsive design for mobile/desktop
- [ ] Image gallery and zoom functionality

### Phase 3: Admin Interface (Week 4-5)
- [ ] Firebase Authentication setup
- [ ] Admin login/logout functionality
- [ ] Photo upload interface with drag & drop
- [ ] Shoe management (CRUD operations)
- [ ] Bulk import/export tools
- [ ] Image cropping and editing features

### Phase 4: Backend Integration (Week 6)
- [ ] Adapt CardCapture Python code for shoes with OpenRouter API
- [ ] Implement Firestore-based queue system for processing
- [ ] Create async workers for OCR and AI processing
- [ ] Set up automated shoe categorization

### Phase 5: Testing & Deployment (Week 7)
- [ ] Comprehensive testing (unit, integration, UI)
- [ ] Performance optimization
- [ ] Proxmox VM setup with Docker deployment
- [ ] Nginx reverse proxy and SSL configuration
- [ ] Monitoring and analytics setup

## Technology Stack Details
### Frontend:
- React 18 with TypeScript
- Vite for build tooling
- Tailwind CSS for styling
- Shadcn/ui for component library
- Firebase SDK for web
- React Query for data fetching

### Backend:
- Python FastAPI (refactored from CardCapture)
- Firebase Admin SDK
- OpenRouter API for AI categorization
- Firebase Storage for image hosting
- Firestore for queue management

### Development Tools:
- ESLint + Prettier for code quality
- GitHub Actions for CI/CD
- Firebase Emulator Suite for local testing
- Chrome DevTools for debugging

## Risk Assessment
1. **Firebase Costs**: Monitor usage to avoid unexpected charges
2. **Image Storage**: Implement compression to reduce storage costs
3. **OCR Accuracy**: May need fine-tuning for shoe-specific text
4. **Authentication Security**: Implement proper role-based access
5. **Performance**: Pagination and lazy loading for large catalogs

## Success Metrics
- ✅ Website loads in under 3 seconds
- ✅ Admin can upload images in under 10 seconds
- ✅ Mobile responsive design works on all devices
- ✅ Authentication system secure and reliable
- ✅ SEO optimized for shoe listings

## Next Steps
1. Review and approve this plan
2. Begin Phase 1 implementation
3. Set up weekly progress reviews
4. Establish testing and QA processes

## Dependencies
- Firebase project must remain active
- Existing CardCapture code needs refactoring for OpenRouter
- Sufficient storage quota in Firebase
- Proxmox server access for deployment
- Domain registration (optional for custom domain)