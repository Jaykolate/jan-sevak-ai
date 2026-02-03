# Jan-Sevak AI Bot - Technical Design Document

## Document Overview

**Project:** Jan-Sevak AI Bot  
**Version:** 1.0  
**Date:** February 2026  
**Purpose:** Technical design specification for AI for Bharat Hackathon

## Table of Contents

1. [System Architecture](#system-architecture)
2. [Component Design](#component-design)
3. [Data Models](#data-models)
4. [API Design](#api-design)
5. [User Flow Diagrams](#user-flow-diagrams)
6. [UI/UX Design Principles](#uiux-design-principles)
7. [Technology Stack Details](#technology-stack-details)
8. [Security & Privacy Design](#security--privacy-design)
9. [Performance Optimization](#performance-optimization)
10. [Testing Strategy](#testing-strategy)
11. [Error Handling & Edge Cases](#error-handling--edge-cases)
12. [Monitoring & Analytics](#monitoring--analytics)

---

## 1. System Architecture

### High-Level Architecture Overview

```
┌─────────────────────────────────────────────────────────────────┐
│                        USER INTERFACE LAYER                     │
├─────────────────────────────────────────────────────────────────┤
│  React Frontend (Mobile-First Responsive Design)               │
│  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐ ┌─────────────┐│
│  │ChatInterface│ │LanguageSelect│ │AudioPlayer  │ │SchemeCard   ││
│  └─────────────┘ └─────────────┘ └─────────────┘ └─────────────┘│
└─────────────────────────────────────────────────────────────────┘
                                │
                                ▼
┌─────────────────────────────────────────────────────────────────┐
│                     API/APPLICATION LAYER                       │
├─────────────────────────────────────────────────────────────────┤
│  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐ ┌─────────────┐│
│  │QueryParser  │ │SchemeRetriever│ │CacheManager │ │AnalyticsLog ││
│  └─────────────┘ └─────────────┘ └─────────────┘ └─────────────┘│
└─────────────────────────────────────────────────────────────────┘
                                │
                                ▼
┌─────────────────────────────────────────────────────────────────┐
│                      AI PROCESSING LAYER                        │
├─────────────────────────────────────────────────────────────────┤
│  ┌─────────────────────────────────────────────────────────────┐ │
│  │           Claude API Integration (Anthropic)                │ │
│  │  • Natural Language Understanding                          │ │
│  │  • Response Generation                                     │ │
│  │  • Context Management                                      │ │
│  └─────────────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────────────┘
                                │
                                ▼
┌─────────────────────────────────────────────────────────────────┐
│                   TEXT-TO-SPEECH SERVICE LAYER                  │
├─────────────────────────────────────────────────────────────────┤
│  ┌─────────────────────────────────────────────────────────────┐ │
│  │     Google Cloud TTS / Web Speech API                      │ │
│  │  • Hindi Voice Synthesis                                   │ │
│  │  • English Voice Synthesis                                 │ │
│  │  • Audio File Generation & Caching                         │ │
│  └─────────────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────────────┘
                                │
                                ▼
┌─────────────────────────────────────────────────────────────────┐
│                      DATA STORAGE LAYER                         │
├─────────────────────────────────────────────────────────────────┤
│  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐ ┌─────────────┐│
│  │JSON Scheme  │ │Audio Cache  │ │User Sessions│ │Analytics    ││
│  │Database     │ │Storage      │ │(Temporary)  │ │Data         ││
│  └─────────────┘ └─────────────┘ └─────────────┘ └─────────────┘│
└─────────────────────────────────────────────────────────────────┘
                                │
                                ▼
┌─────────────────────────────────────────────────────────────────┐
│                    EXTERNAL INTEGRATIONS                        │
├─────────────────────────────────────────────────────────────────┤
│  ┌─────────────────────────────────────────────────────────────┐ │
│  │              Government Portals                             │ │
│  │  • PM-KISAN Portal      • Ayushman Bharat Portal           │ │
│  │  • NSP Portal           • PM Awas Yojana Portal            │ │
│  │  • Official Links & Application Forms                      │ │
│  └─────────────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────────────┘
```
### Data Flow Architecture

```
User Query Input → Query Parser → Intent Analysis → Scheme Retriever
                                                          ↓
Audio Player ← TTS Service ← AI Response Generator ← Claude API
     ↓                              ↓
User Interface ← Response Formatter ← Cache Manager
```

**Detailed Data Flow:**

1. **User Input Processing**
   - User enters text query or uses voice input
   - Input sanitization and language detection
   - Query parsing for intent extraction

2. **AI Processing Pipeline**
   - Query sent to Claude API with context
   - Scheme-specific prompts for accurate responses
   - Response generation with confidence scoring

3. **Audio Generation**
   - Text response sent to TTS service
   - Audio file generation in user's language
   - Caching for frequently requested content

4. **Response Delivery**
   - Formatted response with text and audio
   - Related schemes and next steps
   - Official links and application guidance

---

## 2. Component Design

### Frontend Components Architecture

#### Core Components

**ChatInterface Component**
```javascript
// Primary conversation interface
ChatInterface {
  state: {
    messages: Array<Message>,
    isLoading: boolean,
    currentLanguage: string
  },
  methods: {
    sendMessage(),
    handleVoiceInput(),
    playAudio(),
    clearHistory()
  }
}
```

**InputBox Component**
```javascript
// Text and voice input handling
InputBox {
  props: {
    onSubmit: function,
    language: string,
    disabled: boolean
  },
  state: {
    inputText: string,
    isRecording: boolean,
    voiceSupported: boolean
  },
  methods: {
    handleTextInput(),
    startVoiceRecording(),
    stopVoiceRecording(),
    submitQuery()
  }
}
```

**LanguageSelector Component**
```javascript
// Language selection dropdown
LanguageSelector {
  props: {
    currentLanguage: string,
    onLanguageChange: function,
    supportedLanguages: Array<Language>
  },
  methods: {
    handleLanguageChange(),
    validateLanguageSupport()
  }
}
```

**AudioPlayer Component**
```javascript
// Audio playback with controls
AudioPlayer {
  props: {
    audioUrl: string,
    autoPlay: boolean,
    showControls: boolean
  },
  state: {
    isPlaying: boolean,
    currentTime: number,
    duration: number,
    playbackRate: number
  },
  methods: {
    play(),
    pause(),
    setPlaybackRate(),
    downloadAudio()
  }
}
```
**SchemeCard Component**
```javascript
// Display scheme information in card format
SchemeCard {
  props: {
    scheme: SchemeObject,
    language: string,
    showFullDetails: boolean
  },
  methods: {
    toggleDetails(),
    checkEligibility(),
    openApplicationLink(),
    shareScheme()
  }
}
```

**EligibilityForm Component**
```javascript
// Interactive eligibility checking
EligibilityForm {
  props: {
    scheme: SchemeObject,
    onComplete: function
  },
  state: {
    currentStep: number,
    userResponses: Object,
    eligibilityResult: Object
  },
  methods: {
    nextStep(),
    previousStep(),
    submitResponse(),
    calculateEligibility()
  }
}
```

**StepGuide Component**
```javascript
// Step-by-step application guidance
StepGuide {
  props: {
    steps: Array<Step>,
    currentStep: number,
    language: string
  },
  methods: {
    navigateToStep(),
    markStepComplete(),
    openExternalLink()
  }
}
```

### Backend/API Services

**QueryParser Service**
```javascript
// Analyzes user intent from input
QueryParser {
  methods: {
    parseQuery(input: string): ParsedQuery,
    detectLanguage(input: string): string,
    extractIntent(query: ParsedQuery): Intent,
    sanitizeInput(input: string): string
  }
}
```

**SchemeRetriever Service**
```javascript
// Fetches relevant schemes from database
SchemeRetriever {
  methods: {
    searchSchemes(query: string): Array<Scheme>,
    getSchemeById(id: string): Scheme,
    getSchemesByCategory(category: string): Array<Scheme>,
    filterByEligibility(schemes: Array<Scheme>, criteria: Object): Array<Scheme>
  }
}
```

**AIResponseGenerator Service**
```javascript
// Calls Claude API and formats responses
AIResponseGenerator {
  methods: {
    generateResponse(query: ParsedQuery, context: Object): Promise<Response>,
    formatResponse(rawResponse: string, language: string): FormattedResponse,
    calculateConfidence(response: Response): number,
    getSuggestedSchemes(query: ParsedQuery): Array<Scheme>
  }
}
```

**TTSService**
```javascript
// Converts text responses to audio
TTSService {
  methods: {
    generateAudio(text: string, language: string): Promise<AudioFile>,
    cacheAudio(text: string, audioUrl: string): void,
    getCachedAudio(text: string): string | null,
    validateAudioQuality(audioUrl: string): boolean
  }
}
```

---

## 3. Data Models

### Core Data Structures

**Scheme Object Structure**
```json
{
  "id": "pm-kisan-2024",
  "name": {
    "en": "PM-KISAN Farmer Income Support",
    "hi": "पीएम-किसान किसान आय सहायता",
    "regional": "ಪಿಎಂ-ಕಿಸಾನ್ ರೈತ ಆದಾಯ ಸಹಾಯ"
  },
  "category": "agriculture",
  "description": {
    "en": "Direct income support to farmer families",
    "hi": "किसान परिवारों को प्रत्यक्ष आय सहायता",
    "regional": "ರೈತ ಕುಟುಂಬಗಳಿಗೆ ನೇರ ಆದಾಯ ಸಹಾಯ"
  },
  "eligibility_criteria": [
    {
      "criterion": "Land ownership",
      "details": {
        "en": "Must own cultivable land",
        "hi": "खेती योग्य भूमि का मालिक होना चाहिए"
      }
    }
  ],
  "benefits": {
    "amount": "₹6,000 per year",
    "installments": "3 installments of ₹2,000",
    "duration": "Ongoing",
    "type": "Direct Bank Transfer"
  },
  "documents_required": [
    "Aadhaar Card",
    "Bank Account Details",
    "Land Records",
    "Mobile Number"
  ],
  "application_process": {
    "steps": [
      {
        "step": 1,
        "title": "Visit PM-KISAN Portal",
        "description": "Go to pmkisan.gov.in",
        "action": "registration"
      }
    ],
    "official_link": "https://pmkisan.gov.in",
    "helpline": "155261"
  },
  "faq": [
    {
      "question": {
        "en": "Who is eligible for PM-KISAN?",
        "hi": "पीएम-किसान के लिए कौन पात्र है?"
      },
      "answer": {
        "en": "All landholding farmer families",
        "hi": "सभी भूमिधारक किसान परिवार"
      }
    }
  ],
  "last_updated": "2024-02-01T00:00:00Z",
  "status": "active"
}
```
**User Query Object**
```json
{
  "query_id": "uuid-v4-string",
  "user_input": "मुझे पीएम किसान योजना के बारे में बताएं",
  "language": "hi",
  "timestamp": "2024-02-01T10:30:00Z",
  "intent": {
    "primary": "scheme_information",
    "secondary": "pm_kisan",
    "confidence": 0.95
  },
  "user_context": {
    "income_range": "below_2_lakh",
    "location": "rural",
    "age_group": "35-50",
    "occupation": "farmer",
    "previous_queries": ["ration_card", "crop_insurance"]
  },
  "session_id": "session-uuid",
  "device_info": {
    "type": "mobile",
    "os": "android",
    "browser": "chrome"
  }
}
```

**Response Object**
```json
{
  "response_id": "uuid-v4-string",
  "query_id": "uuid-v4-string",
  "text_response": {
    "en": "PM-KISAN provides ₹6,000 annual income support...",
    "hi": "पीएम-किसान ₹6,000 वार्षिक आय सहायता प्रदान करता है...",
    "formatted": "HTML formatted response with highlights"
  },
  "audio_url": "https://cdn.example.com/audio/response-uuid.mp3",
  "audio_duration": 45,
  "confidence_score": 0.92,
  "related_schemes": [
    {
      "id": "fasal-bima",
      "name": "Pradhan Mantri Fasal Bima Yojana",
      "relevance": 0.85
    }
  ],
  "next_steps": [
    {
      "action": "check_eligibility",
      "title": "Check Your Eligibility",
      "description": "Answer a few questions to see if you qualify"
    },
    {
      "action": "apply_now",
      "title": "Apply Online",
      "url": "https://pmkisan.gov.in"
    }
  ],
  "external_links": [
    {
      "title": "Official PM-KISAN Portal",
      "url": "https://pmkisan.gov.in",
      "type": "official"
    }
  ],
  "metadata": {
    "processing_time": 2.3,
    "tokens_used": 150,
    "cache_hit": false
  }
}
```

**Language Object**
```json
{
  "code": "hi",
  "name": "Hindi",
  "native_name": "हिन्दी",
  "tts_supported": true,
  "stt_supported": true,
  "voice_models": [
    {
      "gender": "female",
      "name": "hi-IN-Wavenet-A",
      "quality": "premium"
    }
  ],
  "rtl": false,
  "status": "active"
}
```

---

## 4. API Design

### RESTful API Endpoints

**POST /api/ask**
Submit a question and get AI-generated response

*Request:*
```json
{
  "query": "मुझे पीएम किसान योजना के बारे में बताएं",
  "language": "hi",
  "user_context": {
    "location": "rural",
    "occupation": "farmer"
  },
  "session_id": "optional-session-id"
}
```

*Response:*
```json
{
  "success": true,
  "data": {
    "response_id": "uuid-v4-string",
    "text_response": "पीएम-किसान योजना भारत सरकार की एक महत्वपूर्ण योजना है...",
    "audio_url": "https://cdn.example.com/audio/response-uuid.mp3",
    "confidence_score": 0.92,
    "related_schemes": [...],
    "next_steps": [...]
  },
  "metadata": {
    "processing_time": 2.3,
    "language": "hi"
  }
}
```

**GET /api/schemes**
List all available government schemes

*Request Parameters:*
- `category` (optional): Filter by scheme category
- `language` (optional): Response language (default: en)
- `limit` (optional): Number of schemes to return (default: 10)
- `offset` (optional): Pagination offset (default: 0)

*Response:*
```json
{
  "success": true,
  "data": {
    "schemes": [
      {
        "id": "pm-kisan",
        "name": "PM-KISAN Farmer Income Support",
        "category": "agriculture",
        "description": "Direct income support to farmer families",
        "benefits": "₹6,000 per year"
      }
    ],
    "total_count": 10,
    "has_more": false
  }
}
```
**GET /api/schemes/:id**
Get specific scheme details

*Response:*
```json
{
  "success": true,
  "data": {
    "scheme": {
      "id": "pm-kisan",
      "name": {...},
      "description": {...},
      "eligibility_criteria": [...],
      "benefits": {...},
      "application_process": {...}
    }
  }
}
```

**POST /api/check-eligibility**
Check user eligibility for specific scheme

*Request:*
```json
{
  "scheme_id": "pm-kisan",
  "user_data": {
    "land_ownership": true,
    "annual_income": 150000,
    "age": 45,
    "location": "rural"
  },
  "language": "hi"
}
```

*Response:*
```json
{
  "success": true,
  "data": {
    "eligible": true,
    "confidence": 0.95,
    "explanation": {
      "hi": "आप इस योजना के लिए पात्र हैं क्योंकि...",
      "en": "You are eligible for this scheme because..."
    },
    "missing_requirements": [],
    "next_steps": [...]
  }
}
```

**GET /api/languages**
List supported languages

*Response:*
```json
{
  "success": true,
  "data": {
    "languages": [
      {
        "code": "hi",
        "name": "Hindi",
        "native_name": "हिन्दी",
        "tts_supported": true,
        "stt_supported": true
      },
      {
        "code": "en",
        "name": "English",
        "native_name": "English",
        "tts_supported": true,
        "stt_supported": true
      }
    ]
  }
}
```

### Error Response Format
```json
{
  "success": false,
  "error": {
    "code": "INVALID_QUERY",
    "message": "The query could not be processed",
    "details": "Query must be at least 3 characters long",
    "timestamp": "2024-02-01T10:30:00Z"
  }
}
```

---

## 5. User Flow Diagrams

### First-Time User Onboarding Flow

```
Start → Language Selection → Welcome Message → Feature Tour → First Query
  ↓
Language Selection Screen:
- Hindi (हिन्दी)
- English
- Regional Language (if available)
  ↓
Welcome Message (Audio + Text):
"नमस्ते! मैं जन-सेवक हूँ। मैं आपको सरकारी योजनाओं के बारे में बताने में मदद करूंगा।"
  ↓
Feature Tour (3 screens):
1. "Ask questions about government schemes"
2. "Listen to answers in your language"
3. "Get step-by-step application guidance"
  ↓
Prompt for First Query:
"आप किस योजना के बारे में जानना चाहते हैं?"
```

### Basic Question-Answer Flow

```
User Input → Query Processing → AI Response → Audio Generation → Display Response
     ↓              ↓               ↓              ↓              ↓
Text/Voice → Intent Analysis → Claude API → TTS Service → Chat Interface
     ↓              ↓               ↓              ↓              ↓
Validation → Scheme Matching → Response Gen → Audio Cache → User Feedback
```

**Detailed Steps:**
1. User enters question (text or voice)
2. Input validation and sanitization
3. Language detection and intent analysis
4. Relevant scheme identification
5. Context-aware prompt sent to Claude API
6. Response generation and formatting
7. Text-to-speech conversion
8. Display response with audio playback
9. Show related schemes and next steps
10. Collect user feedback (optional)

### Eligibility Checking Flow

```
Scheme Selection → Question 1 → Question 2 → ... → Question N → Result Display
       ↓              ↓           ↓                    ↓              ↓
   User Choice → Income Range → Age Group → ... → Location → Eligibility Score
       ↓              ↓           ↓                    ↓              ↓
   Validation → Store Response → Next Question → Final Calc → Show Result + Next Steps
```

**Interactive Questions:**
1. "What is your annual family income?"
2. "Do you own agricultural land?"
3. "What is your age?"
4. "Which state do you live in?"
5. "Are you currently employed?"

**Result Display:**
- Eligibility status (Eligible/Not Eligible/Partially Eligible)
- Explanation in user's language
- Missing requirements (if any)
- Application guidance
- Alternative scheme suggestions
### Multi-Step Conversation Flow

```
Initial Query → Follow-up Questions → Clarification → Detailed Response → Action Items
      ↓               ↓                    ↓              ↓               ↓
"Tell me about → "Which type of → "For farming or → Specific scheme → Application
scholarships"    scholarship?"     business?"        information      guidance
      ↓               ↓                    ↓              ↓               ↓
Context Build → Intent Refinement → Scheme Filter → Personalized → Next Steps
                                                     Response
```

### Voice Input Flow

```
Voice Button Press → Permission Request → Recording Start → Speech Recognition → Text Processing
         ↓                    ↓                ↓                   ↓                ↓
    User Action → Microphone Access → Audio Capture → STT Service → Query Processing
         ↓                    ↓                ↓                   ↓                ↓
    Visual Feedback → Recording UI → Waveform Display → Text Display → Normal Flow
```

### Scheme Application Guidance Flow

```
Scheme Selection → Document Checklist → Step-by-Step Guide → External Links → Progress Tracking
       ↓                   ↓                    ↓                ↓               ↓
   User Choice → Required Docs → Application Steps → Official Portal → User Completion
       ↓                   ↓                    ↓                ↓               ↓
   Validation → Download Links → Visual Guide → External Site → Return to App
```

---

## 6. UI/UX Design Principles

### Design Philosophy

**Mobile-First Approach (80% users on mobile)**
- Touch-friendly interface with minimum 44px touch targets
- Thumb-friendly navigation with bottom-aligned primary actions
- Responsive design that works on screens from 320px to 1920px
- Progressive enhancement for larger screens

**Accessibility-First Design**
- High contrast ratios (minimum 4.5:1 for normal text)
- Large, readable fonts (minimum 16px on mobile)
- Screen reader compatibility with proper ARIA labels
- Keyboard navigation support
- Audio alternatives for all text content

**Simplicity Over Features**
- Single-purpose screens with clear primary actions
- Minimal cognitive load with progressive disclosure
- Clear visual hierarchy with consistent spacing
- Familiar UI patterns and conventions

**Cultural Appropriateness**
- Indian design language with familiar visual metaphors
- Respectful use of cultural colors and symbols
- Local context in examples and illustrations
- Support for right-to-left languages (future)

### Visual Design System

**Color Palette**
```css
/* Primary Colors */
--saffron: #FF9933;      /* Primary brand color */
--green: #138808;        /* Success and positive actions */
--white: #FFFFFF;        /* Background and text */
--navy: #000080;         /* Secondary brand color */

/* Semantic Colors */
--success: #28a745;      /* Success messages */
--warning: #ffc107;      /* Warning messages */
--error: #dc3545;        /* Error messages */
--info: #17a2b8;         /* Information messages */

/* Neutral Colors */
--gray-50: #f8f9fa;      /* Light background */
--gray-100: #e9ecef;     /* Border color */
--gray-500: #6c757d;     /* Secondary text */
--gray-900: #212529;     /* Primary text */
```

**Typography System**
```css
/* Hindi Text */
font-family: 'Noto Sans Devanagari', sans-serif;

/* English Text */
font-family: 'Inter', 'Segoe UI', sans-serif;

/* Font Sizes */
--text-xs: 12px;         /* Small labels */
--text-sm: 14px;         /* Secondary text */
--text-base: 16px;       /* Body text */
--text-lg: 18px;         /* Large body text */
--text-xl: 20px;         /* Small headings */
--text-2xl: 24px;        /* Medium headings */
--text-3xl: 30px;        /* Large headings */
```

**Spacing System**
```css
--space-1: 4px;          /* Tight spacing */
--space-2: 8px;          /* Small spacing */
--space-3: 12px;         /* Medium spacing */
--space-4: 16px;         /* Default spacing */
--space-6: 24px;         /* Large spacing */
--space-8: 32px;         /* Extra large spacing */
--space-12: 48px;        /* Section spacing */
```

**Icon Design Principles**
- Large, simple icons (minimum 24px)
- Culturally relevant symbols
- Consistent stroke width (2px)
- High contrast against backgrounds
- Meaningful and universally understood

### Interaction Design

**Audio-First Interactions**
- Auto-play audio responses (with user control)
- Visual waveform during audio playback
- Playback speed controls (0.5x, 1x, 1.5x, 2x)
- Download option for offline listening

**Loading States**
- Skeleton screens for content loading
- Progress indicators for multi-step processes
- Animated feedback for user actions
- Clear error states with recovery options

**Feedback Mechanisms**
- Haptic feedback on mobile devices
- Visual confirmation for all actions
- Toast notifications for system messages
- Contextual help and tooltips
**Progressive Disclosure**
- Show essential information first
- "Show more" options for detailed content
- Collapsible sections for complex information
- Step-by-step revelation of application processes

---

## 7. Technology Stack Details

### Frontend Technologies

**React 18 with Modern Patterns**
```javascript
// Functional components with hooks
import React, { useState, useEffect, useCallback } from 'react';

// Context for global state management
const AppContext = React.createContext();

// Custom hooks for reusable logic
const useAudioPlayer = () => {
  const [isPlaying, setIsPlaying] = useState(false);
  const [currentTime, setCurrentTime] = useState(0);
  // ... audio logic
};

// Error boundaries for graceful error handling
class ErrorBoundary extends React.Component {
  // ... error boundary implementation
}
```

**Tailwind CSS for Styling**
```css
/* Utility-first CSS framework */
.chat-message {
  @apply p-4 mb-3 rounded-lg shadow-sm;
}

.chat-message.user {
  @apply bg-blue-100 ml-8;
}

.chat-message.bot {
  @apply bg-gray-100 mr-8;
}

/* Custom components */
.btn-primary {
  @apply bg-saffron text-white px-6 py-3 rounded-lg font-medium hover:bg-saffron-dark transition-colors;
}
```

**HTTP Client with Axios**
```javascript
// API client configuration
const apiClient = axios.create({
  baseURL: process.env.REACT_APP_API_BASE_URL,
  timeout: 10000,
  headers: {
    'Content-Type': 'application/json',
  },
});

// Request interceptor for authentication
apiClient.interceptors.request.use((config) => {
  // Add auth token if available
  return config;
});

// Response interceptor for error handling
apiClient.interceptors.response.use(
  (response) => response,
  (error) => {
    // Global error handling
    return Promise.reject(error);
  }
);
```

**Speech Recognition Integration**
```javascript
// Web Speech API wrapper
const useSpeechRecognition = () => {
  const [isListening, setIsListening] = useState(false);
  const [transcript, setTranscript] = useState('');
  
  const SpeechRecognition = window.SpeechRecognition || window.webkitSpeechRecognition;
  const recognition = new SpeechRecognition();
  
  recognition.lang = 'hi-IN'; // Hindi language
  recognition.continuous = false;
  recognition.interimResults = false;
  
  // ... speech recognition logic
};
```

### AI & Backend Integration

**Claude API Integration**
```javascript
// Claude API service
class ClaudeService {
  constructor(apiKey) {
    this.apiKey = apiKey;
    this.baseURL = 'https://api.anthropic.com/v1';
  }
  
  async generateResponse(query, context = {}) {
    const prompt = this.buildPrompt(query, context);
    
    const response = await fetch(`${this.baseURL}/messages`, {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
        'x-api-key': this.apiKey,
        'anthropic-version': '2023-06-01'
      },
      body: JSON.stringify({
        model: 'claude-3-sonnet-20240229',
        max_tokens: 1000,
        messages: [
          {
            role: 'user',
            content: prompt
          }
        ]
      })
    });
    
    return response.json();
  }
  
  buildPrompt(query, context) {
    return `
      You are Jan-Sevak, an AI assistant helping Indian citizens understand government schemes.
      
      Context: ${JSON.stringify(context)}
      User Query: ${query}
      
      Please provide a helpful, accurate response in simple language.
      Focus on practical information and next steps.
    `;
  }
}
```

**Text-to-Speech Service**
```javascript
// Google Cloud TTS integration
class TTSService {
  constructor(apiKey) {
    this.apiKey = apiKey;
    this.baseURL = 'https://texttospeech.googleapis.com/v1';
  }
  
  async generateAudio(text, languageCode = 'hi-IN') {
    const response = await fetch(`${this.baseURL}/text:synthesize?key=${this.apiKey}`, {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({
        input: { text },
        voice: {
          languageCode,
          name: 'hi-IN-Wavenet-A',
          ssmlGender: 'FEMALE'
        },
        audioConfig: {
          audioEncoding: 'MP3',
          speakingRate: 1.0,
          pitch: 0.0
        }
      })
    });
    
    const data = await response.json();
    return data.audioContent; // Base64 encoded audio
  }
}

// Web Speech API fallback
class WebSpeechTTS {
  speak(text, lang = 'hi-IN') {
    if ('speechSynthesis' in window) {
      const utterance = new SpeechSynthesisUtterance(text);
      utterance.lang = lang;
      utterance.rate = 0.9;
      utterance.pitch = 1.0;
      speechSynthesis.speak(utterance);
    }
  }
}
```

### Data Storage & Caching

**JSON-based Scheme Database**
```javascript
// Scheme data structure
const schemeDatabase = {
  schemes: [
    {
      id: 'pm-kisan',
      // ... scheme data
    }
  ],
  categories: ['agriculture', 'education', 'health', 'housing'],
  lastUpdated: '2024-02-01T00:00:00Z'
};

// Database service
class SchemeDatabase {
  constructor(data) {
    this.schemes = new Map(data.schemes.map(s => [s.id, s]));
    this.categories = data.categories;
  }
  
  searchSchemes(query, language = 'en') {
    // Full-text search implementation
    return this.schemes.values().filter(scheme => 
      this.matchesQuery(scheme, query, language)
    );
  }
  
  getSchemeById(id) {
    return this.schemes.get(id);
  }
  
  getSchemesByCategory(category) {
    return Array.from(this.schemes.values())
      .filter(scheme => scheme.category === category);
  }
}
```

**Caching Strategy**
```javascript
// Browser storage for caching
class CacheManager {
  constructor() {
    this.audioCache = new Map();
    this.responseCache = new Map();
    this.maxCacheSize = 50; // Maximum cached items
  }
  
  // Cache audio files
  cacheAudio(text, audioBlob) {
    const key = this.generateKey(text);
    if (this.audioCache.size >= this.maxCacheSize) {
      this.evictOldest(this.audioCache);
    }
    this.audioCache.set(key, {
      blob: audioBlob,
      timestamp: Date.now()
    });
  }
  
  getCachedAudio(text) {
    const key = this.generateKey(text);
    const cached = this.audioCache.get(key);
    if (cached && this.isValid(cached.timestamp)) {
      return cached.blob;
    }
    return null;
  }
  
  generateKey(text) {
    return btoa(text).substring(0, 32); // Simple hash
  }
  
  isValid(timestamp) {
    const maxAge = 24 * 60 * 60 * 1000; // 24 hours
    return Date.now() - timestamp < maxAge;
  }
}
```
### Development Tools & Build Process

**Vite Configuration**
```javascript
// vite.config.js
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';

export default defineConfig({
  plugins: [react()],
  build: {
    outDir: 'dist',
    sourcemap: true,
    rollupOptions: {
      output: {
        manualChunks: {
          vendor: ['react', 'react-dom'],
          audio: ['@/services/tts', '@/services/speech']
        }
      }
    }
  },
  server: {
    port: 3000,
    proxy: {
      '/api': {
        target: 'http://localhost:8000',
        changeOrigin: true
      }
    }
  }
});
```

**ESLint & Prettier Configuration**
```json
// .eslintrc.json
{
  "extends": [
    "react-app",
    "react-app/jest",
    "@typescript-eslint/recommended"
  ],
  "rules": {
    "react-hooks/exhaustive-deps": "warn",
    "no-unused-vars": "error",
    "prefer-const": "error"
  }
}

// .prettierrc
{
  "semi": true,
  "trailingComma": "es5",
  "singleQuote": true,
  "printWidth": 80,
  "tabWidth": 2
}
```

**GitHub Actions CI/CD**
```yaml
# .github/workflows/deploy.yml
name: Deploy to Vercel

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: '18'
      - run: npm ci
      - run: npm run test
      - run: npm run build

  deploy:
    needs: test
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    steps:
      - uses: actions/checkout@v3
      - uses: amondnet/vercel-action@v20
        with:
          vercel-token: ${{ secrets.VERCEL_TOKEN }}
          vercel-org-id: ${{ secrets.ORG_ID }}
          vercel-project-id: ${{ secrets.PROJECT_ID }}
```

---

## 8. Security & Privacy Design

### Data Protection Strategy

**No PII Storage Policy**
```javascript
// Data sanitization before processing
class DataSanitizer {
  static sanitizeUserInput(input) {
    // Remove potential PII patterns
    const piiPatterns = [
      /\b\d{4}\s?\d{4}\s?\d{4}\b/g, // Aadhaar numbers
      /\b\d{10}\b/g, // Phone numbers
      /\b[A-Z]{5}\d{4}[A-Z]\b/g, // PAN numbers
    ];
    
    let sanitized = input;
    piiPatterns.forEach(pattern => {
      sanitized = sanitized.replace(pattern, '[REDACTED]');
    });
    
    return sanitized;
  }
  
  static anonymizeAnalytics(data) {
    return {
      query_type: data.intent,
      language: data.language,
      timestamp: data.timestamp,
      session_hash: this.hashSession(data.session_id)
    };
  }
}
```

**Environment Variables Management**
```javascript
// Environment configuration
const config = {
  claude: {
    apiKey: process.env.REACT_APP_CLAUDE_API_KEY,
    baseURL: process.env.REACT_APP_CLAUDE_BASE_URL
  },
  tts: {
    apiKey: process.env.REACT_APP_TTS_API_KEY,
    service: process.env.REACT_APP_TTS_SERVICE || 'web'
  },
  analytics: {
    enabled: process.env.REACT_APP_ANALYTICS_ENABLED === 'true',
    endpoint: process.env.REACT_APP_ANALYTICS_ENDPOINT
  }
};

// Validate required environment variables
const requiredEnvVars = ['REACT_APP_CLAUDE_API_KEY'];
requiredEnvVars.forEach(envVar => {
  if (!process.env[envVar]) {
    throw new Error(`Missing required environment variable: ${envVar}`);
  }
});
```

**Rate Limiting Implementation**
```javascript
// Client-side rate limiting
class RateLimiter {
  constructor(maxRequests = 100, windowMs = 3600000) { // 100 requests per hour
    this.maxRequests = maxRequests;
    this.windowMs = windowMs;
    this.requests = new Map();
  }
  
  isAllowed(clientId) {
    const now = Date.now();
    const clientRequests = this.requests.get(clientId) || [];
    
    // Remove old requests outside the window
    const validRequests = clientRequests.filter(
      timestamp => now - timestamp < this.windowMs
    );
    
    if (validRequests.length >= this.maxRequests) {
      return false;
    }
    
    validRequests.push(now);
    this.requests.set(clientId, validRequests);
    return true;
  }
}
```

**Input Sanitization**
```javascript
// XSS prevention
class InputValidator {
  static sanitizeHTML(input) {
    const div = document.createElement('div');
    div.textContent = input;
    return div.innerHTML;
  }
  
  static validateQuery(query) {
    if (!query || typeof query !== 'string') {
      throw new Error('Invalid query format');
    }
    
    if (query.length < 3 || query.length > 500) {
      throw new Error('Query length must be between 3 and 500 characters');
    }
    
    // Remove potentially malicious patterns
    const maliciousPatterns = [
      /<script\b[^<]*(?:(?!<\/script>)<[^<]*)*<\/script>/gi,
      /javascript:/gi,
      /on\w+\s*=/gi
    ];
    
    let sanitized = query;
    maliciousPatterns.forEach(pattern => {
      sanitized = sanitized.replace(pattern, '');
    });
    
    return sanitized.trim();
  }
}
```

### HTTPS and CORS Configuration

**Security Headers**
```javascript
// Security middleware (if using Express.js backend)
app.use((req, res, next) => {
  res.setHeader('X-Content-Type-Options', 'nosniff');
  res.setHeader('X-Frame-Options', 'DENY');
  res.setHeader('X-XSS-Protection', '1; mode=block');
  res.setHeader('Strict-Transport-Security', 'max-age=31536000; includeSubDomains');
  res.setHeader('Content-Security-Policy', 
    "default-src 'self'; script-src 'self' 'unsafe-inline'; style-src 'self' 'unsafe-inline';"
  );
  next();
});

// CORS configuration
const corsOptions = {
  origin: process.env.ALLOWED_ORIGINS?.split(',') || ['http://localhost:3000'],
  methods: ['GET', 'POST'],
  allowedHeaders: ['Content-Type', 'Authorization'],
  credentials: false
};
app.use(cors(corsOptions));
```

---

## 9. Performance Optimization

### Frontend Optimization Strategies

**Code Splitting & Lazy Loading**
```javascript
// Route-based code splitting
import { lazy, Suspense } from 'react';

const ChatInterface = lazy(() => import('./components/ChatInterface'));
const SchemeDetails = lazy(() => import('./components/SchemeDetails'));
const EligibilityChecker = lazy(() => import('./components/EligibilityChecker'));

function App() {
  return (
    <Router>
      <Suspense fallback={<LoadingSpinner />}>
        <Routes>
          <Route path="/" element={<ChatInterface />} />
          <Route path="/scheme/:id" element={<SchemeDetails />} />
          <Route path="/eligibility" element={<EligibilityChecker />} />
        </Routes>
      </Suspense>
    </Router>
  );
}

// Component-level lazy loading
const AudioPlayer = lazy(() => 
  import('./AudioPlayer').then(module => ({ default: module.AudioPlayer }))
);
```

**Debouncing for Search Inputs**
```javascript
// Custom debounce hook
function useDebounce(value, delay) {
  const [debouncedValue, setDebouncedValue] = useState(value);
  
  useEffect(() => {
    const handler = setTimeout(() => {
      setDebouncedValue(value);
    }, delay);
    
    return () => {
      clearTimeout(handler);
    };
  }, [value, delay]);
  
  return debouncedValue;
}

// Usage in search component
function SearchInput({ onSearch }) {
  const [query, setQuery] = useState('');
  const debouncedQuery = useDebounce(query, 300);
  
  useEffect(() => {
    if (debouncedQuery.length >= 3) {
      onSearch(debouncedQuery);
    }
  }, [debouncedQuery, onSearch]);
  
  return (
    <input
      value={query}
      onChange={(e) => setQuery(e.target.value)}
      placeholder="Search schemes..."
    />
  );
}
```
**Audio File Caching & Optimization**
```javascript
// Audio caching service
class AudioCache {
  constructor() {
    this.cache = new Map();
    this.maxSize = 50 * 1024 * 1024; // 50MB cache limit
    this.currentSize = 0;
  }
  
  async cacheAudio(key, audioBlob) {
    const size = audioBlob.size;
    
    // Evict old entries if needed
    while (this.currentSize + size > this.maxSize && this.cache.size > 0) {
      this.evictLRU();
    }
    
    // Compress audio if needed
    const compressedBlob = await this.compressAudio(audioBlob);
    
    this.cache.set(key, {
      blob: compressedBlob,
      timestamp: Date.now(),
      size: compressedBlob.size
    });
    
    this.currentSize += compressedBlob.size;
  }
  
  async compressAudio(audioBlob) {
    // Use Web Audio API for compression
    const audioContext = new AudioContext();
    const arrayBuffer = await audioBlob.arrayBuffer();
    const audioBuffer = await audioContext.decodeAudioData(arrayBuffer);
    
    // Reduce sample rate for smaller file size
    const offlineContext = new OfflineAudioContext(
      audioBuffer.numberOfChannels,
      audioBuffer.duration * 22050, // Reduce from 44.1kHz to 22kHz
      22050
    );
    
    const source = offlineContext.createBufferSource();
    source.buffer = audioBuffer;
    source.connect(offlineContext.destination);
    source.start();
    
    const compressedBuffer = await offlineContext.startRendering();
    return this.audioBufferToBlob(compressedBuffer);
  }
}
```

**Image Optimization**
```javascript
// Lazy loading images with intersection observer
function LazyImage({ src, alt, className }) {
  const [isLoaded, setIsLoaded] = useState(false);
  const [isInView, setIsInView] = useState(false);
  const imgRef = useRef();
  
  useEffect(() => {
    const observer = new IntersectionObserver(
      ([entry]) => {
        if (entry.isIntersecting) {
          setIsInView(true);
          observer.disconnect();
        }
      },
      { threshold: 0.1 }
    );
    
    if (imgRef.current) {
      observer.observe(imgRef.current);
    }
    
    return () => observer.disconnect();
  }, []);
  
  return (
    <div ref={imgRef} className={className}>
      {isInView && (
        <img
          src={src}
          alt={alt}
          onLoad={() => setIsLoaded(true)}
          style={{
            opacity: isLoaded ? 1 : 0,
            transition: 'opacity 0.3s ease'
          }}
        />
      )}
    </div>
  );
}
```

**Service Worker for Offline Capability**
```javascript
// service-worker.js
const CACHE_NAME = 'jan-sevak-v1';
const urlsToCache = [
  '/',
  '/static/js/bundle.js',
  '/static/css/main.css',
  '/data/schemes.json'
];

self.addEventListener('install', (event) => {
  event.waitUntil(
    caches.open(CACHE_NAME)
      .then((cache) => cache.addAll(urlsToCache))
  );
});

self.addEventListener('fetch', (event) => {
  event.respondWith(
    caches.match(event.request)
      .then((response) => {
        // Return cached version or fetch from network
        return response || fetch(event.request);
      })
  );
});
```

### Backend Performance Optimization

**Response Caching Strategy**
```javascript
// In-memory cache for frequent queries
class ResponseCache {
  constructor(maxSize = 1000, ttl = 3600000) { // 1 hour TTL
    this.cache = new Map();
    this.maxSize = maxSize;
    this.ttl = ttl;
  }
  
  get(key) {
    const item = this.cache.get(key);
    if (!item) return null;
    
    if (Date.now() - item.timestamp > this.ttl) {
      this.cache.delete(key);
      return null;
    }
    
    // Move to end (LRU)
    this.cache.delete(key);
    this.cache.set(key, item);
    return item.data;
  }
  
  set(key, data) {
    if (this.cache.size >= this.maxSize) {
      // Remove oldest item
      const firstKey = this.cache.keys().next().value;
      this.cache.delete(firstKey);
    }
    
    this.cache.set(key, {
      data,
      timestamp: Date.now()
    });
  }
}
```

---

## 10. Testing Strategy

### Unit Testing Framework

**React Component Testing**
```javascript
// ChatInterface.test.js
import { render, screen, fireEvent, waitFor } from '@testing-library/react';
import { ChatInterface } from './ChatInterface';

describe('ChatInterface', () => {
  test('renders chat interface correctly', () => {
    render(<ChatInterface />);
    expect(screen.getByPlaceholderText('Ask about government schemes...')).toBeInTheDocument();
    expect(screen.getByRole('button', { name: /send/i })).toBeInTheDocument();
  });
  
  test('sends message when form is submitted', async () => {
    const mockOnSendMessage = jest.fn();
    render(<ChatInterface onSendMessage={mockOnSendMessage} />);
    
    const input = screen.getByPlaceholderText('Ask about government schemes...');
    const sendButton = screen.getByRole('button', { name: /send/i });
    
    fireEvent.change(input, { target: { value: 'Tell me about PM-KISAN' } });
    fireEvent.click(sendButton);
    
    await waitFor(() => {
      expect(mockOnSendMessage).toHaveBeenCalledWith('Tell me about PM-KISAN');
    });
  });
  
  test('displays audio player when response includes audio', async () => {
    const mockResponse = {
      text: 'PM-KISAN provides income support...',
      audioUrl: 'https://example.com/audio.mp3'
    };
    
    render(<ChatInterface initialResponse={mockResponse} />);
    
    expect(screen.getByRole('button', { name: /play audio/i })).toBeInTheDocument();
  });
});
```

**API Service Testing**
```javascript
// ClaudeService.test.js
import { ClaudeService } from './ClaudeService';

describe('ClaudeService', () => {
  let service;
  
  beforeEach(() => {
    service = new ClaudeService('test-api-key');
    global.fetch = jest.fn();
  });
  
  test('generates response for valid query', async () => {
    const mockResponse = {
      content: [{ text: 'PM-KISAN is a farmer income support scheme...' }]
    };
    
    fetch.mockResolvedValueOnce({
      ok: true,
      json: async () => mockResponse
    });
    
    const result = await service.generateResponse('Tell me about PM-KISAN');
    
    expect(fetch).toHaveBeenCalledWith(
      expect.stringContaining('/messages'),
      expect.objectContaining({
        method: 'POST',
        headers: expect.objectContaining({
          'x-api-key': 'test-api-key'
        })
      })
    );
    
    expect(result.content[0].text).toContain('PM-KISAN');
  });
  
  test('handles API errors gracefully', async () => {
    fetch.mockRejectedValueOnce(new Error('API Error'));
    
    await expect(service.generateResponse('test query')).rejects.toThrow('API Error');
  });
});
```

**Language Translation Testing**
```javascript
// LanguageService.test.js
import { LanguageService } from './LanguageService';

describe('LanguageService', () => {
  test('detects Hindi language correctly', () => {
    const hindiText = 'मुझे पीएम किसान योजना के बारे में बताएं';
    const detectedLang = LanguageService.detectLanguage(hindiText);
    expect(detectedLang).toBe('hi');
  });
  
  test('translates scheme names correctly', () => {
    const schemeName = 'PM-KISAN Farmer Income Support';
    const hindiName = LanguageService.translate(schemeName, 'en', 'hi');
    expect(hindiName).toBe('पीएम-किसान किसान आय सहायता');
  });
});
```

### Integration Testing

**End-to-End User Flow Testing**
```javascript
// cypress/integration/user-flow.spec.js
describe('Jan-Sevak User Flow', () => {
  beforeEach(() => {
    cy.visit('/');
  });
  
  it('completes full question-answer flow', () => {
    // Select language
    cy.get('[data-testid="language-selector"]').select('hi');
    
    // Type question
    cy.get('[data-testid="chat-input"]')
      .type('मुझे पीएम किसान योजना के बारे में बताएं');
    
    // Send message
    cy.get('[data-testid="send-button"]').click();
    
    // Wait for response
    cy.get('[data-testid="bot-message"]', { timeout: 10000 })
      .should('contain', 'पीएम-किसान');
    
    // Check audio player appears
    cy.get('[data-testid="audio-player"]').should('be.visible');
    
    // Play audio
    cy.get('[data-testid="play-button"]').click();
    
    // Check related schemes appear
    cy.get('[data-testid="related-schemes"]').should('be.visible');
  });
  
  it('handles eligibility checking flow', () => {
    cy.get('[data-testid="chat-input"]')
      .type('Am I eligible for PM-KISAN?');
    
    cy.get('[data-testid="send-button"]').click();
    
    // Should show eligibility form
    cy.get('[data-testid="eligibility-form"]', { timeout: 5000 })
      .should('be.visible');
    
    // Fill eligibility form
    cy.get('[data-testid="land-ownership"]').select('yes');
    cy.get('[data-testid="annual-income"]').type('150000');
    cy.get('[data-testid="submit-eligibility"]').click();
    
    // Check result
    cy.get('[data-testid="eligibility-result"]')
      .should('contain', 'eligible');
  });
});
```
### User Acceptance Testing

**Device Compatibility Testing**
```javascript
// Device testing configuration
const testDevices = [
  {
    name: 'Low-end Android',
    specs: { ram: '2GB', os: 'Android 6.0', browser: 'Chrome 70' },
    tests: ['basic_functionality', 'audio_playback', 'voice_input']
  },
  {
    name: 'Mid-range Android',
    specs: { ram: '4GB', os: 'Android 10', browser: 'Chrome 90' },
    tests: ['full_functionality', 'performance', 'offline_mode']
  },
  {
    name: 'iPhone SE',
    specs: { ram: '3GB', os: 'iOS 14', browser: 'Safari' },
    tests: ['ios_compatibility', 'audio_quality', 'touch_interface']
  }
];

// Performance testing
const performanceTests = {
  pageLoad: { target: '<3s', critical: '<5s' },
  apiResponse: { target: '<2s', critical: '<5s' },
  audioGeneration: { target: '<5s', critical: '<10s' },
  voiceRecognition: { target: '<3s', critical: '<7s' }
};
```

**Accessibility Testing**
```javascript
// Automated accessibility testing
describe('Accessibility Tests', () => {
  test('meets WCAG 2.1 AA standards', async () => {
    const { container } = render(<App />);
    const results = await axe(container);
    expect(results).toHaveNoViolations();
  });
  
  test('supports keyboard navigation', () => {
    render(<ChatInterface />);
    
    // Tab through interactive elements
    const input = screen.getByRole('textbox');
    const sendButton = screen.getByRole('button', { name: /send/i });
    
    input.focus();
    fireEvent.keyDown(input, { key: 'Tab' });
    expect(sendButton).toHaveFocus();
  });
  
  test('provides proper ARIA labels', () => {
    render(<AudioPlayer />);
    
    expect(screen.getByRole('button', { name: /play audio/i }))
      .toHaveAttribute('aria-label', 'Play audio response');
  });
});
```

---

## 11. Error Handling & Edge Cases

### Comprehensive Error Handling Strategy

**Network Connectivity Issues**
```javascript
// Network status monitoring
class NetworkMonitor {
  constructor() {
    this.isOnline = navigator.onLine;
    this.listeners = [];
    
    window.addEventListener('online', () => {
      this.isOnline = true;
      this.notifyListeners('online');
    });
    
    window.addEventListener('offline', () => {
      this.isOnline = false;
      this.notifyListeners('offline');
    });
  }
  
  onStatusChange(callback) {
    this.listeners.push(callback);
  }
  
  notifyListeners(status) {
    this.listeners.forEach(callback => callback(status));
  }
}

// Offline fallback component
function OfflineFallback({ children }) {
  const [isOnline, setIsOnline] = useState(navigator.onLine);
  
  useEffect(() => {
    const monitor = new NetworkMonitor();
    monitor.onStatusChange((status) => {
      setIsOnline(status === 'online');
    });
  }, []);
  
  if (!isOnline) {
    return (
      <div className="offline-message">
        <h2>आप ऑफलाइन हैं</h2>
        <p>कृपया अपना इंटरनेट कनेक्शन जांचें</p>
        <button onClick={() => window.location.reload()}>
          पुनः प्रयास करें
        </button>
      </div>
    );
  }
  
  return children;
}
```

**API Failure Handling**
```javascript
// Robust API client with retry logic
class RobustAPIClient {
  constructor(baseURL, maxRetries = 3) {
    this.baseURL = baseURL;
    this.maxRetries = maxRetries;
  }
  
  async request(endpoint, options = {}, retryCount = 0) {
    try {
      const response = await fetch(`${this.baseURL}${endpoint}`, {
        ...options,
        timeout: 10000
      });
      
      if (!response.ok) {
        throw new Error(`HTTP ${response.status}: ${response.statusText}`);
      }
      
      return await response.json();
    } catch (error) {
      if (retryCount < this.maxRetries) {
        // Exponential backoff
        const delay = Math.pow(2, retryCount) * 1000;
        await new Promise(resolve => setTimeout(resolve, delay));
        return this.request(endpoint, options, retryCount + 1);
      }
      
      throw new APIError(error.message, endpoint, retryCount);
    }
  }
}

// Custom error classes
class APIError extends Error {
  constructor(message, endpoint, retryCount) {
    super(message);
    this.name = 'APIError';
    this.endpoint = endpoint;
    this.retryCount = retryCount;
  }
}

class ValidationError extends Error {
  constructor(message, field) {
    super(message);
    this.name = 'ValidationError';
    this.field = field;
  }
}
```

**Unclear Query Handling**
```javascript
// Query clarification system
class QueryClarifier {
  static needsClarification(query, confidence) {
    return confidence < 0.7 || this.isAmbiguous(query);
  }
  
  static isAmbiguous(query) {
    const ambiguousPatterns = [
      /\b(scheme|योजना)\b/i, // Generic scheme mention
      /\b(help|मदद)\b/i, // Generic help request
      /\b(government|सरकार)\b/i // Generic government mention
    ];
    
    return ambiguousPatterns.some(pattern => pattern.test(query));
  }
  
  static generateClarificationQuestions(query, language = 'en') {
    const questions = {
      en: [
        "Which type of scheme are you interested in?",
        "Are you looking for schemes related to farming, education, or health?",
        "Can you provide more details about what you need help with?"
      ],
      hi: [
        "आप किस प्रकार की योजना में रुचि रखते हैं?",
        "क्या आप खेती, शिक्षा या स्वास्थ्य से संबंधित योजनाओं की तलाश कर रहे हैं?",
        "क्या आप इस बारे में और विवरण दे सकते हैं कि आपको किस चीज़ में मदद चाहिए?"
      ]
    };
    
    return questions[language] || questions.en;
  }
}

// Usage in chat component
function handleUserQuery(query) {
  const analysis = analyzeQuery(query);
  
  if (QueryClarifier.needsClarification(query, analysis.confidence)) {
    const questions = QueryClarifier.generateClarificationQuestions(
      query, 
      currentLanguage
    );
    
    return {
      type: 'clarification',
      questions: questions,
      originalQuery: query
    };
  }
  
  return processQuery(query, analysis);
}
```

**Audio Playback Issues**
```javascript
// Audio fallback system
class AudioManager {
  constructor() {
    this.audioContext = null;
    this.fallbackEnabled = true;
  }
  
  async playAudio(audioUrl, text, language) {
    try {
      // Try HTML5 Audio first
      const audio = new Audio(audioUrl);
      await this.playHTMLAudio(audio);
    } catch (error) {
      console.warn('HTML5 Audio failed, trying Web Speech API:', error);
      
      if (this.fallbackEnabled) {
        this.playWithWebSpeech(text, language);
      } else {
        throw new AudioError('Audio playback not supported', error);
      }
    }
  }
  
  async playHTMLAudio(audio) {
    return new Promise((resolve, reject) => {
      audio.addEventListener('ended', resolve);
      audio.addEventListener('error', reject);
      audio.play().catch(reject);
    });
  }
  
  playWithWebSpeech(text, language) {
    if ('speechSynthesis' in window) {
      const utterance = new SpeechSynthesisUtterance(text);
      utterance.lang = language;
      speechSynthesis.speak(utterance);
    } else {
      throw new AudioError('Speech synthesis not supported');
    }
  }
}
```

**Scheme Not Found Handling**
```javascript
// Intelligent scheme suggestion
class SchemeSuggester {
  static findSimilarSchemes(query, allSchemes, language = 'en') {
    const queryWords = this.extractKeywords(query, language);
    const suggestions = [];
    
    allSchemes.forEach(scheme => {
      const similarity = this.calculateSimilarity(
        queryWords, 
        scheme, 
        language
      );
      
      if (similarity > 0.3) {
        suggestions.push({ scheme, similarity });
      }
    });
    
    return suggestions
      .sort((a, b) => b.similarity - a.similarity)
      .slice(0, 3)
      .map(item => item.scheme);
  }
  
  static extractKeywords(query, language) {
    const stopWords = {
      en: ['the', 'is', 'at', 'which', 'on', 'a', 'an'],
      hi: ['का', 'के', 'की', 'में', 'से', 'को', 'है']
    };
    
    return query
      .toLowerCase()
      .split(/\s+/)
      .filter(word => !stopWords[language]?.includes(word))
      .filter(word => word.length > 2);
  }
  
  static calculateSimilarity(queryWords, scheme, language) {
    const schemeText = [
      scheme.name[language],
      scheme.description[language],
      scheme.category
    ].join(' ').toLowerCase();
    
    const matches = queryWords.filter(word => 
      schemeText.includes(word)
    );
    
    return matches.length / queryWords.length;
  }
}
```

---

## 12. Monitoring & Analytics

### Analytics Implementation

**Privacy-Compliant Analytics**
```javascript
// Anonymous analytics service
class AnalyticsService {
  constructor(endpoint) {
    this.endpoint = endpoint;
    this.sessionId = this.generateSessionId();
    this.events = [];
    this.batchSize = 10;
  }
  
  track(event, properties = {}) {
    const anonymizedEvent = {
      event,
      properties: this.anonymizeProperties(properties),
      timestamp: Date.now(),
      session_id: this.sessionId,
      user_agent: this.getBrowserInfo()
    };
    
    this.events.push(anonymizedEvent);
    
    if (this.events.length >= this.batchSize) {
      this.flush();
    }
  }
  
  anonymizeProperties(properties) {
    const anonymized = { ...properties };
    
    // Remove PII
    delete anonymized.user_id;
    delete anonymized.email;
    delete anonymized.phone;
    
    // Hash sensitive data
    if (anonymized.query) {
      anonymized.query_hash = this.hashString(anonymized.query);
      delete anonymized.query;
    }
    
    return anonymized;
  }
  
  async flush() {
    if (this.events.length === 0) return;
    
    try {
      await fetch(this.endpoint, {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ events: this.events })
      });
      
      this.events = [];
    } catch (error) {
      console.warn('Analytics flush failed:', error);
    }
  }
  
  generateSessionId() {
    return 'session_' + Math.random().toString(36).substr(2, 9);
  }
  
  hashString(str) {
    let hash = 0;
    for (let i = 0; i < str.length; i++) {
      const char = str.charCodeAt(i);
      hash = ((hash << 5) - hash) + char;
      hash = hash & hash; // Convert to 32-bit integer
    }
    return hash.toString(36);
  }
}

// Usage throughout the app
const analytics = new AnalyticsService('/api/analytics');

// Track user interactions
analytics.track('query_submitted', {
  language: 'hi',
  query_length: query.length,
  has_voice_input: false
});

analytics.track('audio_played', {
  language: 'hi',
  audio_duration: 45,
  playback_speed: 1.0
});

analytics.track('scheme_viewed', {
  scheme_id: 'pm-kisan',
  language: 'hi',
  source: 'search_result'
});
```

**Performance Monitoring**
```javascript
// Performance metrics collection
class PerformanceMonitor {
  constructor() {
    this.metrics = new Map();
    this.observers = [];
    this.setupObservers();
  }
  
  setupObservers() {
    // Page load performance
    if ('PerformanceObserver' in window) {
      const observer = new PerformanceObserver((list) => {
        list.getEntries().forEach((entry) => {
          this.recordMetric(entry.name, entry.duration);
        });
      });
      
      observer.observe({ entryTypes: ['navigation', 'resource'] });
      this.observers.push(observer);
    }
    
    // API response times
    this.interceptFetch();
  }
  
  interceptFetch() {
    const originalFetch = window.fetch;
    
    window.fetch = async (...args) => {
      const startTime = performance.now();
      
      try {
        const response = await originalFetch(...args);
        const endTime = performance.now();
        
        this.recordMetric(`api_${args[0]}`, endTime - startTime);
        
        return response;
      } catch (error) {
        const endTime = performance.now();
        this.recordMetric(`api_error_${args[0]}`, endTime - startTime);
        throw error;
      }
    };
  }
  
  recordMetric(name, value) {
    if (!this.metrics.has(name)) {
      this.metrics.set(name, []);
    }
    
    this.metrics.get(name).push({
      value,
      timestamp: Date.now()
    });
    
    // Keep only last 100 measurements
    const measurements = this.metrics.get(name);
    if (measurements.length > 100) {
      measurements.shift();
    }
  }
  
  getMetrics() {
    const summary = {};
    
    this.metrics.forEach((measurements, name) => {
      const values = measurements.map(m => m.value);
      summary[name] = {
        count: values.length,
        avg: values.reduce((a, b) => a + b, 0) / values.length,
        min: Math.min(...values),
        max: Math.max(...values),
        p95: this.percentile(values, 95)
      };
    });
    
    return summary;
  }
  
  percentile(values, p) {
    const sorted = values.sort((a, b) => a - b);
    const index = Math.ceil((p / 100) * sorted.length) - 1;
    return sorted[index];
  }
}
```

**Error Tracking**
```javascript
// Error monitoring service
class ErrorTracker {
  constructor(endpoint) {
    this.endpoint = endpoint;
    this.setupGlobalHandlers();
  }
  
  setupGlobalHandlers() {
    // JavaScript errors
    window.addEventListener('error', (event) => {
      this.trackError({
        type: 'javascript_error',
        message: event.message,
        filename: event.filename,
        lineno: event.lineno,
        colno: event.colno,
        stack: event.error?.stack
      });
    });
    
    // Promise rejections
    window.addEventListener('unhandledrejection', (event) => {
      this.trackError({
        type: 'unhandled_promise_rejection',
        message: event.reason?.message || 'Unhandled promise rejection',
        stack: event.reason?.stack
      });
    });
    
    // React error boundaries
    this.setupReactErrorBoundary();
  }
  
  trackError(error) {
    const errorReport = {
      ...error,
      timestamp: Date.now(),
      url: window.location.href,
      user_agent: navigator.userAgent,
      session_id: this.getSessionId()
    };
    
    // Send to error tracking service
    fetch(this.endpoint, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(errorReport)
    }).catch(console.error);
  }
  
  setupReactErrorBoundary() {
    // This would be implemented in React error boundary component
    window.reportReactError = (error, errorInfo) => {
      this.trackError({
        type: 'react_error',
        message: error.message,
        stack: error.stack,
        component_stack: errorInfo.componentStack
      });
    };
  }
}
```

---

## Conclusion

This comprehensive technical design document provides a complete blueprint for building the Jan-Sevak AI Bot. The architecture emphasizes:

- **Scalability**: Modular design that can grow from MVP to national scale
- **Accessibility**: Mobile-first, audio-centric design for low-literacy users
- **Performance**: Optimized for low-bandwidth and low-end devices
- **Security**: Privacy-first approach with no PII storage
- **Reliability**: Comprehensive error handling and fallback mechanisms
- **Maintainability**: Clean code architecture with extensive testing

The design balances technical sophistication with practical constraints, ensuring the solution can be built within hackathon timelines while providing a foundation for long-term growth and impact.

**Next Steps:**
1. Set up development environment with specified tech stack
2. Implement core components following the component design
3. Integrate Claude API and TTS services
4. Build and test MVP features
5. Deploy to Vercel for hackathon demonstration
6. Gather user feedback and iterate based on testing results

---

**Document Version:** 1.0  
**Last Updated:** February 2026  
**Review Date:** March 2026