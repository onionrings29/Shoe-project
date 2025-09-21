# OpenRouter API Integration Guide

## OpenRouter Configuration

### API Setup Requirements
1. **Create OpenRouter Account**: https://openrouter.ai/
2. **Get API Key**: From your OpenRouter dashboard
3. **Configure Environment Variables**:
```bash
# .env file
OPENROUTER_API_KEY=your_openrouter_api_key_here
OPENROUTER_MODEL=openai/gpt-4  # or other supported model
OPENROUTER_BASE_URL=https://openrouter.ai/api/v1
```

### OpenRouter Provider Implementation
```python
# backend/services/openrouter_provider.py
import aiohttp
import json
import logging
from typing import Dict, Any
import os

logger = logging.getLogger(__name__)

class OpenRouterProvider:
    """OpenRouter API provider for AI categorization"""
    
    def __init__(self):
        self.api_key = os.getenv('OPENROUTER_API_KEY')
        self.model = os.getenv('OPENROUTER_MODEL', 'openai/gpt-4')
        self.base_url = os.getenv('OPENROUTER_BASE_URL', 'https://openrouter.ai/api/v1')
        
    async def generate(self, prompt: str) -> str:
        """Generate response using OpenRouter API"""
        headers = {
            "Authorization": f"Bearer {self.api_key}",
            "Content-Type": "application/json",
            "HTTP-Referer": "https://shoe-inventory.com",  # Required by OpenRouter
            "X-Title": "Shoe Inventory Processor"
        }
        
        payload = {
            "model": self.model,
            "messages": [{"role": "user", "content": prompt}],
            "temperature": 0.2,
            "max_tokens": 2000,
            "top_p": 0.9,
            "frequency_penalty": 0.1,
            "presence_penalty": 0.1
        }
        
        try:
            async with aiohttp.ClientSession() as session:
                async with session.post(
                    f"{self.base_url}/chat/completions",
                    headers=headers,
                    json=payload,
                    timeout=30
                ) as response:
                    response.raise_for_status()
                    data = await response.json()
                    
                    # Log token usage for cost tracking
                    usage = data.get('usage', {})
                    logger.info(f"OpenRouter usage: {usage}")
                    
                    return data['choices'][0]['message']['content']
                    
        except aiohttp.ClientError as e:
            logger.error(f"OpenRouter API request failed: {e}")
            raise
        except json.JSONDecodeError as e:
            logger.error(f"OpenRouter API response parsing failed: {e}")
            raise
        except KeyError as e:
            logger.error(f"OpenRouter API response missing expected fields: {e}")
            raise

# Usage example
async def test_openrouter():
    provider = OpenRouterProvider()
    prompt = "Extract shoe information from the following text..."
    response = await provider.generate(prompt)
    return response
```

## Integration with Existing CardCapture

### Replace Ollama Provider
```python
# backend/categorize_shoe.py (modified)
# Remove OllamaProvider and replace with OpenRouterProvider

class ShoeProcessor:
    def __init__(self):
        # Replace Ollama with OpenRouter
        self.llm_provider = OpenRouterProvider()
        # Keep existing Firebase and OCR setup
```

