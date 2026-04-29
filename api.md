# main.py
from fastapi import FastAPI, Depends
from pydantic import BaseModel
from sqlalchemy.orm import Session
from database import SessionLocal, HCPInteraction
from ai_agent import crm_ai_agent

app = FastAPI(title="AI-First CRM API")

def get_db():
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()

class ChatRequest(BaseModel):
    user_message: str
    current_form: dict

class InteractionLog(BaseModel):
    hcp_name: str | None = None
    interaction_type: str | None = None
    date: str | None = None
    time: str | None = None
    topics_discussed: str | None = None
    sentiment: str | None = None
    outcomes: str | None = None
    follow_up_actions: str | None = None

@app.post("/api/chat")
async def handle_chat(request: ChatRequest):
    """Passes chat to LangGraph to extract form entities"""
    initial_state = {
        "user_message": request.user_message,
        "current_form": request.current_form,
        "updated_form": {},
        "ai_reply": ""
    }
    
    # Run LangGraph Agent
    result = crm_ai_agent.invoke(initial_state)
    
    return {
        "updated_form": result["updated_form"],
        "ai_reply": result["ai_reply"]
    }

@app.post("/api/interactions")
async def save_interaction(log: InteractionLog, db: Session = Depends(get_db)):
    """Saves the final form to the PostgreSQL database"""
    new_interaction = HCPInteraction(**log.dict())
    db.add(new_interaction)
    db.commit()
    db.refresh(new_interaction)
    return {"status": "success", "id": new_interaction.id}