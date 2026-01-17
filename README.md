# PlantGuard AI - Smart Greenhouse Management System

A comprehensive plant disease detection and greenhouse monitoring system built with AI, implemented as a Jupyter notebook with clean layered architecture.

## 🎯 Features

- **AI-Powered Disease Detection**: Analyzes plant leaf images using computer vision and ML models
- **Smart Search Engine**: RAG (Retrieval Augmented Generation) system for research articles
- **Real-time Monitoring**: IoT sensor integration (temperature, humidity, soil moisture)
- **Intelligent Chatbot**: AI assistant for plant care guidance
- **Risk Assessment**: Environmental condition analysis and disease risk prediction
- **Data Visualization**: Interactive dashboards and historical trends
- **Gamification**: XP system to encourage user engagement

## 📁 Files

- **SHON_REFACTORED.ipynb** - Main refactored notebook with clean architecture ⭐ **USE THIS**
- **SHON_FIX_HW3_Ant_with_Ai.ipynb** - Original version (kept for reference)

## 🏗️ Architecture

The refactored notebook follows a **4-layer architecture** organized in 17 cells:

```
┌─────────────────────────────────────────────┐
│  Presentation Layer (Cell 17)              │
│  - HTML/CSS/JavaScript UI                  │
│  - Interactive Dashboard                   │
└──────────────┬──────────────────────────────┘
               │
┌──────────────▼──────────────────────────────┐
│  Application Layer (Cells 11-12)           │
│  - ImageProcessingController               │
│  - SearchController                        │
└──────────────┬──────────────────────────────┘
               │
┌──────────────▼──────────────────────────────┐
│  Business Logic Layer (Cells 8-10)         │
│  - TextProcessor (NLP operations)          │
│  - SearchEngine (Inverted index + TF-IDF)  │
│  - DiseaseDetector (CV + ML)               │
└──────────────┬──────────────────────────────┘
               │
┌──────────────▼──────────────────────────────┐
│  Data Access Layer (Cells 5-7)             │
│  - FirebaseRepository                      │
│  - GeminiAIClient                          │
│  - ModelLoader                             │
└──────────────┬──────────────────────────────┘
               │
┌──────────────▼──────────────────────────────┐
│  Configuration Layer (Cell 4)              │
│  - Config (centralized constants)          │
└─────────────────────────────────────────────┘
```

### Layer Responsibilities

#### 1. Configuration Layer (Cell 4)
- Centralized constants and configuration
- Firebase URLs, API keys, thresholds
- Disease database definitions
- Sensor thresholds

#### 2. Data Access Layer (Cells 5-7)
- **FirebaseRepository**: All Firebase Realtime Database operations
- **GeminiAIClient**: Google Gemini AI API interactions
- **ModelLoader**: Hugging Face model management

#### 3. Business Logic Layer (Cells 8-10)
- **TextProcessor**: Tokenization, lemmatization, stopword removal
- **SearchEngine**: Inverted index, TF-IDF scoring, RAG implementation
- **DiseaseDetector**: HSV color analysis, plant classification, disease detection

#### 4. Application Layer (Cells 11-12)
- **ImageProcessingController**: Orchestrates image analysis workflow
- **SearchController**: Coordinates search and AI summary generation

#### 5. Presentation Layer (Cell 17)
- Complete HTML/CSS/JavaScript UI
- 4-tab interface (Dashboard, AI Scan, Analytics, Research)
- Real-time data visualization

## 🚀 Quick Start

### Prerequisites
- Google Colab account
- Google API Key (for Gemini AI)

### Running the Notebook

1. **Open in Google Colab**
   ```
   File → Open Notebook → GitHub → idoo25/shon → SHON_REFACTORED.ipynb
   ```

2. **Set API Key**
   - Get API key from [Google AI Studio](https://makersuite.google.com/app/apikey)
   - Set in Colab secrets or environment variable

3. **Run All Cells**
   ```
   Runtime → Run All
   ```

4. **Access UI**
   - Scroll to bottom for interactive dashboard
   - Click tabs to explore features

## 📦 Dependencies

```python
# Core ML/AI
transformers>=4.36.0
torch>=2.0.0
google-generativeai>=0.3.0

# Computer Vision
opencv-python>=4.8.0
pillow>=10.0.0

# NLP
nltk>=3.8

# Data & Utilities
requests>=2.31.0
numpy>=1.24.0
```

## 🔑 Key Improvements Over Original

### ✅ Architecture Quality
- **Layered architecture** - Clear separation of concerns
- **No duplication** - Merged duplicate functions
- **OOP design** - 8 well-defined classes
- **Single responsibility** - Each class has one job

### ✅ Maintainability
- **Centralized config** - All constants in one place
- **Clear structure** - 17 organized cells
- **Easy to extend** - Follow established patterns
- **Better testability** - Classes can be tested independently

### ✅ Code Quality
- **Type hints** - Better IDE support
- **Documentation** - Comprehensive docstrings
- **Error handling** - Proper exception management
- **Clean code** - Follows Python best practices

## 📊 Architecture Benefits

| Aspect | Before | After |
|--------|--------|-------|
| **Cells** | 19 mixed | 17 organized |
| **Classes** | 0 | 8 |
| **Duplication** | Yes | No |
| **Config** | Scattered | Centralized |
| **Testability** | Hard | Easy |
| **Maintainability** | Low | High |

## 🔧 Configuration

All configuration is centralized in the `Config` dataclass (Cell 4):

```python
@dataclass
class Config:
    # Firebase settings
    FIREBASE_URL = "https://..."
    
    # Sensor thresholds
    TEMP_MIN = 15.0
    TEMP_MAX = 28.0
    
    # Disease database
    DISEASES = {...}
    
    # Search settings
    MAX_RESULTS = 10
```

## 🧪 Testing

While this is a notebook, the architecture supports testing:

```python
# Example: Test text processor
text_processor = TextProcessor()
tokens = text_processor.tokenize("Plant disease detection")
assert len(tokens) > 0

# Example: Test disease detector
detector = DiseaseDetector()
result = detector.classify_plant(test_image)
assert result['is_valid'] in [True, False]
```

## 📝 Usage Examples

### Image Analysis
```python
# Via controller
result = image_controller.process_leaf(base64_image)
print(result['disease'])  # Disease info
print(result['confidence'])  # Confidence score
```

### Search
```python
# Via search controller
results = search_controller.search_with_summary("powdery mildew sage")
print(results['ai_summary'])  # AI-generated summary
print(results['articles'])  # Relevant articles
```

### Sensor Analysis
```python
# Via Firebase repository
data = firebase_repo.get_sensor_history(limit=100)
stats = firebase_repo.calculate_statistics(data)
print(stats['temperature']['average'])
```

## 🤝 Contributing

When adding features, follow the layered architecture:

1. **Add constants** to Config class (Cell 4)
2. **Create data access** in appropriate repository (Cells 5-7)
3. **Implement business logic** in service class (Cells 8-10)
4. **Orchestrate** in controller (Cells 11-12)
5. **Update UI** as needed (Cell 17)

## 📄 License

This project is part of a Cloud Computing course assignment.

## 🙏 Acknowledgments

- Plant classification model: `umutbozdag/plant-identity`
- AI: Google Gemini 1.5 Flash
- UI Framework: Custom HTML/CSS/JS
- Cloud: Google Firebase

## 📧 Contact

For questions or issues, please open an issue on GitHub.

---

**Built with ❤️ for smart agriculture and plant health**
