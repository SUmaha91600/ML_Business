# Star Learning - Academic Learning Platform

## Overview

Star Learning is a comprehensive web-based educational platform that leverages artificial intelligence to enhance the learning experience for students and researchers. The application combines interactive quiz generation, progress tracking, skills analysis, research paper discovery, and automated generation of academic study materials (fiches).

---

## Core Features

### 1. Intelligent Quiz Generation

**Description:**
Generate customized quizzes from uploaded lecture materials using AI. The system analyzes your documents and creates contextual questions tailored to your learning level.

**Capabilities:**
- Upload multiple file formats: PDF, DOCX, TXT, MD, HTML, CSV, Excel
- AI-powered question generation from lecture content
- Multiple difficulty levels (1-5):
  - Level 1: First-year undergraduate (basic concepts)
  - Level 2: Second-year undergraduate (simple inferences)
  - Level 3: Advanced undergraduate (synthesizing concepts)
  - Level 4: Master's level (abstract thinking)
  - Level 5: Expert level (MIT/Cambridge/ENS style - deep intuition)
- Question types:
  - Multiple Choice Questions (MCQ) with 4 options
  - Free-form theory questions
  - Coding exercises (for programming topics)
- Typically generates 10 questions per quiz
- Maximum 48,000 characters from source material

**How It Works:**
1. Upload your lecture materials (slides, notes, textbooks)
2. Select difficulty level
3. AI analyzes content and generates relevant questions
4. Take the quiz and receive immediate feedback
5. Get detailed explanations for incorrect answers

**Technical Details:**
- Uses OpenRouter API with Claude 3.5 Sonnet for superior question quality
- Fallback to Gemini Flash and Claude Haiku if primary model unavailable
- Processes up to 1000 rows for table-based documents
- Supports multiple files in a single quiz generation

---

### 2. Topic-Based Learning & Organization

**Description:**
Organize your learning materials by topic and generate quizzes without uploading files each time.

**Capabilities:**
- Create custom topics (e.g., "Machine Learning", "Quantum Physics")
- Associate multiple files with each topic
- Generate quizzes directly from topic name (searches academic sources)
- Merge topics to combine related materials
- Delete topics and associated files
- Persistent storage of topics and files

**Workflow:**
1. Create a topic (e.g., "Deep Learning")
2. Upload related lecture materials to that topic
3. Generate quizzes anytime from the topic
4. System can also auto-fetch content from academic sources if no files uploaded

**Auto-Content Discovery:**
- If no files uploaded, system searches:
  - Google Scholar (.edu domains)
  - arXiv.org (research papers)
  - Academic websites (.ac.*)
- Automatically downloads and processes first relevant PDF
- Generates questions from discovered content

---

### 3. Academic Fiche Generation (Study Sheets)

**Description:**
Automatically generate comprehensive, publication-quality academic summary sheets (fiches) with mathematical derivations, rendered equations, and proper citations.

**Capabilities:**
- Generate detailed academic summaries for any topic
- Beautiful LaTeX-rendered mathematical equations
- Professional PDF output with proper typesetting
- Includes:
  - Introduction with historical context
  - 5-10 key definitions (2-4 sentences each)
  - 10-15 mathematical equations with full derivations
  - Complete notation explanations for every variable
  - Key theorems and proofs
  - Worked examples and applications
  - Academic references with clickable links
- Email delivery of generated PDF
- Suitable for Master's level and above

**Mathematical Features:**
- Full LaTeX compilation using MacTeX (pdflatex)
- Proper rendering of:
  - Fractions, subscripts, superscripts
  - Greek letters (α, β, σ, θ, Σ, etc.)
  - Complex equations with multiple levels
  - Matrices, vectors, integrals, derivatives
- Every equation is accompanied by:
  - Complete definition of all variables
  - Units and constraints
  - Step-by-step derivation
  - Mathematical intuition

