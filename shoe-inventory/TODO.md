# TODO List - Shoe Inventory Website Project

## Current Status
- [x] Analyze current CardCapture Firebase structure and data model
- [x] Set up project structure for frontend and backend
- [x] Create documentation and setup instructions
- [x] Create change log and maintain activity tracking
- [x] Refactor CardCapture to use OpenRouter API instead of local Ollama
- [-] Create React + TypeScript frontend application with camera upload (includes HTML5 MediaDevices API integration)
- [-] Implement instant client-side blur detection with visual feedback for retakes
- [ ] Add continuous capture mode for rapid sequential photos
- [ ] Develop batch review screen for manual image selection
- [ ] Implement background uploads and offline support
- [ ] Build Firestore-based queue system for processing
- [ ] Develop public website to display shoe items from Firestore
- [ ] Build admin interface with real-time queue management
- [ ] Integrate Firebase authentication for admin access
- [ ] Add responsive design and modern UI components
- [ ] Add comprehensive unit tests for frontend and backend
- [ ] Implement logging throughout the application
- [ ] Test the application thoroughly
- [ ] Deploy web app to Proxmox server using Docker

## Priority Tasks
1. **Complete Frontend with Camera Upload**: Finish React app with HTML5 MediaDevices API integration
2. **Implement Blur Detection**: Add client-side blur detection for instant feedback
3. **Database Integration**: Ensure seamless connection to existing Firebase project
4. **Admin Access Setup**: Configure Firebase Admin SDK with proper credentials
5. **Deployment Preparation**: Set up Docker containers for Proxmox deployment

## Notes
- All changes must be logged in CHANGELOG.md
- Unit tests should cover frontend components and backend services
- Logging should include user actions, API calls, and errors
- Maintain compatibility with existing CardCapture codebase
- Use HTML5 MediaDevices API for camera access in web app
- Implement lightweight blur detection library for client-side checks
- Plan for offline support and background uploads
- Ensure responsive design for mobile devices, especially iPhone