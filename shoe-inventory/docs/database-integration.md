# Database Integration Guide

## Existing CardCapture Firebase Analysis

### Current Firebase Project
- **Project ID**: cardcapture-a4dd3
- **Database**: Firestore with 'items' and 'images' collections
- **Storage**: Firebase Storage bucket for images
- **Authentication**: Firebase Auth (to be configured for admin access)

### Collections Structure
From your CardCapture codebase:
- **items collection**: Documents with OCR-processed data
- **images collection**: Image metadata and storage references

### Adaptation for Shoe Inventory
We'll maintain the existing structure but adapt it for shoes:
- **items → shoes**: Modify documents to include shoe-specific fields
- **images → shoe_images**: Focus on shoe photography needs

## Admin Access Setup

### Firebase Admin SDK Configuration
To grant admin access to the database, you need to:

1. **Use Existing Service Account**:
   - Your CardCapture already has a service account key: `cardcapture-a4dd3-firebase-adminsdk-fbsvc-6dbc83fbab.json`
   - This key provides full admin access to Firestore and Storage

2. **Environment Setup**:
   ```bash
   # Copy the service account key to the shoe-inventory project
   cp cardcapture/cardcapture-a4dd3-firebase-adminsdk-fbsvc-6dbc83fbab.json shoe-inventory/backend/
   ```

3. **Admin SDK Initialization**:
   ```python
   # In backend/shoe_capture.py
   import firebase_admin
   from firebase_admin import credentials, firestore, storage

   # Initialize with existing service account
   cred = credentials.Certificate('cardcapture-a4dd3-firebase-adminsdk-fbsvc-6dbc83fbab.json')
   firebase_admin.initialize_app(cred, {
       'storageBucket': 'cardcapture-a4dd3.firebasestorage.app'
   })
   ```

### Security Rules Update
Modify Firestore rules to allow admin access:
```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    // Admin has full access
    match /{document=**} {
      allow read, write: if request.auth != null && 
        request.auth.token.admin === true;
    }
    
    // Public read access for shoes
    match /shoes/{shoeId} {
      allow read: if true;
      allow write: if request.auth != null;
    }
  }
}
```

## Data Migration Strategy

### Phase 1: Schema Adaptation
1. Create new collections while keeping old ones intact
2. Gradually migrate data from 'items' to 'shoes'
3. Maintain backward compatibility during transition

### Phase 2: Data Transformation
```python
# Example migration script
def migrate_items_to_shoes():
    items_ref = db.collection('items')
    shoes_ref = db.collection('shoes')
    
    for item in items_ref.stream():
        item_data = item.to_dict()
        shoe_data = {
            'brand': item_data.get('brand'),
            'model': item_data.get('model'),
            # ... other shoe fields
            'original_item_id': item.id,
            'migrated_at': datetime.now()
        }
        shoes_ref.add(shoe_data)
```

## Folder Structure Reorganization

### Recommended Structure
```
inventory-project/                  # New parent folder
├── cardcapture/                   # Existing CardCapture code
│   ├── IMAGES/
│   ├── categorize.py
│   ├── cardcapture.py
│   └── firebase-credentials.json
├── shoe-inventory/                # New React frontend
│   ├── frontend/
│   ├── backend/                   # Adapted CardCapture code
│   ├── docs/
│   └── planning/
└── README.md                      # Overall project description
```

### Migration Steps
1. Create parent directory: `mkdir inventory-project`
2. Move both projects: `mv cardcapture/ shoe-inventory/ inventory-project/`
3. Update relative paths in both codebases

## Backup Strategy
- Export current Firestore data before any schema changes
- Backup Firebase Storage images
- Maintain version control for all code changes

## Validation Checklist
- [ ] Service account key accessible to both projects
- [ ] Firestore rules updated for admin access
- [ ] Data migration scripts tested
- [ ] Backward compatibility maintained
- [ ] All image references updated correctly