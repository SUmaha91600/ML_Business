# Star Learning Platform - Architecture Diagrams

> **How to view these diagrams:**
> - Copy to [mermaid.live](https://mermaid.live) for instant rendering
> - View directly on GitHub (renders automatically)
> - Use VS Code with Mermaid extension
> - Export as PNG/SVG from mermaid.live

---

## 1. System Context (C4 Level 1) - The Big Picture

**What is this app?** A learning platform where students upload their course materials and get AI-generated quizzes, study sheets, and personalized learning paths.

```mermaid
C4Context
    title Star Learning Platform - System Context

    Person(student, "Student", "Wants to learn and practice with quizzes")
    Person(researcher, "Researcher", "Needs to find and organize research papers")
    Person(admin, "Admin", "Manages the platform")

    System(starlearning, "Star Learning Platform", "AI-powered educational platform for quiz generation, spaced repetition, and research paper management")

    System_Ext(openrouter, "OpenRouter API", "AI models: Claude, Gemini")
    System_Ext(arxiv, "arXiv API", "Research papers database")
    System_Ext(serpapi, "SerpAPI", "Web search for jobs and academic content")
    System_Ext(telegram, "Telegram", "Notifications")
    System_Ext(email, "Email Server", "Gmail SMTP")

    Rel(student, starlearning, "Uses", "HTTPS")
    Rel(researcher, starlearning, "Uses", "HTTPS")
    Rel(admin, starlearning, "Manages", "HTTPS")

    Rel(starlearning, openrouter, "Generates quizzes & summaries", "API")
    Rel(starlearning, arxiv, "Searches papers", "API")
    Rel(starlearning, serpapi, "Searches web", "API")
    Rel(starlearning, telegram, "Sends notifications", "Bot API")
    Rel(starlearning, email, "Sends emails", "SMTP")
```

---

## 2. Container Diagram (C4 Level 2) - Main Components

**What's inside the system?** The main parts that work together.

```mermaid
C4Container
    title Star Learning Platform - Container Diagram

    Person(user, "User", "Student or Researcher")

    Container_Boundary(app, "Star Learning Platform") {
        Container(webapp, "Web Application", "HTML/CSS/JS", "User interface for all features")
        Container(api, "Flask API", "Python/Flask", "Handles all business logic and API endpoints")
        Container(db, "Database", "SQLite/PostgreSQL", "Stores users, quizzes, progress, papers")
        Container(filestore, "File Storage", "Local filesystem", "Uploaded PDFs, documents, papers")
        Container(vectordb, "Vector Database", "FAISS", "Embeddings for RAG search")
    }

    System_Ext(ai, "AI Services", "OpenRouter API")
    System_Ext(external, "External APIs", "arXiv, SerpAPI, Telegram")

    Rel(user, webapp, "Interacts with", "Browser")
    Rel(webapp, api, "API calls", "JSON/REST")
    Rel(api, db, "Reads/Writes", "SQLAlchemy")
    Rel(api, filestore, "Stores/Reads files")
    Rel(api, vectordb, "Semantic search")
    Rel(api, ai, "AI generation", "HTTPS")
    Rel(api, external, "External data", "HTTPS")
```

---

## 3. How a Student Uses the Platform - Simple Flow

```mermaid
flowchart TD
    subgraph "👤 Student Journey"
        A[🎓 Student visits website] --> B{Already registered?}
        B -->|No| C[📝 Register account]
        B -->|Yes| D[🔐 Login]
        C --> D

        D --> E[🏠 Dashboard]

        E --> F[📚 Upload lecture materials]
        E --> G[📖 Create study topics]
        E --> H[📊 View progress]
        E --> I[🔄 Practice with spaced repetition]

        F --> J[🤖 AI generates quiz questions]
        G --> K[📄 Generate study sheet PDF]

        J --> L[✍️ Take quiz]
        L --> M{Score check}
        M -->|Good| N[✅ Progress saved]
        M -->|Needs work| O[📝 Create flashcards for review]

        O --> I
        K --> P[📧 Receive PDF by email]
    end

    style A fill:#e1f5fe
    style J fill:#fff3e0
    style L fill:#f3e5f5
    style N fill:#e8f5e9
```

---

## 4. Quiz Generation Flow - Step by Step

**How does the quiz get created?** Follow the blue boxes!

```mermaid
flowchart LR
    subgraph "Step 1: Upload"
        A[📄 User uploads<br/>PDF/DOCX/TXT] --> B[📂 File saved to<br/>server storage]
    end

    subgraph "Step 2: Extract Text"
        B --> C{File type?}
        C -->|PDF| D[pdfplumber<br/>extracts text]
        C -->|DOCX| E[python-docx<br/>extracts text]
        C -->|TXT| F[Direct read]
        D --> G[📝 Raw text<br/>max 48,000 chars]
        E --> G
        F --> G
    end

    subgraph "Step 3: AI Generation"
        G --> H[🤖 Send to<br/>OpenRouter API]
        H --> I{Which AI model?}
        I -->|Try 1| J[Claude 3.5 Sonnet<br/>Best quality]
        I -->|Try 2| K[Gemini Flash<br/>Free option]
        I -->|Try 3| L[Claude Haiku<br/>Fast backup]
        J --> M[📋 JSON with<br/>10 questions]
        K --> M
        L --> M
    end

    subgraph "Step 4: Quiz Ready"
        M --> N[🎯 Display quiz<br/>to student]
        N --> O[✍️ Student<br/>answers]
        O --> P[📊 Score<br/>calculated]
        P --> Q[💾 Save to<br/>database]
    end

    style A fill:#bbdefb
    style H fill:#fff9c4
    style M fill:#c8e6c9
    style Q fill:#f8bbd9
```

---

## 5. Study Sheet (Fiche) Generation Flow

**How does the PDF study sheet get created?**

```mermaid
flowchart TD
    subgraph "Input"
        A[📚 Select topic] --> B{Has uploaded files?}
        B -->|Yes| C[📄 Read all topic files]
        B -->|No| D[🔍 Search Google Scholar<br/>& arXiv for content]
        C --> E[📝 Combined text]
        D --> E
    end

    subgraph "AI Processing"
        E --> F[🤖 Claude 3.5 Sonnet]
        F --> G[📄 Generate HTML with:<br/>• Definitions<br/>• Equations in LaTeX<br/>• Derivations<br/>• Examples<br/>• References]
    end

    subgraph "PDF Creation"
        G --> H{pdflatex available?}
        H -->|Yes| I[🔧 Convert HTML to LaTeX]
        I --> J[📐 Compile with pdflatex<br/>Beautiful equations!]
        H -->|No| K[📄 Use WeasyPrint<br/>Basic formatting]
        J --> L[📑 PDF file ready]
        K --> L
    end

    subgraph "Delivery"
        L --> M[📧 Send via email<br/>Flask-Mail + Gmail SMTP]
        M --> N[✅ Student receives<br/>professional PDF]
    end

    style A fill:#e3f2fd
    style F fill:#fff8e1
    style J fill:#fce4ec
    style N fill:#e8f5e9
```

---

## 6. Spaced Repetition (FSRS) System

**How does the app remember what you need to review?**

```mermaid
flowchart TD
    subgraph "Card Creation"
        A[❌ Wrong answer<br/>on quiz] --> B[📝 Create flashcard<br/>Question → Answer]
        B --> C[💾 Save as FSRSCard<br/>state: 'new']
    end

    subgraph "Daily Review"
        D[📅 Student opens<br/>review section] --> E[🔍 Find cards<br/>where due ≤ today]
        E --> F[🃏 Show card<br/>front side]
        F --> G[🤔 Student thinks<br/>of answer]
        G --> H[👁️ Reveal<br/>back side]
    end

    subgraph "Rating"
        H --> I{How well did<br/>you remember?}
        I -->|Forgot| J[1️⃣ Again<br/>Review in 1 min]
        I -->|Struggled| K[2️⃣ Hard<br/>Review tomorrow]
        I -->|Remembered| L[3️⃣ Good<br/>Review in X days]
        I -->|Easy| M[4️⃣ Easy<br/>Review in X+ days]
    end

    subgraph "FSRS Algorithm"
        J --> N[🧮 Calculate new:<br/>• Stability<br/>• Difficulty<br/>• Next due date]
        K --> N
        L --> N
        M --> N
        N --> O[💾 Update card<br/>in database]
        O --> P[📈 Memory gets<br/>stronger over time!]
    end

    style A fill:#ffcdd2
    style B fill:#fff9c4
    style N fill:#e1bee7
    style P fill:#c8e6c9
```

---

## 7. Concept Dependency Graph & Learning Path

**How does the app know what to learn first?**

```mermaid
flowchart TD
    subgraph "Knowledge Graph"
        A[🎯 Target: Neural Networks] --> B[Prerequisites?]
        B --> C[Linear Algebra]
        B --> D[Calculus]
        B --> E[Python Programming]
        C --> F[Vectors]
        C --> G[Matrices]
        D --> H[Derivatives]
        D --> I[Chain Rule]
    end

    subgraph "Algorithm: Topological Sort"
        J[🔍 Find all prerequisites] --> K[📊 Order from basic → advanced]
        K --> L[Check user's mastery<br/>of each concept]
        L --> M{Mastery ≥ 80%?}
        M -->|Yes| N[✅ Skip this concept]
        M -->|No| O[📝 Add to learning path]
    end

    subgraph "Personalized Path"
        O --> P[📚 Your Learning Path:<br/>1. Vectors<br/>2. Matrices<br/>3. Derivatives<br/>4. Chain Rule<br/>5. Neural Networks]
        P --> Q[🎮 Start learning!]
    end

    style A fill:#e3f2fd
    style K fill:#fff8e1
    style P fill:#e8f5e9
```

---

## 8. Research Paper Library (For Researchers)

```mermaid
flowchart LR
    subgraph "Import Papers"
        A[📥 Upload PDF] --> B[Extract metadata:<br/>• Title<br/>• Authors<br/>• Abstract]
        C[🔍 Search arXiv] --> D[Download PDFs<br/>automatically]
        B --> E[💾 Save to Paper table]
        D --> E
    end

    subgraph "Organize"
        E --> F[🏷️ Add tags]
        E --> G[📁 Assign to project]
        E --> H[⭐ Set priority]
    end

    subgraph "AI Features"
        E --> I[🤖 Generate AI summary]
        E --> J[📊 Extract key findings]
        E --> K[🔍 RAG: Ask questions<br/>about your papers]
    end

    subgraph "RAG System"
        K --> L[📄 Chunk text]
        L --> M[🔢 Create embeddings]
        M --> N[💾 Store in FAISS]
        N --> O[🔍 Semantic search]
        O --> P[💬 AI answers with<br/>paper citations]
    end

    style A fill:#e3f2fd
    style I fill:#fff8e1
    style P fill:#e8f5e9
```

---

## 9. Database Structure - Simplified

```mermaid
erDiagram
    User ||--o{ Topic : creates
    User ||--o{ QuestionHistory : has
    User ||--o{ FSRSCard : owns
    User ||--o{ Paper : uploads
    User ||--o{ ConceptPerformance : tracks

    Topic ||--o{ TopicFile : contains

    FSRSCard ||--o{ FSRSReview : has

    Concept ||--o{ ConceptDependency : requires
    Concept ||--o{ UserConceptProgress : tracked_by

    Paper ||--o{ PaperTag : tagged_with
    Paper ||--o{ PaperAnnotation : annotated_with
    PaperProject ||--o{ Paper : contains

    User {
        int id PK
        string name
        string email
        string password
        string role
    }

    Topic {
        int id PK
        int user_id FK
        string name
    }

    FSRSCard {
        int id PK
        int user_id FK
        string front
        string back
        float stability
        float difficulty
        datetime due
    }

    Paper {
        int id PK
        int user_id FK
        string title
        string authors
        text abstract
        text ai_summary
    }

    Concept {
        int id PK
        string name
        int difficulty_level
        float estimated_hours
    }
```

---

## 10. API Endpoints Overview

```mermaid
flowchart TD
    subgraph "Authentication"
        A1[POST /register]
        A2[POST /login]
        A3[POST /logout]
        A4[POST /verify-2fa]
    end

    subgraph "Quiz System"
        B1[POST /api/generate<br/>Upload files → Quiz]
        B2[POST /api/generate-from-topic<br/>Topic name → Quiz]
        B3[POST /api/get-feedback<br/>Wrong answers → Explanations]
    end

    subgraph "Topics & Fiche"
        C1[GET/POST/DELETE /api/topics]
        C2[POST /api/topics/merge]
        C3[POST /api/fiche/send-email]
    end

    subgraph "Spaced Repetition"
        D1[POST /api/fsrs/create-card]
        D2[GET /api/fsrs/due-cards]
        D3[POST /api/fsrs/review]
        D4[GET /api/fsrs/stats]
    end

    subgraph "Concept Graph"
        E1[GET /api/concepts/graph]
        E2[GET /api/concepts/learning-path/:id]
        E3[GET /api/concepts/recommended]
        E4[POST /api/concepts/generate-exam]
    end

    subgraph "Research Papers"
        F1[POST /api/papers/upload]
        F2[GET /api/papers]
        F3[POST /api/papers/chat<br/>RAG Q&A]
        F4[POST /api/arxiv-search]
    end

    style B1 fill:#e3f2fd
    style C3 fill:#fff8e1
    style D3 fill:#fce4ec
    style F3 fill:#e8f5e9
```

---

## 11. Technology Stack

```mermaid
mindmap
  root((Star Learning<br/>Platform))
    Frontend
      HTML5/CSS3
      JavaScript
      Galaxy theme
    Backend
      Flask
      SQLAlchemy
      Flask-Login
      Flask-Mail
    Database
      SQLite dev
      PostgreSQL prod
      FAISS vectors
    AI Services
      OpenRouter API
        Claude 3.5 Sonnet
        Gemini Flash
        Claude Haiku
    External APIs
      arXiv
      SerpAPI
      Telegram
    File Processing
      pdfplumber
      python-docx
      pandas
    PDF Generation
      pdflatex
      WeasyPrint
```

---

## 12. Complete User Flow - The Full Picture

```mermaid
sequenceDiagram
    actor Student
    participant Web as Web Browser
    participant API as Flask API
    participant DB as Database
    participant AI as OpenRouter AI
    participant Email as Email Server

    Note over Student,Email: 📚 Learning Session Flow

    Student->>Web: 1. Login
    Web->>API: POST /login
    API->>DB: Verify credentials
    DB-->>API: User data
    API-->>Web: Session token

    Student->>Web: 2. Upload lecture PDF
    Web->>API: POST /api/generate
    API->>API: Extract text from PDF
    API->>AI: Generate 10 quiz questions
    AI-->>API: JSON with questions
    API-->>Web: Quiz ready

    Student->>Web: 3. Take quiz
    Web->>API: Submit answers
    API->>DB: Save to QuestionHistory
    API->>AI: Generate feedback for wrong answers
    AI-->>API: Explanations
    API-->>Web: Score + feedback

    Student->>Web: 4. Create flashcards from mistakes
    Web->>API: POST /api/fsrs/bulk-create
    API->>DB: Create FSRSCard entries

    Student->>Web: 5. Request study sheet
    Web->>API: POST /api/fiche/send-email
    API->>AI: Generate detailed summary
    AI-->>API: HTML with LaTeX equations
    API->>API: Convert to PDF
    API->>Email: Send PDF attachment
    Email-->>Student: 📧 PDF in inbox!

    Note over Student,Email: 📅 Next Day - Review Session

    Student->>Web: 6. Open spaced repetition
    Web->>API: GET /api/fsrs/due-cards
    API->>DB: Find cards where due ≤ now
    DB-->>API: Due cards
    API-->>Web: Cards to review

    Student->>Web: 7. Rate each card
    Web->>API: POST /api/fsrs/review
    API->>API: Run FSRS algorithm
    API->>DB: Update card stability & due date
    API-->>Web: Next card

    Note over Student,Email: ✅ Knowledge retained!
```

---

## How to Use These Diagrams

1. **View Online**: Go to [mermaid.live](https://mermaid.live), paste any diagram code
2. **Export**: Download as PNG or SVG for presentations
3. **GitHub**: This file renders automatically on GitHub
4. **Presentations**: Export and add to PowerPoint/Google Slides

---

*Created for Star Learning Platform - M1 ML Business Project*
