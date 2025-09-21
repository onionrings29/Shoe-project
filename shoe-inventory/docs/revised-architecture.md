# Revised Architecture Plan - Camera Uploads & Proxmox Hosting

## Current CardCapture AI Analysis
Your current CardCapture system uses:
- **OllamaProvider**: Local AI with "deepseek-r1:14b" model (lines 152-208 in categorize.py)
- **DeepseekProvider**: Cloud API option (lines 209-239 in categorize.py)
- **OCR Processing**: Google Cloud Vision for text extraction
- **Processing Flow**: Batch processing of images from file system

## New Architecture Decisions

### 1. AI Service Replacement
**Switch from Ollama to OpenRouter API:**
- **Why**: Better scalability, no local GPU requirements, access to multiple models
- **Implementation**: Replace OllamaProvider with OpenRouterProvider
- **Cost Consideration**: OpenRouter provides access to multiple models with pay-per-use pricing

### 2. Camera Upload Integration
**Web-based Camera Capture for iPhone:**
- Use HTML5 MediaDevices API for camera access in Safari
- Implement instant client-side blur detection for quality feedback
- Support rapid sequential photos with continuous capture mode
- Real-time preview with immediate retake option for blurry images
- Optimized for mobile devices, especially iPhone

### 3. Queue System Implementation
**Processing Queue Architecture:**
- Firebase Firestore as queue management system
- Real-time updates using Firestore listeners
- Status tracking: queued, processing, completed, failed
- Priority system for urgent processing

### 4. Proxmox Hosting Setup
**Instead of Firebase Hosting:**
- Self-hosted on Proxmox virtual machine
- Docker containerization for easy deployment
- Nginx reverse proxy for web server
- SSL certificate management with Let's Encrypt

## Technology Stack Update

### Frontend (React + TypeScript)
- **Camera Access**: react-webcam or custom MediaDevices implementation
- **Queue Management**: Firebase real-time listeners
- **UI Framework**: Tailwind CSS + Shadcn/ui
- **State Management**: React Context + Firebase hooks

### Backend (Python + FastAPI)
- **Web Framework**: FastAPI (instead of pure Python scripts)
- **API Services**: REST endpoints for image processing
- **Queue Workers**: Async workers for OCR and AI processing
- **Containerization**: Docker for easy deployment

### AI Services
- **OpenRouter API**: Multiple model support (GPT-4, Claude, etc.)
- **Fallback Options**: Keep Deepseek as backup provider
- **Cost Management**: API usage tracking and limits

### Infrastructure
- **Proxmox VM**: Ubuntu Server 22.04 LTS
- **Docker**: Container orchestration
- **Nginx**: Web server and reverse proxy
- **Supervisor**: Process management for Python workers

## Implementation Phases

### Phase 1: AI Service Refactor (Priority)
- Replace OllamaProvider with OpenRouterProvider
- Implement API key management for OpenRouter
- Add fallback mechanism to Deepseek
- Update prompt engineering for shoe-specific categorization

### Phase 2: Camera Upload & Queue System
- Implement camera capture in React frontend
- Create Firestore-based queue system
- Build admin interface for queue management
- Add real-time status updates

### Phase 3: Proxmox Deployment
- Set up Proxmox virtual machine
- Dockerize the application
- Configure Nginx and SSL
- Implement CI/CD pipeline

### Phase 4: Enhanced Features
- Bulk processing capabilities
- Advanced image editing tools
- Export functionality for eBay integration
- Analytics and reporting dashboard

## OpenRouter Integration Details

