# PlantGuard AI - Architecture Documentation

## Overview

PlantGuard AI implements a **clean layered architecture** within a single Jupyter notebook. This document explains the design decisions, layer responsibilities, and code organization.

## Layered Architecture Pattern

### Why Layered Architecture?

1. **Separation of Concerns** - Each layer has a specific responsibility
2. **Maintainability** - Changes are localized to specific layers
3. **Testability** - Layers can be tested independently
4. **Scalability** - Easy to add features following established patterns
5. **Code Reusability** - Services can be used across different features

### The 4 Layers

```
┌─────────────────────────────────────────────────────────┐
│                   PRESENTATION LAYER                    │
│  Responsibility: User Interface & Visualization         │
│  Technologies: HTML, CSS, JavaScript, Chart.js          │
│  Cell: 17                                               │
└────────────────────┬────────────────────────────────────┘
                     │ Display data, handle UI events
                     │
┌────────────────────▼────────────────────────────────────┐
│                  APPLICATION LAYER                      │
│  Responsibility: Orchestrate workflows, handle requests │
│  Classes: ImageProcessingController, SearchController   │
│  Cells: 11-12                                           │
└────────────────────┬────────────────────────────────────┘
                     │ Coordinate services, enforce rules
                     │
┌────────────────────▼────────────────────────────────────┐
│                 BUSINESS LOGIC LAYER                    │
│  Responsibility: Core domain logic & algorithms         │
│  Classes: DiseaseDetector, SearchEngine, TextProcessor  │
│  Cells: 8-10                                            │
└────────────────────┬────────────────────────────────────┘
                     │ Read/write data, call external APIs
                     │
┌────────────────────▼────────────────────────────────────┐
│                  DATA ACCESS LAYER                      │
│  Responsibility: External systems & data persistence    │
│  Classes: FirebaseRepository, GeminiAIClient           │
│  Cells: 5-7                                             │
└─────────────────────────────────────────────────────────┘
```

## Cell-by-Cell Breakdown

### Cell 1: Title & Overview (Markdown)
**Purpose**: Documentation header explaining architecture

### Cell 2: Package Installation (Code)
**Purpose**: Install required Python packages
- transformers, torch, opencv, google-generativeai
- Downloads NLTK data

### Cell 3: Import Dependencies (Code)
**Purpose**: Import all required libraries
- Groups imports by category (AI, CV, NLP, Utils)

### Cell 4: Configuration Layer (Code)
**Class**: `Config`
**Responsibility**: Centralize all configuration
**Contains**:
- Firebase URLs
- Sensor thresholds
- Disease database
- Search settings
- Model names

**Why centralized?**
- Single source of truth
- Easy to modify settings
- No scattered hardcoded values

### Cell 5: Data Access - Firebase Repository (Code)
**Class**: `FirebaseRepository`
**Responsibility**: All Firebase operations
**Methods**:
- `save_sensor_data()` - Write sensor readings
- `get_sensor_history()` - Read historical data
- `save_articles()` - Store article database
- `get_inverted_index()` - Read search index
- `save_inverted_index()` - Write search index

**Design Pattern**: Repository Pattern
- Abstracts data persistence
- Swappable backend (could use SQL, MongoDB, etc.)

### Cell 6: Data Access - Gemini AI Client (Code)
**Class**: `GeminiAIClient`
**Responsibility**: Google Gemini AI interactions
**Methods**:
- `generate_response()` - General text generation
- `synthesize_articles()` - RAG summarization
- `chat()` - Chatbot conversation

**Why separate class?**
- API calls are expensive
- Rate limiting logic in one place
- Easy to mock for testing

### Cell 7: Data Access - Model Loader (Code)
**Class**: `ModelLoader`
**Responsibility**: ML model management
**Methods**:
- `load_plant_classifier()` - Load Hugging Face model
- `get_classifier()` - Return cached model

**Caching Strategy**: Singleton pattern to avoid reloading

### Cell 8: Business Logic - Text Processor (Code)
**Class**: `TextProcessor`
**Responsibility**: NLP operations
**Methods**:
- `tokenize()` - Split text into words
- `remove_stopwords()` - Filter common words
- `lemmatize()` - Reduce to base form

**Why separate?**
- Reusable across search and analysis
- Complex NLP logic isolated
- Easy to switch lemmatization algorithms

### Cell 9: Business Logic - Search Engine (Code)
**Class**: `SearchEngine`
**Responsibility**: Inverted index + TF-IDF search
**Methods**:
- `build_index()` - Create inverted index
- `search()` - Find relevant articles
- `calculate_tfidf()` - Relevance scoring

**Algorithm**: TF-IDF with inverted index
**Why this approach?**
- Fast O(k) lookup (k = query terms)
- Better than linear search O(n)
- Relevance scoring via TF-IDF

### Cell 10: Business Logic - Disease Detector (Code)
**Class**: `DiseaseDetector`
**Responsibility**: Plant disease detection
**Methods**:
- `classify_plant()` - Identify plant species
- `detect_disease()` - HSV color analysis
- `process_image()` - Complete pipeline

**Computer Vision Pipeline**:
1. Classify plant (Hugging Face model)
2. Validate is sage
3. Convert to HSV color space
4. Apply disease-specific masks
5. Count affected pixels
6. Generate diagnosis

**No Duplication**: Original had 2 similar functions, now unified