### Cost Management Implementation
```python
# backend/services/cost_tracker.py
import logging
from datetime import datetime, timedelta
from firebase_admin import firestore

logger = logging.getLogger(__name__)

class CostTracker:
    """Track OpenRouter API usage and costs"""
    
    def __init__(self):
        self.db = firestore.client()
        self.daily_usage = 0
        self.monthly_usage = 0
        
    async def track_usage(self, usage_data: Dict[str, Any]):
        """Track API usage and costs"""
        try:
            # Calculate cost (approximate based on OpenRouter pricing)
            prompt_tokens = usage_data.get('prompt_tokens', 0)
            completion_tokens = usage_data.get('completion_tokens', 0)
            total_tokens = prompt_tokens + completion_tokens
            
            # Approximate cost calculation (varies by model)
            cost = total_tokens * 0.00002  # Example: $0.02 per 1K tokens
            
            # Update daily and monthly usage
            self.daily_usage += cost
            self.monthly_usage += cost
            
            # Store in Firestore for analytics
            usage_doc = {
                'timestamp': datetime.now(),
                'prompt_tokens': prompt_tokens,
                'completion_tokens': completion_tokens,
                'total_tokens': total_tokens,
                'cost': cost,
                'model': os.getenv('OPENROUTER_MODEL')
            }
            
            self.db.collection('api_usage').add(usage_doc)
            logger.info(f"API usage tracked: {total_tokens} tokens, ${cost:.6f}")
            
        except Exception as e:
            logger.error(f"Cost tracking failed: {e}")

# Integrate with OpenRouterProvider
class OpenRouterProviderWithTracking(OpenRouterProvider):
    def __init__(self, cost_tracker: CostTracker):
        super().__init__()
        self.cost_tracker = cost_tracker
        
    async def generate(self, prompt: str) -> str:
        response = await super().generate(prompt)
        # Extract usage data from response if available
        # This would depend on OpenRouter's response format
        return response
```

## Model Selection Guide

### Recommended Models for Shoe Categorization
1. **openai/gpt-4** - Best accuracy, higher cost
2. **anthropic/claude-3-sonnet** - Good balance of cost/performance  
3. **google/gemini-pro** - Cost-effective, good for structured data
4. **meta-llama/llama-3-70b-instruct** - Open source alternative

### Model Configuration
```python
# Model configuration based on needs
MODEL_CONFIGS = {
    'high_accuracy': {
        'model': 'openai/gpt-4',
        'max_tokens': 2000,
        'temperature': 0.1
    },
    'balanced': {
        'model': 'anthropic/claude-3-sonnet',
        'max_tokens': 2000,
        'temperature': 0.2
    },
    'cost_effective': {
        'model': 'google/gemini-pro',
        'max_tokens': 2000,
        'temperature': 0.3
    }
}
```

## Error Handling and Fallbacks

### Fallback Strategy
```python
async def generate_with_fallback(self, prompt: str, retries: int = 3):
    """Generate with retry and fallback logic"""
    for attempt in range(retries):
        try:
            return await self.generate(prompt)
        except Exception as e:
            logger.warning(f"Attempt {attempt + 1} failed: {e}")
            if attempt == retries - 1:
                # Fallback to simpler processing or queue for retry
                logger.error("All retry attempts failed")
                raise
            await asyncio.sleep(2 ** attempt)  # Exponential backoff
```

## Testing and Validation

### Test Script
```python
# backend/test_openrouter.py
import asyncio
import os
from dotenv import load_dotenv
from services.openrouter_provider import OpenRouterProvider

load_dotenv()

async def test_categorization():
    provider = OpenRouterProvider()
    
    test_prompt = """
    Extract shoe information from: 
    "Nike Air Force 1, Size 10, White, Condition: New, Price: $120"
    """
    
    try:
        response = await provider.generate(test_prompt)
        print("OpenRouter response:")
        print(response)
    except Exception as e:
        print(f"Test failed: {e}")

if __name__ == "__main__":
    asyncio.run(test_categorization())
```

## Deployment Considerations

### Environment Setup
```bash
# Production environment variables
OPENROUTER_API_KEY=sk-or-...
OPENROUTER_MODEL=anthropic/claude-3-sonnet
OPENROUTER_BASE_URL=https://openrouter.ai/api/v1
OPENROUTER_MAX_RETRIES=3
OPENROUTER_TIMEOUT=30
```

### Monitoring and Alerts
- Set up usage alerts in OpenRouter dashboard
- Implement cost threshold alerts
- Monitor response times and success rates
- Log all API interactions for debugging

This integration provides a robust, cost-effective AI categorization system using OpenRouter API while maintaining the existing CardCapture functionality for OCR and image processing.