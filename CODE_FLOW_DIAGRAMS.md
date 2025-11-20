# Star Learning Platform - Code Flow Diagrams

> **How to view these diagrams:**
> - Copy to [mermaid.live](https://mermaid.live) for instant rendering
> - View directly on GitHub (renders automatically)
> - Export as PNG/SVG from mermaid.live

> **Color Palette (Star Learning Theme):**
> - Primary Accent: #64ffda
> - Dark Background: #0a192f
> - Card Background: #112240
> - Border: #172a45
> - Text: #ccd6f6

---

## 1. Application Initialization Flow

**File: webapp/server.py**

```mermaid
%%{init: {'theme': 'base', 'themeVariables': { 'primaryColor': '#112240', 'primaryTextColor': '#ccd6f6', 'primaryBorderColor': '#64ffda', 'lineColor': '#64ffda', 'secondaryColor': '#172a45', 'tertiaryColor': '#0a192f'}}}%%
flowchart TD
    subgraph "Step 1: Load Environment"
        A[load_dotenv] --> B[Import Flask extensions]
        B --> C[Import utils.py functions]
        C --> D[Import RAG modules]
    end

    subgraph "Step 2: Create Flask App"
        D --> E[app = Flask]
        E --> F[Configure SECRET_KEY]
        F --> G[Configure SQLALCHEMY_DATABASE_URI]
        G --> H[Configure MAIL settings]
    end

    subgraph "Step 3: Initialize Extensions"
        H --> I[mail = Mail app]
        I --> J[db = SQLAlchemy app]
        J --> K[bcrypt = Bcrypt app]
        K --> L[login_manager = LoginManager app]
    end

    subgraph "Step 4: Define Models"
        L --> M[User model]
        M --> N[Topic, TopicFile models]
        N --> O[FSRSCard, FSRSReview models]
        O --> P[Paper, PaperProject models]
        P --> Q[Concept, ConceptDependency models]
    end

    subgraph "Step 5: Register Routes"
        Q --> R[Authentication routes]
        R --> S[API routes]
        S --> T[app.run port=5001]
    end

    style A fill:#112240,stroke:#64ffda,color:#ccd6f6
    style E fill:#172a45,stroke:#64ffda,color:#64ffda
    style I fill:#112240,stroke:#64ffda,color:#ccd6f6
    style T fill:#172a45,stroke:#64ffda,color:#64ffda
```

---

## 2. User Authentication Flow

**Files: webapp/server.py - /register, /login routes**

```mermaid
%%{init: {'theme': 'base', 'themeVariables': { 'primaryColor': '#112240', 'primaryTextColor': '#ccd6f6', 'primaryBorderColor': '#64ffda', 'lineColor': '#64ffda', 'secondaryColor': '#172a45', 'tertiaryColor': '#0a192f'}}}%%
flowchart TD
    subgraph "Registration Flow"
        A[POST /register] --> B[Get form data: name, email, password]
        B --> C{Email domain check}
        C -->|.edu/.ac.*| D[Role = researcher]
        C -->|Other| E[Role = student]
        D --> F[Generate researcher_secret_code]
        E --> G[Hash password with bcrypt]
        F --> G
        G --> H[Create User object]
        H --> I[db.session.add user]
        I --> J[db.session.commit]
        J --> K[Send welcome email]
        K --> L[Redirect to login]
    end

    style A fill:#112240,stroke:#64ffda,color:#ccd6f6
    style G fill:#172a45,stroke:#64ffda,color:#64ffda
    style J fill:#112240,stroke:#64ffda,color:#ccd6f6
    style L fill:#172a45,stroke:#64ffda,color:#64ffda
```

```mermaid
%%{init: {'theme': 'base', 'themeVariables': { 'primaryColor': '#112240', 'primaryTextColor': '#ccd6f6', 'primaryBorderColor': '#64ffda', 'lineColor': '#64ffda', 'secondaryColor': '#172a45', 'tertiaryColor': '#0a192f'}}}%%
flowchart TD
    subgraph "Login Flow"
        A[POST /login] --> B[Get email, password]
        B --> C[User.query.filter_by email]
        C --> D{User exists?}
        D -->|No| E[Flash error message]
        D -->|Yes| F[bcrypt.check_password_hash]
        F --> G{Password valid?}
        G -->|No| E
        G -->|Yes| H{Role == researcher?}
        H -->|Yes| I[Redirect to /verify-2fa]
        H -->|No| J[login_user user]
        I --> K[Enter secret code]
        K --> L{Code matches?}
        L -->|Yes| J
        L -->|No| E
        J --> M[Redirect to index]
    end

    style A fill:#112240,stroke:#64ffda,color:#ccd6f6
    style F fill:#172a45,stroke:#64ffda,color:#64ffda
    style J fill:#112240,stroke:#64ffda,color:#64ffda
    style M fill:#172a45,stroke:#64ffda,color:#64ffda
```

---

## 3. Quiz Generation Code Flow

**Files: webapp/server.py - /api/generate, utils.py - openrouter_generate_questions**

```mermaid
%%{init: {'theme': 'base', 'themeVariables': { 'primaryColor': '#112240', 'primaryTextColor': '#ccd6f6', 'primaryBorderColor': '#64ffda', 'lineColor': '#64ffda', 'secondaryColor': '#172a45', 'tertiaryColor': '#0a192f'}}}%%
flowchart TD
    subgraph "Step 1: Receive Request"
        A[POST /api/generate] --> B[request.files.getlist files]
        B --> C[request.form.get level]
        C --> D[request.form.get style]
    end

    subgraph "Step 2: Process Files"
        D --> E[Loop through files]
        E --> F[Save file to UPLOAD_DIR]
        F --> G[Call read_all_text_from_file]
        G --> H{File extension?}
        H -->|.pdf| I[pdfplumber.open]
        H -->|.docx| J[Document from docx]
        H -->|.txt/.md| K[file.read_text]
        H -->|.csv/.xlsx| L[pd.read_csv/excel]
        I --> M[Combine all text]
        J --> M
        K --> M
        L --> M
    end

    subgraph "Step 3: Generate Questions"
        M --> N[Truncate to 48000 chars]
        N --> O[Call openrouter_generate_questions]
    end

    style A fill:#112240,stroke:#64ffda,color:#ccd6f6
    style G fill:#172a45,stroke:#64ffda,color:#64ffda
    style O fill:#112240,stroke:#64ffda,color:#64ffda
```

```mermaid
%%{init: {'theme': 'base', 'themeVariables': { 'primaryColor': '#112240', 'primaryTextColor': '#ccd6f6', 'primaryBorderColor': '#64ffda', 'lineColor': '#64ffda', 'secondaryColor': '#172a45', 'tertiaryColor': '#0a192f'}}}%%
flowchart TD
    subgraph "openrouter_generate_questions function"
        A[Receive: text, level, api_key, model] --> B[Build difficulty_prompt from level 1-5]
        B --> C[Create OpenAI client with OpenRouter base_url]
        C --> D{quiz_type?}
        D -->|coding| E[Build coding exercise prompt]
        D -->|theory| F[Build MCQ/theory prompt]
        E --> G[client.chat.completions.create]
        F --> G
        G --> H[Set temperature=0.2, max_tokens=1800]
        H --> I[response_format = json_object]
    end

    subgraph "Parse Response"
        I --> J[Get completion.choices 0 .message.content]
        J --> K[json.loads response]
        K --> L{Valid JSON?}
        L -->|No| M[Regex search for JSON]
        L -->|Yes| N[Extract questions array]
        M --> N
        N --> O[Clean each question object]
        O --> P[Return list of questions]
    end

    style A fill:#112240,stroke:#64ffda,color:#ccd6f6
    style G fill:#172a45,stroke:#64ffda,color:#64ffda
    style K fill:#112240,stroke:#64ffda,color:#ccd6f6
    style P fill:#172a45,stroke:#64ffda,color:#64ffda
```

---

## 4. Fiche/Study Sheet Generation Flow

**Files: webapp/server.py - /api/fiche/send-email, utils.py - generate_detailed_summary, create_pdf**

```mermaid
%%{init: {'theme': 'base', 'themeVariables': { 'primaryColor': '#112240', 'primaryTextColor': '#ccd6f6', 'primaryBorderColor': '#64ffda', 'lineColor': '#64ffda', 'secondaryColor': '#172a45', 'tertiaryColor': '#0a192f'}}}%%
flowchart TD
    subgraph "Step 1: Gather Content"
        A[POST /api/fiche/send-email] --> B[Get topic_name from request]
        B --> C[get_topics for current_user]
        C --> D{Topic has files?}
        D -->|Yes| E[Loop topic files]
        E --> F[read_all_text_from_file each]
        F --> G[Combine text]
        D -->|No| H[Search Google Scholar via SerpAPI]
        H --> I[Download first PDF result]
        I --> J[Extract text from PDF]
        J --> G
    end

    subgraph "Step 2: Search arXiv"
        G --> K[search_arxiv topic, max=3, days=365]
        K --> L[Build references list with URLs]
    end

    subgraph "Step 3: AI Generation"
        L --> M[Try Claude 3.5 Sonnet]
        M -->|Fail| N[Try Gemini Flash]
        N -->|Fail| O[Try Claude Haiku]
        M -->|Success| P[generate_detailed_summary]
        N -->|Success| P
        O --> P
    end

    style A fill:#112240,stroke:#64ffda,color:#ccd6f6
    style F fill:#172a45,stroke:#64ffda,color:#64ffda
    style K fill:#112240,stroke:#64ffda,color:#ccd6f6
    style P fill:#172a45,stroke:#64ffda,color:#64ffda
```

```mermaid
%%{init: {'theme': 'base', 'themeVariables': { 'primaryColor': '#112240', 'primaryTextColor': '#ccd6f6', 'primaryBorderColor': '#64ffda', 'lineColor': '#64ffda', 'secondaryColor': '#172a45', 'tertiaryColor': '#0a192f'}}}%%
flowchart TD
    subgraph "generate_detailed_summary function"
        A[Receive: text, api_key, model, topic_name] --> B[Build system_msg with HTML format instructions]
        B --> C[Include LaTeX equation requirements]
        C --> D[client.chat.completions.create]
        D --> E[temperature=0.4, max_tokens=4000]
        E --> F[Get HTML content with equations]
    end

    subgraph "create_pdf function"
        F --> G[Check for pdflatex path]
        G --> H{pdflatex found?}
        H -->|Yes| I[Parse HTML with BeautifulSoup]
        I --> J[_html_to_latex conversion]
        J --> K[Build LaTeX document with packages]
        K --> L[Write .tex file to temp dir]
        L --> M[subprocess.run pdflatex twice]
        M --> N[Read generated PDF bytes]
        H -->|No| O[_create_pdf_weasyprint fallback]
        O --> P[HTML.write_pdf]
        N --> Q[Return PDF bytes]
        P --> Q
    end

    subgraph "Send Email"
        Q --> R[Create Flask-Mail Message]
        R --> S[Attach PDF to message]
        S --> T[mail.send message]
        T --> U[Return success response]
    end

    style A fill:#112240,stroke:#64ffda,color:#ccd6f6
    style J fill:#172a45,stroke:#64ffda,color:#64ffda
    style M fill:#112240,stroke:#64ffda,color:#ccd6f6
    style U fill:#172a45,stroke:#64ffda,color:#64ffda
```

---

## 5. FSRS Spaced Repetition Algorithm Flow

**File: webapp/server.py - fsrs_schedule_card function**

```mermaid
%%{init: {'theme': 'base', 'themeVariables': { 'primaryColor': '#112240', 'primaryTextColor': '#ccd6f6', 'primaryBorderColor': '#64ffda', 'lineColor': '#64ffda', 'secondaryColor': '#172a45', 'tertiaryColor': '#0a192f'}}}%%
flowchart TD
    subgraph "Step 1: Get Parameters"
        A[fsrs_schedule_card card, rating, params] --> B[Calculate elapsed_days since last_review]
        B --> C[Save old_state, old_stability, old_difficulty]
        C --> D{card.state?}
    end

    subgraph "Step 2: Calculate Retrievability"
        D -->|new| E[retrievability = 1.0]
        D -->|other| F[fsrs_calculate_retrievability]
        F --> G[R = 1 + elapsed/9*S pow -1]
    end

    subgraph "Step 3: Update Based on State"
        E --> H{State processing}
        G --> H
        H -->|new| I[fsrs_init_difficulty rating]
        I --> J[fsrs_init_stability params, rating]
        H -->|learning| K[fsrs_next_difficulty]
        K --> L[fsrs_next_stability]
        H -->|review| M[fsrs_next_difficulty]
        M --> N[fsrs_next_stability]
        H -->|relearning| O[fsrs_next_difficulty]
        O --> P[fsrs_next_stability]
    end

    style A fill:#112240,stroke:#64ffda,color:#ccd6f6
    style F fill:#172a45,stroke:#64ffda,color:#64ffda
    style H fill:#112240,stroke:#64ffda,color:#ccd6f6
```

```mermaid
%%{init: {'theme': 'base', 'themeVariables': { 'primaryColor': '#112240', 'primaryTextColor': '#ccd6f6', 'primaryBorderColor': '#64ffda', 'lineColor': '#64ffda', 'secondaryColor': '#172a45', 'tertiaryColor': '#0a192f'}}}%%
flowchart TD
    subgraph "Step 4: Calculate Next Interval"
        A[fsrs_next_interval params, stability, R] --> B[Get request_retention default 0.9]
        B --> C[decay = -0.5]
        C --> D[interval = 9 * S * R_target pow decay - 1]
        D --> E[Clamp to 1, maximum_interval]
    end

    subgraph "Step 5: Update Card"
        E --> F[card.stability = new_stability]
        F --> G[card.difficulty = new_difficulty]
        G --> H[card.scheduled_days = interval]
        H --> I[card.last_review = now]
        I --> J[card.due = now + timedelta days]
        J --> K[card.reps += 1]
    end

    subgraph "Step 6: Record Review"
        K --> L[Create FSRSReview object]
        L --> M[Set rating, state_before, stability_before]
        M --> N[db.session.add review]
        N --> O[Return updated card]
    end

    style A fill:#112240,stroke:#64ffda,color:#ccd6f6
    style D fill:#172a45,stroke:#64ffda,color:#64ffda
    style J fill:#112240,stroke:#64ffda,color:#ccd6f6
    style O fill:#172a45,stroke:#64ffda,color:#64ffda
```

---

## 6. Concept Dependency & Learning Path Flow

**File: webapp/server.py - topological_sort_concepts, generate_learning_path**

```mermaid
%%{init: {'theme': 'base', 'themeVariables': { 'primaryColor': '#112240', 'primaryTextColor': '#ccd6f6', 'primaryBorderColor': '#64ffda', 'lineColor': '#64ffda', 'secondaryColor': '#172a45', 'tertiaryColor': '#0a192f'}}}%%
flowchart TD
    subgraph "topological_sort_concepts"
        A[Input: target_concept_id] --> B[Initialize graph dict, in_degree dict]
        B --> C[BFS queue starting with target]
        C --> D[While queue not empty]
        D --> E[Pop concept_id]
        E --> F[Query ConceptDependency for concept]
        F --> G[For each prerequisite]
        G --> H[Add edge prereq to concept in graph]
        H --> I[Increment in_degree for concept]
        I --> J[Add prereq to queue]
        J --> D
    end

    subgraph "Kahn's Algorithm"
        D -->|Queue empty| K[Find nodes with in_degree = 0]
        K --> L[While zero_in_degree not empty]
        L --> M[Pop current node]
        M --> N[Append to sorted_concepts]
        N --> O[For each neighbor in graph current]
        O --> P[Decrement neighbor in_degree]
        P --> Q{in_degree = 0?}
        Q -->|Yes| R[Add to zero_in_degree]
        Q -->|No| L
        R --> L
    end

    subgraph "Return Result"
        L -->|Empty| S{Cycle check}
        S -->|len != all_concepts| T[Raise circular dependency error]
        S -->|OK| U[Return sorted_concepts list]
    end

    style A fill:#112240,stroke:#64ffda,color:#ccd6f6
    style H fill:#172a45,stroke:#64ffda,color:#64ffda
    style N fill:#112240,stroke:#64ffda,color:#ccd6f6
    style U fill:#172a45,stroke:#64ffda,color:#64ffda
```

```mermaid
%%{init: {'theme': 'base', 'themeVariables': { 'primaryColor': '#112240', 'primaryTextColor': '#ccd6f6', 'primaryBorderColor': '#64ffda', 'lineColor': '#64ffda', 'secondaryColor': '#172a45', 'tertiaryColor': '#0a192f'}}}%%
flowchart TD
    subgraph "generate_learning_path"
        A[Input: user_id, target_concept_id] --> B[Call topological_sort_concepts]
        B --> C[Get all_prereqs in order]
        C --> D[Initialize learning_sequence list]
        D --> E[For each concept_id in all_prereqs]
        E --> F[calculate_concept_mastery user, concept]
        F --> G{mastery < 80?}
        G -->|Yes| H[Get Concept from DB]
        H --> I[Add to learning_sequence with details]
        I --> J[Add estimated_hours to total]
        G -->|No| E
        J --> E
    end

    subgraph "Return Path"
        E -->|Done| K[Return learning_sequence, total_hours]
    end

    style A fill:#112240,stroke:#64ffda,color:#ccd6f6
    style F fill:#172a45,stroke:#64ffda,color:#64ffda
    style I fill:#112240,stroke:#64ffda,color:#ccd6f6
    style K fill:#172a45,stroke:#64ffda,color:#64ffda
```

---

## 7. RAG System Flow

**Files: rag_indexer.py, rag_query.py, rag_chunker.py, rag_embeddings.py, rag_vector_store.py**

```mermaid
%%{init: {'theme': 'base', 'themeVariables': { 'primaryColor': '#112240', 'primaryTextColor': '#ccd6f6', 'primaryBorderColor': '#64ffda', 'lineColor': '#64ffda', 'secondaryColor': '#172a45', 'tertiaryColor': '#0a192f'}}}%%
flowchart TD
    subgraph "index_paper_on_upload"
        A[Receive paper_id, file_path] --> B[Extract text with pdfplumber]
        B --> C[rag_chunker: split into chunks]
        C --> D[Chunk size ~500 tokens with overlap]
        D --> E[rag_embeddings: generate vectors]
        E --> F[Use sentence-transformers model]
        F --> G[rag_vector_store: add to FAISS]
        G --> H[Save index with paper metadata]
    end

    style A fill:#112240,stroke:#64ffda,color:#ccd6f6
    style E fill:#172a45,stroke:#64ffda,color:#64ffda
    style H fill:#172a45,stroke:#64ffda,color:#64ffda
```

```mermaid
%%{init: {'theme': 'base', 'themeVariables': { 'primaryColor': '#112240', 'primaryTextColor': '#ccd6f6', 'primaryBorderColor': '#64ffda', 'lineColor': '#64ffda', 'secondaryColor': '#172a45', 'tertiaryColor': '#0a192f'}}}%%
flowchart TD
    subgraph "query_papers"
        A[Receive user query] --> B[Generate query embedding]
        B --> C[FAISS similarity search]
        C --> D[Get top-k relevant chunks]
        D --> E[Retrieve paper metadata for chunks]
        E --> F[Build context from chunks]
        F --> G[Send to Claude with context]
        G --> H[Generate answer with citations]
        H --> I[Return response with paper references]
    end

    style A fill:#112240,stroke:#64ffda,color:#ccd6f6
    style C fill:#172a45,stroke:#64ffda,color:#64ffda
    style G fill:#112240,stroke:#64ffda,color:#ccd6f6
    style I fill:#172a45,stroke:#64ffda,color:#64ffda
```

---

## 8. Skills Analysis Flow

**Files: webapp/server.py - /api/analyze-skills, utils.py - extract_skills_from_cv, extract_skills_from_job_description**

```mermaid
%%{init: {'theme': 'base', 'themeVariables': { 'primaryColor': '#112240', 'primaryTextColor': '#ccd6f6', 'primaryBorderColor': '#64ffda', 'lineColor': '#64ffda', 'secondaryColor': '#172a45', 'tertiaryColor': '#0a192f'}}}%%
flowchart TD
    subgraph "Step 1: Get CV Skills"
        A[POST /api/analyze-skills] --> B[Get CV file from request]
        B --> C[read_all_text_from_file CV]
        C --> D[extract_skills_from_cv text, api_key]
        D --> E[AI extracts skills with levels 0-100]
        E --> F[Return JSON: skill, level pairs]
    end

    subgraph "Step 2: Get Job Requirements"
        A --> G{input_method?}
        G -->|search| H[SerpAPI job search]
        H --> I[Get job descriptions]
        G -->|upload| J[Read uploaded job file]
        G -->|paste| K[Get pasted text]
        I --> L[extract_skills_from_job_description]
        J --> L
        K --> L
        L --> M[AI extracts required skills with levels]
    end

    subgraph "Step 3: Compare & Return"
        F --> N[Combine CV skills and job skills]
        M --> N
        N --> O[Calculate gaps for each skill]
        O --> P[Return comparison JSON]
    end

    style A fill:#112240,stroke:#64ffda,color:#ccd6f6
    style D fill:#172a45,stroke:#64ffda,color:#64ffda
    style L fill:#112240,stroke:#64ffda,color:#ccd6f6
    style P fill:#172a45,stroke:#64ffda,color:#64ffda
```

---

## 9. arXiv Search & Telegram Notification Flow

**Files: utils.py - search_arxiv, send_telegram_message**

```mermaid
%%{init: {'theme': 'base', 'themeVariables': { 'primaryColor': '#112240', 'primaryTextColor': '#ccd6f6', 'primaryBorderColor': '#64ffda', 'lineColor': '#64ffda', 'secondaryColor': '#172a45', 'tertiaryColor': '#0a192f'}}}%%
flowchart TD
    subgraph "search_arxiv function"
        A[Input: keywords, max_results, days_ago] --> B[Calculate date range]
        B --> C[Build arXiv query string]
        C --> D[ti:keywords OR abs:keywords AND submittedDate range]
        D --> E[arxiv.Search with SortCriterion.Relevance]
        E --> F[arxiv.Client.results search]
        F --> G[For each result]
        G --> H[Extract arxiv_id, title, authors]
        H --> I[Build pdf_url]
        I --> J[Append to results list]
        J --> G
        G -->|Done| K[Return results list]
    end

    style A fill:#112240,stroke:#64ffda,color:#ccd6f6
    style E fill:#172a45,stroke:#64ffda,color:#64ffda
    style K fill:#172a45,stroke:#64ffda,color:#64ffda
```

```mermaid
%%{init: {'theme': 'base', 'themeVariables': { 'primaryColor': '#112240', 'primaryTextColor': '#ccd6f6', 'primaryBorderColor': '#64ffda', 'lineColor': '#64ffda', 'secondaryColor': '#172a45', 'tertiaryColor': '#0a192f'}}}%%
flowchart TD
    subgraph "send_telegram_message function"
        A[Input: token, chat_id, message] --> B{Token and chat_id valid?}
        B -->|No| C[Raise ValueError]
        B -->|Yes| D[telegram.Bot token=token]
        D --> E[await bot.send_message]
        E --> F[chat_id, text, parse_mode=Markdown]
        F --> G[Return True on success]
    end

    subgraph "API Route /api/arxiv-search"
        H[POST /api/arxiv-search] --> I[Get keywords, days, max_results]
        I --> J[Get telegram token, chat_id]
        J --> K[Call search_arxiv]
        K --> L[Format results as Markdown]
        L --> M[asyncio.run send_telegram_message]
        M --> N[Return success response]
    end

    style A fill:#112240,stroke:#64ffda,color:#ccd6f6
    style E fill:#172a45,stroke:#64ffda,color:#64ffda
    style K fill:#112240,stroke:#64ffda,color:#ccd6f6
    style N fill:#172a45,stroke:#64ffda,color:#64ffda
```

---

## 10. Database Operations Flow

**File: webapp/server.py - SQLAlchemy operations**

```mermaid
%%{init: {'theme': 'base', 'themeVariables': { 'primaryColor': '#112240', 'primaryTextColor': '#ccd6f6', 'primaryBorderColor': '#64ffda', 'lineColor': '#64ffda', 'secondaryColor': '#172a45', 'tertiaryColor': '#0a192f'}}}%%
flowchart TD
    subgraph "Create Operations"
        A[Create new object] --> B[db.session.add object]
        B --> C[db.session.commit]
        C --> D[Object saved with auto ID]
    end

    subgraph "Read Operations"
        E[Model.query] --> F[.filter_by field=value]
        F --> G[.first or .all]
        G --> H[Return object or list]
        E --> I[db.session.get Model, id]
        I --> H
    end

    subgraph "Update Operations"
        J[Get object from query] --> K[Modify object.field = value]
        K --> L[db.session.commit]
        L --> M[Changes persisted]
    end

    subgraph "Delete Operations"
        N[Get object from query] --> O[db.session.delete object]
        O --> P[db.session.commit]
        P --> Q[Object removed]
    end

    style A fill:#112240,stroke:#64ffda,color:#ccd6f6
    style C fill:#172a45,stroke:#64ffda,color:#64ffda
    style H fill:#112240,stroke:#64ffda,color:#ccd6f6
    style M fill:#172a45,stroke:#64ffda,color:#64ffda
```

---

## 11. Error Handling & Model Fallback Flow

**File: webapp/server.py, utils.py**

```mermaid
%%{init: {'theme': 'base', 'themeVariables': { 'primaryColor': '#112240', 'primaryTextColor': '#ccd6f6', 'primaryBorderColor': '#64ffda', 'lineColor': '#64ffda', 'secondaryColor': '#172a45', 'tertiaryColor': '#0a192f'}}}%%
flowchart TD
    subgraph "AI Model Fallback Strategy"
        A[Need AI generation] --> B[Define models list]
        B --> C[claude-3.5-sonnet, gemini-flash, claude-haiku]
        C --> D[For model in models]
        D --> E[Try API call]
        E --> F{Success?}
        F -->|Yes| G[Return result, break loop]
        F -->|No| H[Log warning]
        H --> I[Continue to next model]
        I --> D
        D -->|All failed| J[Return error response]
    end

    subgraph "Rate Limit Handling"
        K[API returns 429] --> L[Log rate limit error]
        L --> M[Try next model in list]
        M --> N[If all models fail]
        N --> O[Return user-friendly error]
        O --> P[Suggest waiting 10-15 minutes]
    end

    style A fill:#112240,stroke:#64ffda,color:#ccd6f6
    style E fill:#172a45,stroke:#64ffda,color:#64ffda
    style G fill:#112240,stroke:#64ffda,color:#64ffda
    style J fill:#172a45,stroke:#64ffda,color:#ccd6f6
```

---

## How to Use These Diagrams

1. **View Online**: Go to [mermaid.live](https://mermaid.live), paste any diagram code
2. **Export**: Download as PNG or SVG for presentations
3. **GitHub**: This file renders automatically on GitHub
4. **Code Reference**: Use alongside source code for understanding

---

*Created for Star Learning Platform - M1 ML Business Project*