### Cell 11: Application - Image Controller (Code)
**Class**: `ImageProcessingController`
**Responsibility**: Orchestrate image analysis
**Methods**:
- `process_leaf()` - Main entry point
- Coordinates: DiseaseDetector + SearchEngine

**Workflow**:
```
JavaScript (UI) → process_leaf_logic() → ImageProcessingController
                                           ↓
                                    DiseaseDetector.process_image()
                                           ↓
                                    SearchEngine.search()
                                           ↓
                                    Return JSON result
```

### Cell 12: Application - Search Controller (Code)
**Class**: `SearchController`
**Responsibility**: Orchestrate search operations
**Methods**:
- `search_with_summary()` - Search + AI synthesis

**Workflow**:
```
JavaScript (UI) → rag_search_bridge() → SearchController
                                         ↓
                                  SearchEngine.search()
                                         ↓
                                  GeminiAIClient.synthesize()
                                         ↓
                                  Display results in UI
```

### Cell 13: Initialize Services (Code)
**Purpose**: Instantiate all service objects
**Pattern**: Dependency Injection
- Controllers receive service instances
- Easier to test (can inject mocks)

### Cell 14: Load Models (Code)
**Purpose**: Load ML models from Hugging Face
**Note**: Takes time, shows progress

### Cell 15: Build Search Index (Code)
**Purpose**: Create inverted index from articles
**Caching**: Saves to Firebase for reuse

### Cell 16: Register Colab Bridges (Code)
**Purpose**: Connect Python backend to JavaScript UI
**Functions**:
- `process_leaf_logic()` - Image analysis bridge
- `rag_search_bridge()` - Search bridge
- `ask_ai()` - Chatbot bridge

**Why bridges?**
- Colab requires specific callback format
- Thin adapter layer
- Controllers do real work

### Cell 17: Presentation Layer (Code)
**Purpose**: Complete UI in HTML/CSS/JS
**Features**:
- 4 tabs (Dashboard, AI Scan, Analytics, Research)
- Real-time sensor display
- Image upload & analysis
- Search interface
- Chatbot

## Design Patterns Used

### 1. Repository Pattern (Cells 5-7)
**Problem**: Direct database calls scattered in code
**Solution**: Centralize in repository classes
**Benefit**: Easy to swap databases

### 2. Service Layer Pattern (Cells 8-10)
**Problem**: Business logic mixed with UI
**Solution**: Pure service classes
**Benefit**: Reusable, testable

### 3. Controller Pattern (Cells 11-12)
**Problem**: Who orchestrates multi-service workflows?
**Solution**: Controller classes coordinate services
**Benefit**: Clear entry points

### 4. Dependency Injection (Cell 13)
**Problem**: Hard to test with tight coupling
**Solution**: Pass dependencies to constructors
**Benefit**: Can inject mocks for testing

### 5. Singleton (Cell 7)
**Problem**: Reloading ML model wastes time/memory
**Solution**: Cache model instance
**Benefit**: Load once, use many times

## Code Duplication Elimination

### Before Refactoring:
1. **Firebase URLs**: Appeared 4+ times
2. **Disease detection**: 2 separate functions
3. **Text processing**: Duplicated in multiple places
4. **Sensor thresholds**: Scattered across code

### After Refactoring:
1. **Firebase URLs**: Config.FIREBASE_URL (1 place)
2. **Disease detection**: DiseaseDetector.process_image() (1 function)
3. **Text processing**: TextProcessor class (reused)
4. **Sensor thresholds**: Config.TEMP_*, Config.HUM_*, etc.

## Testing Strategy

Though this is a notebook, the architecture enables testing:

```python
# Mock dependencies
class MockFirebase:
    def get_sensor_history(self):
        return [{"temperature": 22.5, ...}]

# Test controller
mock_firebase = MockFirebase()
controller = SearchController(mock_firebase, mock_gemini)
result = controller.search("test")
assert result is not None
```

## Future Extensibility

Adding a new feature:

1. **Add configuration** to Config class
2. **Create repository method** if new data source
3. **Implement service** with business logic
4. **Create controller** to orchestrate
5. **Update UI** to display results

Example: Adding weather API integration
```python
# Cell 4: Add to Config
WEATHER_API_KEY = "..."

# Cell 7: New data access
class WeatherAPIClient:
    def get_forecast(self): ...

# Cell 10: New business logic
class WeatherAnalyzer:
    def assess_risk(self, forecast): ...

# Cell 12: Extend controller
class DashboardController:
    def get_dashboard_data(self):
        weather = weather_client.get_forecast()
        risk = weather_analyzer.assess_risk(weather)
        ...
```

## Performance Considerations

### Caching
- ML models loaded once
- Inverted index cached in Firebase
- Sensor data paginated

### Async Operations
- Firebase calls could be parallelized
- Image processing happens in background
- UI updates via callbacks

### Optimization Opportunities
1. Batch Firebase writes
2. Lazy load ML models
3. WebSocket for real-time sensors
4. CDN for static assets

## Security Considerations

### API Keys
- Store in Colab secrets
- Never commit to repo

### Input Validation
- Validate base64 images
- Sanitize search queries
- Check sensor value ranges

### Rate Limiting
- Gemini API has quotas
- Implement exponential backoff

## Conclusion

This architecture provides:
- ✅ **Maintainability**: Clear structure
- ✅ **Scalability**: Easy to extend
- ✅ **Testability**: Isolated components
- ✅ **Performance**: Efficient caching
- ✅ **Quality**: No duplication

The single-notebook constraint is satisfied while maintaining professional software engineering standards.