**Example Output Structure:**
```
1. Introduction & Overview (2-3 paragraphs)
2. Key Definitions (detailed, context-rich)
3. Mathematical Foundations & Derivations
   - Equation 1 with full explanation
   - Derivation from first principles
   - Intermediate steps
   - [10-15 equations total]
4. Key Theorems & Results
5. Examples & Applications
6. References (clickable arXiv links with authors)
```

**Technical Implementation:**
- AI generates HTML with LaTeX notation ($$...$$)
- HTML converted to LaTeX document
- Compiled using pdflatex for professional output
- Falls back to WeasyPrint if LaTeX unavailable
- Typical output: 150-200 KB, 5-10 pages

**Sources:**
- Uploaded files associated with topic
- Auto-searches arXiv for recent papers (last 365 days)
- Fetches academic PDFs from Google Scholar
- Includes top 3 arXiv papers as references

---

### 4. Progress Tracking & Analytics

**Description:**
Track your learning progress over time with detailed analytics and visualizations.

**Metrics Tracked:**
- Quiz scores (percentage)
- Topics studied
- Difficulty levels attempted
- Number of questions per quiz
- Date and time of each attempt
- Historical performance trends

**Data Storage:**
- Stored in `data/progress.csv`
- Persistent across sessions
- User-specific tracking (linked to account)
- Exportable data for external analysis

**Potential Visualizations:**
- Score trends over time
- Performance by topic
- Difficulty level distribution
- Study frequency patterns

---

### 5. CV & Skills Gap Analysis

**Description:**
Compare your skills against job requirements using AI-powered analysis. Identify skill gaps and areas for improvement.

**Capabilities:**
- Upload your CV/resume (PDF, DOCX, TXT)
- Three input methods for job requirements:
  1. **Search**: Enter job title, system fetches real job listings
  2. **Upload**: Upload job description files
  3. **Paste**: Directly paste job description text
- AI extracts skills from both CV and job descriptions
- Assigns proficiency levels (0-100) to each skill
- Visual comparison of your skills vs. required skills
- Identifies:
  - Skills you have that match requirements
  - Skills you're strong in
  - Skill gaps (required but missing)
  - Skills exceeding requirements

**Analysis Output:**
- Radar chart or bar graph comparing skills
- Sorted by requirement priority
- Each skill shows:
  - Your level (0-100)
  - Required level (0-100)
  - Gap analysis

**Data Sources:**
- SerpAPI for real-time job search results
- AI analysis of job descriptions
- Intelligent skill extraction and categorization

---

### 6. arXiv Research Paper Discovery

**Description:**
Stay updated with the latest research papers in your field. Search arXiv and receive paper summaries via Telegram.

**Capabilities:**
- Search arXiv by keywords
- Filter by date range (e.g., last 7, 30, 365 days)
- Specify number of papers (up to 100)
- Sorted by relevance
- Delivers results via Telegram bot
- **Researcher Access Only**

**Requirements:**
- Researcher or Admin role
- Telegram Bot Token
- Telegram Chat ID

**Output Format (Telegram message):**
```
Hello! Here are the 5 most recent papers for your query: *Deep Learning*

1. *Paper Title Here*
   Published: 2025-10-10
   [Link](https://arxiv.org/pdf/1234.5678)

2. *Another Paper*
   Published: 2025-10-08
   [Link](https://arxiv.org/pdf/2345.6789)
...
```

**Use Cases:**
- Literature review for research
- Staying current in your field
- Finding related work for papers
- Discovering new methodologies

---

### 7. Quiz Review & Feedback System

**Description:**
After completing a quiz, receive detailed AI-generated feedback on incorrect answers.

**Features:**
- Shows your score (percentage)
- Lists all incorrect answers
- For each mistake:
  - Your answer
  - Correct answer
  - Detailed explanation of why you were wrong
  - Explanation of the correct concept
- Helps reinforce learning from errors
- Adaptive feedback based on difficulty level

**Feedback Quality:**
- Context-aware explanations
- References lecture material
- Provides intuition, not just facts
- Tailored to your knowledge level

