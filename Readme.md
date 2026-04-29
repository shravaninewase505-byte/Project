# AI-First CRM: HCP Module

This project is an AI-powered CRM module for field representatives to log interactions with Healthcare Professionals (HCPs). It features a split-screen interface allowing for standard form entry or conversational logging via an AI Assistant.

## Tech Stack
* **Frontend:** React, Redux, Google Inter Font
* **Backend:** Python, FastAPI
* **AI Agent:** LangGraph, Groq (gemma2-9b-it)
* **Database:** PostgreSQL, SQLAlchemy

## Project Structure
```text
/crm-project
  ├── /frontend            # React App
  │   ├── src/
  │   │   ├── LogInteractionScreen.jsx
  │   │   ├── store.js
  │   │   └── LogInteractionScreen.css
  ├── /backend             # FastAPI backend
  │   ├── main.py
  │   ├── database.py
  │   └── ai_agent.py
  └── README.md
```

## Setup Instructions

### 1. Database Setup
Create a PostgreSQL database and user:
```sql
CREATE DATABASE crm_db;
CREATE USER crm_user WITH ENCRYPTED PASSWORD 'secure_password';
GRANT ALL PRIVILEGES ON DATABASE crm_db TO crm_user;
```

### 2. Backend Setup
Install dependencies and run the server:
```bash
pip install fastapi "uvicorn[standard]" sqlalchemy psycopg2-binary langchain-groq langgraph
python database.py # To generate the tables
uvicorn main:app --reload
```

### 3. Frontend Setup
Install dependencies and start React:
```bash
npm install @reduxjs/toolkit react-redux
npm start
```
