# Star Learning Platform - Architecture Diagrams

> **How to view these diagrams:**
> - Copy to [mermaid.live](https://mermaid.live) for instant rendering
> - View directly on GitHub (renders automatically)
> - Use VS Code with Mermaid extension
> - Export as PNG/SVG from mermaid.live

> **Color Palette (Star Learning Theme):**
> - Primary Accent: #64ffda (Cyan/Green)
> - Dark Background: #0a192f
> - Card Background: #112240
> - Border: #172a45
> - Text: #ccd6f6

---

## 1. System Context (C4 Level 1) - The Big Picture

**What is this app?** A learning platform where students upload their course materials and get AI-generated quizzes, study sheets, and personalized learning paths.

```mermaid
%%{init: {'theme': 'base', 'themeVariables': { 'primaryColor': '#112240', 'primaryTextColor': '#ccd6f6', 'primaryBorderColor': '#64ffda', 'lineColor': '#64ffda', 'secondaryColor': '#172a45', 'tertiaryColor': '#0a192f', 'background': '#0a192f', 'mainBkg': '#112240', 'secondBkg': '#172a45', 'border1': '#64ffda', 'border2': '#64ffda', 'fontFamily': 'arial'}}}%%
flowchart TD
    subgraph Users
        student[Student]
        researcher[Researcher]
        admin[Admin]
    end

    subgraph "Star Learning Platform"
        app[AI-powered educational platform<br/>Quiz generation, spaced repetition,<br/>research paper management]
    end

    subgraph "External Services"
        openrouter[OpenRouter API<br/>Claude, Gemini]
        arxiv[arXiv API<br/>Research papers]
        serpapi[SerpAPI<br/>Web search]
        telegram[Telegram<br/>Notifications]
        email[Email Server<br/>Gmail SMTP]
    end

    student --> app
    researcher --> app
    admin --> app

    app --> openrouter
    app --> arxiv
    app --> serpapi
    app --> telegram
    app --> email

    style app fill:#112240,stroke:#64ffda,color:#ccd6f6
    style student fill:#172a45,stroke:#64ffda,color:#ccd6f6
    style researcher fill:#172a45,stroke:#64ffda,color:#ccd6f6
    style admin fill:#172a45,stroke:#64ffda,color:#ccd6f6
    style openrouter fill:#112240,stroke:#64ffda,color:#ccd6f6
    style arxiv fill:#112240,stroke:#64ffda,color:#ccd6f6
    style serpapi fill:#112240,stroke:#64ffda,color:#ccd6f6
    style telegram fill:#112240,stroke:#64ffda,color:#ccd6f6
    style email fill:#112240,stroke:#64ffda,color:#ccd6f6
```

---

## 2. Container Diagram (C4 Level 2) - Main Components

**What's inside the system?** The main parts that work together.

```mermaid
%%{init: {'theme': 'base', 'themeVariables': { 'primaryColor': '#112240', 'primaryTextColor': '#ccd6f6', 'primaryBorderColor': '#64ffda', 'lineColor': '#64ffda', 'secondaryColor': '#172a45', 'tertiaryColor': '#0a192f'}}}%%
flowchart TD
    user[User<br/>Student or Researcher] --> webapp

    subgraph "Star Learning Platform"
        webapp[Web Application<br/>HTML/CSS/JS] --> api
        api[Flask API<br/>Python/Flask] --> db
        api --> filestore
        api --> vectordb
        db[Database<br/>SQLite/PostgreSQL]
        filestore[File Storage<br/>Local filesystem]
        vectordb[Vector Database<br/>FAISS]
    end

    api --> external

    subgraph "External"
        external[External APIs<br/>OpenRouter, arXiv,<br/>SerpAPI, Telegram]
    end

    style user fill:#172a45,stroke:#64ffda,color:#ccd6f6
    style webapp fill:#112240,stroke:#64ffda,color:#ccd6f6
    style api fill:#112240,stroke:#64ffda,color:#64ffda
    style db fill:#112240,stroke:#64ffda,color:#ccd6f6
    style filestore fill:#112240,stroke:#64ffda,color:#ccd6f6
    style vectordb fill:#112240,stroke:#64ffda,color:#ccd6f6
    style external fill:#172a45,stroke:#64ffda,color:#ccd6f6
```

---

## 3. How a Student Uses the Platform - Simple Flow

```mermaid
%%{init: {'theme': 'base', 'themeVariables': { 'primaryColor': '#112240', 'primaryTextColor': '#ccd6f6', 'primaryBorderColor': '#64ffda', 'lineColor': '#64ffda', 'secondaryColor': '#172a45', 'tertiaryColor': '#0a192f'}}}%%
flowchart TD
    A[Student visits website] --> B{Already registered?}
    B -->|No| C[Register account]
    B -->|Yes| D[Login]
    C --> D
    D --> E[Dashboard]

    E --> F[Upload lecture materials]
    F --> J[AI generates quiz questions]
    J --> L[Take quiz]
    L --> M{Score check}
    M -->|Good| N[Progress saved]
    M -->|Needs work| O[Create flashcards]
    O --> I[Practice with spaced repetition]

    E --> G[Create study topics]
    G --> K[Generate study sheet PDF]
    K --> P[Receive PDF by email]

    E --> H[View progress]
    E --> I

    style A fill:#112240,stroke:#64ffda,color:#ccd6f6
    style D fill:#112240,stroke:#64ffda,color:#ccd6f6
    style E fill:#172a45,stroke:#64ffda,color:#64ffda
    style J fill:#112240,stroke:#64ffda,color:#ccd6f6
    style L fill:#112240,stroke:#64ffda,color:#ccd6f6
    style N fill:#172a45,stroke:#64ffda,color:#64ffda
    style P fill:#172a45,stroke:#64ffda,color:#64ffda
    style I fill:#112240,stroke:#64ffda,color:#ccd6f6
```

---

## 4. Quiz Generation Flow - Step by Step

**How does the quiz get created?** Follow the flow from top to bottom.

```mermaid
%%{init: {'theme': 'base', 'themeVariables': { 'primaryColor': '#112240', 'primaryTextColor': '#ccd6f6', 'primaryBorderColor': '#64ffda', 'lineColor': '#64ffda', 'secondaryColor': '#172a45', 'tertiaryColor': '#0a192f'}}}%%
flowchart TD
    subgraph "Step 1: Upload"
        A[User uploads PDF/DOCX/TXT] --> B[File saved to server]
    end

    subgraph "Step 2: Extract Text"
        B --> C{File type?}
        C -->|PDF| D[pdfplumber extracts text]
        C -->|DOCX| E[python-docx extracts text]
        C -->|TXT| F[Direct read]
        D --> G[Raw text - max 48,000 chars]
        E --> G
        F --> G
    end

    subgraph "Step 3: AI Generation"
        G --> H[Send to OpenRouter API]
        H --> I[Try Claude 3.5 Sonnet]
        I -->|Success| M[JSON with 10 questions]
        I -->|Fail| J[Try Gemini Flash]
        J -->|Success| M
        J -->|Fail| K[Try Claude Haiku]
        K --> M
    end

    subgraph "Step 4: Quiz Ready"
        M --> N[Display quiz to student]
        N --> O[Student answers]
        O --> P[Score calculated]
        P --> Q[Save to database]
    end

    style A fill:#112240,stroke:#64ffda,color:#ccd6f6
    style H fill:#172a45,stroke:#64ffda,color:#64ffda
    style M fill:#112240,stroke:#64ffda,color:#64ffda
    style Q fill:#172a45,stroke:#64ffda,color:#64ffda
```

---

## 5. Study Sheet (Fiche) Generation Flow

**How does the PDF study sheet get created?**

```mermaid
%%{init: {'theme': 'base', 'themeVariables': { 'primaryColor': '#112240', 'primaryTextColor': '#ccd6f6', 'primaryBorderColor': '#64ffda', 'lineColor': '#64ffda', 'secondaryColor': '#172a45', 'tertiaryColor': '#0a192f'}}}%%
flowchart TD
    subgraph "Step 1: Input"
        A[Select topic] --> B{Has uploaded files?}
        B -->|Yes| C[Read all topic files]
        B -->|No| D[Search Google Scholar and arXiv]
        C --> E[Combined text]
        D --> E
    end

    subgraph "Step 2: AI Processing"
        E --> F[Claude 3.5 Sonnet generates HTML]
        F --> G[Content includes:<br/>Definitions, Equations,<br/>Derivations, Examples, References]
    end

    subgraph "Step 3: PDF Creation"
        G --> H{pdflatex available?}
        H -->|Yes| I[Convert HTML to LaTeX]
        I --> J[Compile with pdflatex]
        H -->|No| K[Use WeasyPrint]
        J --> L[PDF file ready]
        K --> L
    end

    subgraph "Step 4: Delivery"
        L --> M[Send via Flask-Mail]
        M --> N[Student receives PDF]
    end

    style A fill:#112240,stroke:#64ffda,color:#ccd6f6
    style F fill:#172a45,stroke:#64ffda,color:#64ffda
    style J fill:#112240,stroke:#64ffda,color:#ccd6f6
    style N fill:#172a45,stroke:#64ffda,color:#64ffda
```

---

## 6. Spaced Repetition (FSRS) System

**How does the app remember what you need to review?**

```mermaid
%%{init: {'theme': 'base', 'themeVariables': { 'primaryColor': '#112240', 'primaryTextColor': '#ccd6f6', 'primaryBorderColor': '#64ffda', 'lineColor': '#64ffda', 'secondaryColor': '#172a45', 'tertiaryColor': '#0a192f'}}}%%
flowchart TD
    subgraph "Step 1: Card Creation"
        A[Wrong answer on quiz] --> B[Create flashcard]
        B --> C[Save as FSRSCard - state: new]
    end

    subgraph "Step 2: Daily Review"
        D[Student opens review] --> E[Find due cards]
        E --> F[Show card front]
        F --> G[Student thinks]
        G --> H[Reveal back]
    end

    subgraph "Step 3: Rating"
        H --> I{How well remembered?}
        I -->|Forgot| J[Again - 1 min]
        I -->|Struggled| K[Hard - tomorrow]
        I -->|Remembered| L[Good - X days]
        I -->|Easy| M[Easy - X+ days]
    end

    subgraph "Step 4: FSRS Algorithm"
        J --> N[Calculate: Stability, Difficulty, Next due]
        K --> N
        L --> N
        M --> N
        N --> O[Update card in database]
        O --> P[Memory strengthens over time]
    end

    style A fill:#112240,stroke:#64ffda,color:#ccd6f6
    style B fill:#172a45,stroke:#64ffda,color:#ccd6f6
    style N fill:#172a45,stroke:#64ffda,color:#64ffda
    style P fill:#112240,stroke:#64ffda,color:#64ffda
```

---

## 7. Concept Dependency Graph & Learning Path

**How does the app know what to learn first?**

```mermaid
%%{init: {'theme': 'base', 'themeVariables': { 'primaryColor': '#112240', 'primaryTextColor': '#ccd6f6', 'primaryBorderColor': '#64ffda', 'lineColor': '#64ffda', 'secondaryColor': '#172a45', 'tertiaryColor': '#0a192f'}}}%%
flowchart TD
    subgraph "Step 1: Knowledge Graph"
        A[Target: Neural Networks]
        A --> B[Linear Algebra]
        A --> C[Calculus]
        A --> D[Python]
        B --> E[Vectors]
        B --> F[Matrices]
        C --> G[Derivatives]
        C --> H[Chain Rule]
    end

    subgraph "Step 2: Topological Sort"
        I[Find all prerequisites] --> J[Order basic to advanced]
        J --> K[Check user mastery]
        K --> L{Mastery >= 80%?}
        L -->|Yes| M[Skip concept]
        L -->|No| N[Add to path]
    end

    subgraph "Step 3: Learning Path"
        N --> O[Your Path:<br/>1. Vectors<br/>2. Matrices<br/>3. Derivatives<br/>4. Chain Rule<br/>5. Neural Networks]
        O --> P[Start learning]
    end

    style A fill:#172a45,stroke:#64ffda,color:#64ffda
    style J fill:#112240,stroke:#64ffda,color:#ccd6f6
    style O fill:#172a45,stroke:#64ffda,color:#64ffda
    style P fill:#112240,stroke:#64ffda,color:#64ffda
```

---

## 8. Research Paper Library (For Researchers)

```mermaid
%%{init: {'theme': 'base', 'themeVariables': { 'primaryColor': '#112240', 'primaryTextColor': '#ccd6f6', 'primaryBorderColor': '#64ffda', 'lineColor': '#64ffda', 'secondaryColor': '#172a45', 'tertiaryColor': '#0a192f'}}}%%
flowchart TD
    subgraph "Step 1: Import"
        A[Upload PDF] --> B[Extract metadata]
        C[Search arXiv] --> D[Download PDFs]
        B --> E[Save to database]
        D --> E
    end

    subgraph "Step 2: Organize"
        E --> F[Add tags]
        E --> G[Assign to project]
        E --> H[Set priority]
    end

    subgraph "Step 3: AI Features"
        E --> I[Generate AI summary]
        E --> J[Extract key findings]
        E --> K[RAG: Ask questions]
    end

    subgraph "Step 4: RAG System"
        K --> L[Chunk text]
        L --> M[Create embeddings]
        M --> N[Store in FAISS]
        N --> O[Semantic search]
        O --> P[AI answers with citations]
    end

    style A fill:#112240,stroke:#64ffda,color:#ccd6f6
    style I fill:#172a45,stroke:#64ffda,color:#64ffda
    style P fill:#172a45,stroke:#64ffda,color:#64ffda
```

---

## 9. Database Structure - Simplified

```mermaid
%%{init: {'theme': 'base', 'themeVariables': { 'primaryColor': '#112240', 'primaryTextColor': '#ccd6f6', 'primaryBorderColor': '#64ffda', 'lineColor': '#64ffda', 'secondaryColor': '#172a45', 'tertiaryColor': '#0a192f', 'entityBorder': '#64ffda', 'attributeBackgroundColorOdd': '#112240', 'attributeBackgroundColorEven': '#172a45'}}}%%
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
%%{init: {'theme': 'base', 'themeVariables': { 'primaryColor': '#112240', 'primaryTextColor': '#ccd6f6', 'primaryBorderColor': '#64ffda', 'lineColor': '#64ffda', 'secondaryColor': '#172a45', 'tertiaryColor': '#0a192f'}}}%%
flowchart LR
    subgraph "Authentication"
        A1[POST /register]
        A2[POST /login]
        A3[POST /logout]
    end

    subgraph "Quiz System"
        B1[POST /api/generate]
        B2[POST /api/generate-from-topic]
        B3[POST /api/get-feedback]
    end

    subgraph "Topics"
        C1[GET/POST/DELETE /api/topics]
        C2[POST /api/fiche/send-email]
    end

    subgraph "Spaced Repetition"
        D1[POST /api/fsrs/create-card]
        D2[GET /api/fsrs/due-cards]
        D3[POST /api/fsrs/review]
    end

    subgraph "Concepts"
        E1[GET /api/concepts/graph]
        E2[GET /api/concepts/learning-path]
        E3[POST /api/concepts/generate-exam]
    end

    subgraph "Papers"
        F1[POST /api/papers/upload]
        F2[GET /api/papers]
        F3[POST /api/papers/chat]
    end

    style B1 fill:#112240,stroke:#64ffda,color:#ccd6f6
    style C2 fill:#172a45,stroke:#64ffda,color:#64ffda
    style D3 fill:#112240,stroke:#64ffda,color:#ccd6f6
    style F3 fill:#172a45,stroke:#64ffda,color:#64ffda
```

---

## 11. Technology Stack

```mermaid
%%{init: {'theme': 'base', 'themeVariables': { 'primaryColor': '#112240', 'primaryTextColor': '#ccd6f6', 'primaryBorderColor': '#64ffda', 'lineColor': '#64ffda', 'secondaryColor': '#172a45', 'tertiaryColor': '#0a192f'}}}%%
flowchart TD
    APP[Star Learning Platform]

    APP --> FE[Frontend]
    APP --> BE[Backend]
    APP --> DB[Database]
    APP --> AI[AI Services]
    APP --> EXT[External APIs]
    APP --> FILE[File Processing]
    APP --> PDF[PDF Generation]

    FE --> FE1[HTML5/CSS3]
    FE --> FE2[JavaScript]
    FE --> FE3[Galaxy theme]

    BE --> BE1[Flask]
    BE --> BE2[SQLAlchemy]
    BE --> BE3[Flask-Login]
    BE --> BE4[Flask-Mail]

    DB --> DB1[SQLite dev]
    DB --> DB2[PostgreSQL prod]
    DB --> DB3[FAISS vectors]

    AI --> AI1[OpenRouter API]
    AI1 --> AI2[Claude 3.5 Sonnet]
    AI1 --> AI3[Gemini Flash]
    AI1 --> AI4[Claude Haiku]

    EXT --> EXT1[arXiv]
    EXT --> EXT2[SerpAPI]
    EXT --> EXT3[Telegram]

    FILE --> FILE1[pdfplumber]
    FILE --> FILE2[python-docx]
    FILE --> FILE3[pandas]

    PDF --> PDF1[pdflatex]
    PDF --> PDF2[WeasyPrint]

    style APP fill:#172a45,stroke:#64ffda,color:#64ffda
    style FE fill:#112240,stroke:#64ffda,color:#ccd6f6
    style BE fill:#112240,stroke:#64ffda,color:#ccd6f6
    style DB fill:#112240,stroke:#64ffda,color:#ccd6f6
    style AI fill:#112240,stroke:#64ffda,color:#ccd6f6
    style EXT fill:#112240,stroke:#64ffda,color:#ccd6f6
    style FILE fill:#112240,stroke:#64ffda,color:#ccd6f6
    style PDF fill:#112240,stroke:#64ffda,color:#ccd6f6
```

---

## 12. Complete User Flow - The Full Picture

```mermaid
%%{init: {'theme': 'base', 'themeVariables': { 'primaryColor': '#112240', 'primaryTextColor': '#ccd6f6', 'primaryBorderColor': '#64ffda', 'lineColor': '#64ffda', 'secondaryColor': '#172a45', 'tertiaryColor': '#0a192f', 'actorBkg': '#112240', 'actorBorder': '#64ffda', 'actorTextColor': '#ccd6f6', 'signalColor': '#64ffda', 'signalTextColor': '#ccd6f6', 'labelBoxBkgColor': '#172a45', 'labelBoxBorderColor': '#64ffda', 'labelTextColor': '#ccd6f6', 'loopTextColor': '#64ffda', 'noteBkgColor': '#172a45', 'noteTextColor': '#ccd6f6', 'noteBorderColor': '#64ffda', 'activationBkgColor': '#112240', 'activationBorderColor': '#64ffda', 'sequenceNumberColor': '#64ffda'}}}%%
sequenceDiagram
    actor Student
    participant Web as Web Browser
    participant API as Flask API
    participant DB as Database
    participant AI as OpenRouter AI
    participant Email as Email Server

    Note over Student,Email: Learning Session Flow

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
    API->>AI: Generate feedback
    AI-->>API: Explanations
    API-->>Web: Score + feedback

    Student->>Web: 4. Create flashcards
    Web->>API: POST /api/fsrs/bulk-create
    API->>DB: Create FSRSCard entries

    Student->>Web: 5. Request study sheet
    Web->>API: POST /api/fiche/send-email
    API->>AI: Generate summary
    AI-->>API: HTML with LaTeX
    API->>API: Convert to PDF
    API->>Email: Send attachment
    Email-->>Student: PDF in inbox

    Note over Student,Email: Next Day - Review

    Student->>Web: 6. Open spaced repetition
    Web->>API: GET /api/fsrs/due-cards
    API->>DB: Find due cards
    DB-->>API: Due cards
    API-->>Web: Cards to review

    Student->>Web: 7. Rate card
    Web->>API: POST /api/fsrs/review
    API->>API: Run FSRS algorithm
    API->>DB: Update card
    API-->>Web: Next card
```

---

## How to Use These Diagrams

1. **View Online**: Go to [mermaid.live](https://mermaid.live), paste any diagram code
2. **Export**: Download as PNG or SVG for presentations
3. **GitHub**: This file renders automatically on GitHub
4. **Presentations**: Export and add to PowerPoint/Google Slides

---

*Created for Star Learning Platform - M1 ML Business Project*