---

## User Roles & Authentication

### Student Role
**Access:**
- Quiz generation from files and topics
- Fiche generation (study sheets)
- Progress tracking
- Skills analysis
- Quiz feedback and review

**Registration:**
- Open to anyone
- Email verification
- Standard login with password

### Researcher Role
**Access:**
- All student features, plus:
- arXiv paper search
- Telegram notifications
- Priority support

**Registration Requirements:**
- Must use educational email domain:
  - `.edu` (US universities)
  - `.ac.uk` (UK universities)
  - `.ac.in`, `.ac.jp`, `.ac.cn` (other academic institutions)
- Receives secret access code via email
- Two-factor authentication (2FA) with secret code

### Admin Role
**Access:**
- All platform features
- User management capabilities
- System administration

**Special Accounts:**
- `mahadevan.sutharsan@gmail.com` (auto-admin)
- `bcivitcioglu@gmail.com` (researcher)

---

## Security Features

### Authentication
- Secure password hashing (bcrypt)
- Session management (Flask-Login)
- Remember me functionality
- Two-factor authentication for researchers

### Password Recovery
- Forgot password flow
- New password generation
- Email delivery of credentials
- Forgot 2FA code regeneration

### Email Notifications
- Welcome emails for new users
- Secret code delivery for researchers
- Admin notification on new registrations
- Fiche delivery via email
- Password reset emails

---

## Technical Architecture

### Backend Stack
- **Framework**: Flask (Python)
- **Database**: SQLite with SQLAlchemy ORM
- **Authentication**: Flask-Login, Flask-Bcrypt
- **Email**: Flask-Mail (SMTP via Gmail)
- **PDF Generation**:
  - Primary: LaTeX (pdflatex) for equations
  - Fallback: WeasyPrint
- **AI Models**:
  - Claude 3.5 Sonnet (primary)
  - Gemini Flash 1.5
  - Claude 3 Haiku (backup)
- **APIs**:
  - OpenRouter (AI models)
  - SerpAPI (job search, Google Scholar)
  - arXiv API (research papers)
  - Telegram Bot API

### Frontend Stack
- HTML5, CSS3, JavaScript
- Responsive design
- Galaxy/space theme with animations
- Custom CSS for logo and styling
- Dynamic content loading with AJAX

### File Processing
- PDF: pdfplumber
- DOCX: python-docx
- HTML: BeautifulSoup + lxml
- Tables: pandas (CSV, TSV, Excel)
- Text: UTF-8 encoding with error handling

### Data Storage
- **Database** (`site.db`): User accounts, authentication
- **Topics** (`data/topics.json`): Topic organization
- **Progress** (`data/progress.csv`): Quiz results
- **Uploads** (`progress_data/`): User-uploaded files

---

## AI Model Strategy

### Primary Model: Claude 3.5 Sonnet
- Best for mathematical content
- Superior reasoning and derivations
- Excellent at following complex instructions
- Used for fiche generation
- Cost: ~$3 per million tokens

### Fallback Models
1. **Gemini Flash 1.5**: Free, good quality, may have rate limits
2. **Claude 3 Haiku**: Fast, cheap ($0.25/M tokens), reliable

### Model Selection Logic
- Tries models in order until one succeeds
- Automatic retry on rate limits
- Falls back gracefully on errors
- Logs which model was used

---

## Configuration

### Environment Variables (.env)
```bash
# Email Configuration
MAIL_SERVER='smtp.gmail.com'
MAIL_PORT=587
MAIL_USERNAME='your-email@gmail.com'
MAIL_PASSWORD='your-app-password'

# API Keys
OPENROUTER_API_KEY='sk-or-v1-...'
SERPAPI_API_KEY='your-serpapi-key'

# Optional
SECRET_KEY='your-secret-key'
DATABASE_URL='sqlite:///site.db'
```

