# TODO List - Shoe Inventory Website Project

## Current Status
- [x] Analyze current CardCapture Firebase structure and data model
- [x] Set up project structure for frontend and backend
- [x] Create documentation and setup instructions
- [x] Create change log and maintain activity tracking
- [x] Refactor CardCapture to use OpenRouter API instead of local Ollama
- [-] **Service: Frontend Application** - React + TypeScript with camera upload
- [-] **Service: OpenRouter Integration** - AI categorization service
- [-] **Service: OCR Service Integration** - Google Cloud Vision text extraction
- [-] **Service: Image Processing Pipeline** - HEIC conversion and preprocessing
- [ ] **Service: Blur Detection** - Client-side image quality checking
- [ ] **Service: Continuous Capture Mode** - Rapid sequential photo capture
- [ ] **Service: Batch Review Screen** - Manual image selection interface
- [ ] **Service: Background Uploads** - Offline support and sync
- [ ] **Service: Firestore Queue System** - Processing queue management
- [ ] **Service: Public Website** - Shoe catalog display
- [ ] **Service: Admin Interface** - Real-time queue management
- [ ] **Service: Authentication** - Firebase Auth with admin roles
- [ ] **Service: Testing** - Comprehensive unit and integration tests
- [ ] **Service: Logging** - Structured logging throughout application
- [ ] **Service: Deployment** - Proxmox Docker deployment

## Service Implementation Details

### Frontend Application
- [ ] Initialize Vite React+TS project with Tailwind CSS
- [ ] Create Firebase configuration service
- [ ] Implement HTML5 MediaDevices API camera component
- [ ] Create image upload service to Firebase Storage
- [ ] Set up Firestore data services for shoe CRUD operations

### OpenRouter Integration  
- [ ] Create OpenRouterProvider class with async generate method
- [ ] Implement cost tracking and usage monitoring
- [ ] Add fallback mechanism to Deepseek provider
- [ ] Update prompt engineering for shoe-specific categorization
- [ ] Test OpenRouter integration with sample shoe data

### OCR Service Integration
- [ ] Create OCR service with Google Cloud Vision client
- [ ] Port HEIC conversion functionality with pillow-heif support
- [ ] Implement card detection and cropping algorithms
- [ ] Add perspective correction for shoe images
- [ ] Integrate layout processing for text block extraction

### Testing Service (High Priority)
- [ ] Set up Jest and React Testing Library for frontend components
- [ ] Implement pytest for backend service testing
- [ ] Create comprehensive test suites for all critical components
- [ ] Implement integration testing with Firebase emulators
- [ ] Add end-to-end testing for complete user workflows
- [ ] Set up test coverage reporting and CI/CD test integration

## Priority Tasks
1. **Complete Frontend Foundation**: Finish React app setup with camera integration
2. **Implement Core Services**: OpenRouter, OCR, and image processing pipelines
3. **Testing Infrastructure**: Set up comprehensive test suites early in development
4. **Database Integration**: Ensure seamless connection to existing Firebase project
5. **Admin Access Setup**: Configure Firebase Admin SDK with proper credentials
6. **Deployment Preparation**: Set up Docker containers for Proxmox deployment

## Notes
- All changes must be logged in CHANGELOG.md using JSON format
- Unit tests should cover frontend components and backend services (90%+ coverage goal)
- Logging should include user actions, API calls, and errors with structured format
- Maintain compatibility with existing CardCapture codebase and data
- Use HTML5 MediaDevices API for camera access in web app
- Implement lightweight blur detection library for client-side checks
- Plan for offline support and background uploads with service workers
- Ensure responsive design for mobile devices, especially iPhone
- All services must include comprehensive unit tests before deployment
- Testing should be integrated into CI/CD pipeline from the beginning