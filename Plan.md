# Project Plan: AI-Powered Learning Tracker

## 1. Core Application

- **Objective:** Develop a Python application using Streamlit to track and enhance skill acquisition.
- **Primary Interface:** A web-based dashboard built with Streamlit.

## 2. Feature Breakdown

### 2.1. Topic & Content Management

- **UI:** A scrolling panel where users can manage their learning topics.
- **Actions:**
    - Create a new topic section.
    - Assign a name to the topic.
    - Upload learning materials (PDF, DOCX, TXT, etc.).

### 2.2. Automated Quiz & Assessment

- **Engine:** An AI-powered module to generate questions from uploaded content.
- **Question Types:**
    - **10 Theoretical Questions:** Covering definitions, key concepts, and mathematical formulas.
    - **5 Practical Questions:** Coding problems with adjustable difficulty based on the user's demonstrated skill level (If any Computer Langage lectures).

### 2.3. Progress Tracking & Visualization

- **Dashboard:** A personal dashboard for each topic.
- **Visualization:** A graph plotting `Grade` (Y-axis) against `Date` (X-axis) to show daily growth.
- **Data Persistence:** All progress data will be saved to a local CSV file for tracking and analysis.

### 2.4. AI Research Assistant

- **Target Audience:** Researchers, PhD students, and academics.
- **Functionality:**
    - Users input keywords or a research subject.
    - An AI agent fetches the latest papers from arXiv.
    - Users can set a weekly reading goal (e.g., 5 papers/week).
- **Notifications:** Paper titles and links are sent directly to the user via a messaging service like Telegram.

## 3. Proposed Technology Stack

- **Application Framework:** Streamlit
- **Data Storage:** CSV
- **AI & Data Processing:**
    - Large Language Model (LLM) for question generation.
    - ArXiv API for fetching research papers.
- **Notification Service:** Telegram Bot API