### Requirements
- Python 3.7+
- MacTeX (for LaTeX compilation)
- Virtual environment recommended
- Internet connection for APIs

---

## Installation & Setup

### 1. Clone/Download Project
```bash
cd /path/to/Project
```

### 2. Create Virtual Environment
```bash
python3 -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
```

### 3. Install Dependencies
```bash
pip install -r requirements.txt
```

### 4. Configure Environment
Create `.env` file in parent directory with your credentials (see Configuration section).

### 5. Initialize Database
```bash
python create_db.py
```

### 6. Run Application
```bash
python3 webapp/server.py
```

Visit: `http://localhost:5001`

---

## Usage Examples

### Example 1: Generate Quiz from Lecture
1. Log in to your account
2. Go to "Generate Quiz" section
3. Upload lecture PDF (e.g., "Machine_Learning_Lecture5.pdf")
4. Select difficulty level 3
5. Click "Generate"
6. Take the quiz
7. Review feedback on incorrect answers

### Example 2: Create Topic and Generate Fiche
1. Navigate to "Topics" section
2. Create new topic: "Neural Networks"
3. Upload course materials (textbook chapters, lecture notes)
4. Go to "Fiche" section
5. Select "Neural Networks" topic
6. Click "Generate Fiche"
7. Click "Send to Email"
8. Receive professional PDF with equations

### Example 3: Skills Gap Analysis
1. Go to "Skills Analysis" section
2. Upload your CV (PDF format)
3. Select "Search" method
4. Enter job title: "Machine Learning Engineer"
5. Click "Analyze"
6. View your skills compared to job requirements
7. Identify areas for improvement

### Example 4: Research Paper Discovery (Researchers)
1. Go to "Research Assistant" section
2. Enter keywords: "transformer neural networks"
3. Set date range: Last 30 days
4. Number of papers: 10
5. Enter Telegram Bot Token and Chat ID
6. Click "Search"
7. Receive paper list via Telegram

---

## API Endpoints

### Quiz Generation
- `POST /api/generate` - Generate quiz from uploaded files
- `POST /api/generate-from-topic` - Generate quiz from topic name

### Topics Management
- `GET /api/topics` - Get all topics
- `POST /api/topics` - Create new topic
- `DELETE /api/topics` - Delete topic
- `POST /api/topics/merge` - Merge two topics

### Feedback & Progress
- `POST /api/get-feedback` - Get AI feedback on quiz answers
- `GET /api/progress-data` - Get user's progress history

### Skills Analysis
- `POST /api/analyze-skills` - Analyze CV vs job description

### Research
- `POST /api/arxiv-search` - Search arXiv papers (researcher only)

### Fiche Generation
- `POST /api/fiche/send-email` - Generate and email fiche

---

## Troubleshooting

### Issue: PDFs are blank
**Solution**: Ensure pdflatex is installed and in PATH (`/Library/TeX/texbin/pdflatex` on Mac)

### Issue: Rate limit errors (429)
**Solution**:
- Wait 10-15 minutes for rate limit to reset
- Use paid API key with credits
- System will automatically try fallback models

### Issue: Equations not rendering
**Check**: Look for `[INFO] Using pdflatex at: /path/to/pdflatex` in terminal logs

### Issue: Email not sending
**Check**:
- Gmail App Password is correct (not regular password)
- MAIL_SERVER and MAIL_PORT are correct
- Internet connection active

### Issue: Quiz generation fails
**Check**:
- OPENROUTER_API_KEY is valid
- File format is supported
- File size is reasonable (< 10 MB)

---

## Performance Metrics

### Typical Response Times
- Quiz generation: 5-15 seconds
- Fiche generation: 10-20 seconds (with LaTeX compilation)
- Skills analysis: 8-12 seconds
- arXiv search: 3-5 seconds

### Resource Usage
- PDF generation: 3-5 seconds for LaTeX compilation
- AI generation: Depends on model and content length
- Database queries: < 100ms