### Provider Implementation
```python
class OpenRouterProvider(LLMProvider):
    """OpenRouter API provider"""
    API_BASE = "https://openrouter.ai/api/v1"
    
    async def generate(self, prompt: str) -> str:
        headers = {
            "Authorization": f"Bearer {OPENROUTER_API_KEY}",
            "Content-Type": "application/json",
            "HTTP-Referer": "https://yourdomain.com",  # Required by OpenRouter
            "X-Title": "Shoe Inventory Processor"
        }
        
        payload = {
            "model": "openai/gpt-4",  # Can be configured
            "messages": [{"role": "user", "content": prompt}],
            "temperature": 0.2,
            "max_tokens": 2000
        }
        
        async with aiohttp.ClientSession() as session:
            async with session.post(
                f"{self.API_BASE}/chat/completions",
                headers=headers,
                json=payload
            ) as response:
                response.raise_for_status()
                data = await response.json()
                return data['choices'][0]['message']['content']
```

### Cost Management
- Implement usage tracking per image processed
- Set cost limits and alerts
- Support multiple model options based on budget

## Queue System Design

### Firestore Collection Structure
```
/processing_queue/
  ├── documentId: auto-generated
  ├── status: "queued" | "processing" | "completed" | "failed"
  ├── imageUrl: string (Firebase Storage URL)
  ├── createdAt: timestamp
  ├── startedAt: timestamp
  ├── completedAt: timestamp
  ├── result: { ... } (AI categorization results)
  └── error: string (if failed)
```

### Worker Implementation
- Multiple workers can process queue concurrently
- Exponential backoff for retries
- Priority system based on user requirements

## Camera Upload Implementation

### React Component Structure with Blur Detection
```tsx
// CameraCapture component with instant blur feedback
const CameraCapture: React.FC = () => {
  const [imageData, setImageData] = useState<string | null>(null);
  const [isBlurry, setIsBlurry] = useState<boolean>(false);
  
  const captureImage = async () => {
    const stream = await navigator.mediaDevices.getUserMedia({ video: true });
    // Capture logic here
    const imageBlob = await captureFrame(videoRef.current);
    const blurScore = await checkImageBlur(imageBlob);
    
    if (blurScore > 0.8) { // Threshold for blur detection
      setIsBlurry(true);
      setImageData(URL.createObjectURL(imageBlob));
    } else {
      setIsBlurry(false);
      await uploadToQueue(imageBlob);
    }
  };
  
  const retakeImage = () => {
    setImageData(null);
    setIsBlurry(false);
  };
  
  return (
    <div>
      <video ref={videoRef} autoPlay />
      <button onClick={captureImage}>Capture</button>
      {imageData && (
        <div>
          <img src={imageData} alt="Captured" />
          {isBlurry && (
            <div className="blur-warning">
              <span>Image is blurry - please retake</span>
              <button onClick={retakeImage}>Retake</button>
            </div>
          )}
        </div>
      )}
    </div>
  );
};
```

## Proxmox Hosting Setup

### VM Configuration
- **OS**: Ubuntu Server 22.04 LTS
- **Resources**: 4GB RAM, 2 vCPUs, 50GB storage
- **Network**: Bridged networking for public access

### Docker Setup
```dockerfile
# Dockerfile for backend
FROM python:3.11-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
```

### Deployment Script
```bash
#!/bin/bash
# deploy.sh
docker build -t shoe-inventory-backend .
docker stop shoe-backend || true
docker rm shoe-backend || true
docker run -d --name shoe-backend -p 8000:8000 shoe-inventory-backend
```

## Migration Strategy

### Step 1: AI Service Replacement
- Implement OpenRouterProvider alongside existing providers
- Test with sample data
- Gradually phase out Ollama

### Step 2: Camera Integration
- Add camera components to React frontend
- Implement Firebase Storage upload
- Connect to processing queue

### Step 3: Queue System
- Build Firestore queue structure
- Create worker processes
- Implement status tracking

### Step 4: Proxmox Deployment
- Set up production environment
- Migrate from Firebase Hosting
- Configure domain and SSL

## Priority Assessment
1. **Immediate**: AI service refactor (OpenRouter integration)
2. **High**: Camera upload functionality
3. **Medium**: Queue system implementation
4. **Long-term**: Proxmox deployment and optimization

This revised architecture maintains your existing Firebase infrastructure for database and storage while enhancing the AI capabilities and adding modern web features for camera-based uploads and processing.