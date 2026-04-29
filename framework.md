# ai_agent.py
import json
import re
from typing import TypedDict, Dict, Any
from langgraph.graph import StateGraph, END
from langchain_groq import ChatGroq
from langchain_core.messages import HumanMessage, SystemMessage

# 1. Define the LangGraph State
class AgentState(TypedDict):
    user_message: str
    current_form: Dict[str, Any]
    updated_form: Dict[str, Any]
    ai_reply: str

# 2. Initialize Groq LLM
# Utilizing gemma2-9b-it as requested
llm = ChatGroq(
    temperature=0, 
    model_name="gemma2-9b-it", 
    groq_api_key="YOUR_GROQ_API_KEY" # Replace with your Groq API Token
)

# 3. Define the Extraction Node
def process_chat_and_update_form(state: AgentState):
    user_msg = state["user_message"]
    current_form = state["current_form"]
    
    system_prompt = f"""
    You are an AI Assistant in a Pharma CRM. Your job is to extract interaction details 
    from the field rep's message and update the current form state.
    
    Current Form State: {json.dumps(current_form)}
    
    Extract: hcp_name, interaction_type, date, time, attendees, topics_discussed, sentiment (Positive/Neutral/Negative), outcomes, follow_up_actions.
    
    Output ONLY a JSON object containing the updated fields. If a field is not mentioned, retain its current value.
    Also include a field called "ai_reply" with a brief, friendly confirmation message to the user.
    """
    
    response = llm.invoke([
        SystemMessage(content=system_prompt),
        HumanMessage(content=user_msg)
    ])
    
    # Parse JSON from Gemma's response
    try:
        match = re.search(r'\{.*\}', response.content, re.DOTALL)
        extracted_data = json.loads(match.group(0)) if match else {}
    except Exception:
        extracted_data = current_form # Fallback

    ai_reply = extracted_data.pop("ai_reply", "Got it. I've updated the form based on your notes.")
    
    # Merge existing form with newly extracted data
    updated_form = {**current_form, **extracted_data}
    
    return {"updated_form": updated_form, "ai_reply": ai_reply}

# 4. Build and Compile the Graph
workflow = StateGraph(AgentState)
workflow.add_node("process_chat", process_chat_and_update_form)
workflow.set_entry_point("process_chat")
workflow.add_edge("process_chat", END)

crm_ai_agent = workflow.compile()