### Scalability
- SQLite suitable for < 1000 concurrent users
- For production, migrate to PostgreSQL
- Consider Redis for session management
- CDN for static assets

---

## Future Enhancements

### Potential Features
1. **Spaced Repetition**: Implement Anki-style spaced repetition algorithm
2. **Collaborative Learning**: Share topics and quizzes with classmates
3. **Video Integration**: Generate quizzes from YouTube lectures
4. **Mobile App**: Native iOS/Android applications
5. **Gamification**: Badges, leaderboards, achievements
6. **Study Groups**: Create and join study groups
7. **Flashcards**: Auto-generate flashcards from materials
8. **Diagram Generation**: TikZ diagrams in fiches
9. **Voice Quizzes**: Audio-based questions and answers
10. **Multi-language Support**: Interface in multiple languages

### Technical Improvements
1. Migrate to PostgreSQL for production
2. Implement caching (Redis) for faster responses
3. Add API rate limiting per user
4. Implement WebSocket for real-time updates
5. Add comprehensive logging and monitoring
6. Create admin dashboard for analytics
7. Implement automated testing suite
8. Add Docker containerization
9. Set up CI/CD pipeline
10. Implement content delivery network (CDN)

---

## Credits & Technologies

### AI Models
- **Claude 3.5 Sonnet** by Anthropic
- **Gemini Flash 1.5** by Google
- **Claude 3 Haiku** by Anthropic

### APIs & Services
- **OpenRouter**: AI model aggregation
- **SerpAPI**: Web search and job listings
- **arXiv API**: Research paper access
- **Telegram Bot API**: Messaging

### Libraries & Frameworks
- **Flask**: Web framework
- **SQLAlchemy**: Database ORM
- **BeautifulSoup**: HTML/XML parsing
- **pandas**: Data analysis
- **pdfplumber**: PDF text extraction
- **WeasyPrint**: HTML to PDF conversion
- **MacTeX**: LaTeX distribution

### Development
- **Python 3.14**: Programming language
- **SQLite**: Development database
- **Git**: Version control

---

## License & Support

### License
Proprietary - Star Learning Platform

### Support
- Email: mahadevan.sutharsan@gmail.com
- Created by: M. Sutharsan
- Organization: Aivancity - M1 ML Business

### Contributing
This is a private educational project. For questions or collaborations, contact the admin.

---

## Appendix

### File Structure
```
Project/
├── webapp/
│   ├── server.py          # Main Flask application
│   ├── static/            # CSS, JS, images
│   │   ├── style.css
│   │   ├── galaxy.css
│   │   ├── animation.css
│   │   ├── logo.css
│   │   └── main.js
│   └── templates/         # HTML templates
│       ├── index.html
│       ├── login.html
│       ├── register.html
│       ├── verify_2fa.html
│       ├── forgot_password.html
│       └── review.html
├── data/
│   ├── topics.json        # Topic storage
│   └── progress.csv       # Quiz results
├── progress_data/         # User uploads
├── utils.py               # Utility functions
├── create_db.py           # Database initialization
├── requirements.txt       # Python dependencies
├── site.db               # SQLite database
└── CLAUDE.md             # This documentation
```

### Database Schema

**User Table**
```sql
CREATE TABLE user (
    id INTEGER PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(120) UNIQUE NOT NULL,
    password VARCHAR(60) NOT NULL,
    role VARCHAR(20) NOT NULL DEFAULT 'student',
    researcher_secret_code VARCHAR(16)
);
```

### Supported File Formats

| Format | Extensions | Use Case |
|--------|------------|----------|
| Text | .txt, .md | Plain text documents |
| PDF | .pdf | Academic papers, textbooks |
| Word | .docx | Lecture notes, assignments |
| HTML | .html, .htm | Web content |
| Tables | .csv, .tsv, .xlsx, .xls | Data, statistics |

---

**Last Updated**: October 16, 2025
**Version**: 2.0
**Author**: M. Sutharsan (with assistance from Claude)
