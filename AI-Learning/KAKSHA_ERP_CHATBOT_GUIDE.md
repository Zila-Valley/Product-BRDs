# Kaksha+ ERP AI Chatbot: Absolute Beginner's Implementation Guide

Welcome! This guide will teach you exactly how to build a highly intelligent AI Chatbot for the Kaksha+ ERP system. 

Because Kaksha+ is a multi-tenant SaaS platform (meaning multiple schools use the same system), the chatbot must be **context-aware**. If a parent asks *"When is the math exam?"*, the AI must know exactly *which school* the parent belongs to, and *which student* they are asking about, so it doesn't accidentally reveal another school's data.

---

## 1. The Architecture (How it works)

To answer questions based on structured database data (like exams, holidays, and grades), we cannot use standard RAG (Vector Databases). Instead, we use a concept called **Function Calling (or Tool Use)**.

1.  **The User Asks:** "When is the math exam?"
2.  **The Interceptor:** Our backend receives the message and invisibly attaches the `school_id` and `student_id` (pulled from their login token) to the request.
3.  **The AI Thinks:** The LLM receives the question. It realizes it needs to look up the database.
4.  **The Tool Call:** The LLM triggers a predefined Python function we wrote called `get_exam_schedule(school_id, class_id)`.
5.  **The Database Query:** Our Python function runs a standard SQL query and returns the dates to the LLM.
6.  **The Answer:** The LLM formats the dates into a friendly, human-readable sentence and sends it to the user.

---

## 2. The Tech Stack
*   **Backend:** Python (FastAPI)
*   **AI Engine:** OpenAI `gpt-4o-mini` (Extremely fast, cheap, and great at function calling)
*   **Orchestration:** `LangChain` (Makes building AI tools very easy)

---

## 3. Step-by-Step Implementation

### Step 1: Install Dependencies
First, install the required libraries in your Python environment:
```bash
pip install fastapi langchain langchain-openai python-dotenv uvicorn
```

### Step 2: Define Your Database "Tools"
We need to create Python functions that actually query your SQL database. We use the `@tool` decorator from LangChain to tell the AI that it is allowed to use these functions.

Create a file named `chatbot_tools.py`:
```python
from langchain.tools import tool
import json

# In reality, you would connect to your PostgreSQL/MySQL database here.
# For this example, we are using mock data.

@tool
def get_upcoming_exams(school_id: str, class_id: str) -> str:
    """Fetches the upcoming exam schedule for a specific school and class."""
    
    # SECURITY: The AI passes the school_id it received from the backend, 
    # ensuring it only queries data for this specific school.
    
    # Mock SQL Query: 
    # SELECT * FROM exams WHERE school_id = school_id AND class_id = class_id AND date > NOW()
    
    mock_db = {
        "school_123": {
            "class_10": "Math Exam on Oct 15th, Science Exam on Oct 18th"
        }
    }
    
    # Return the data as a string to the AI
    return mock_db.get(school_id, {}).get(class_id, "No upcoming exams found.")

@tool
def get_current_holidays(school_id: str) -> str:
    """Fetches the holiday list for the current month for a specific school."""
    
    # Mock SQL Query: SELECT * FROM holidays WHERE school_id = school_id AND month = CURRENT_MONTH
    
    mock_db = {
        "school_123": "Diwali break from Oct 24th to Oct 28th."
    }
    
    return mock_db.get(school_id, "No holidays scheduled for this month.")

@tool
def get_student_results(school_id: str, student_id: str) -> str:
    """Fetches the latest result summary for a specific student."""
    
    mock_db = {
        "student_777": "Term 1 Results: Math A+, Science B, English A"
    }
    
    return mock_db.get(student_id, "Results not yet published.")
```

### Step 3: Create the AI Agent
Now, we create the AI brain, give it the tools we just made, and give it a **System Prompt** so it knows its personality.

Create a file named `agent.py`:
```python
import os
from langchain_openai import ChatOpenAI
from langchain.agents import create_tool_calling_agent, AgentExecutor
from langchain_core.prompts import ChatPromptTemplate
from chatbot_tools import get_upcoming_exams, get_current_holidays, get_student_results

# 1. Initialize the LLM (Requires OPENAI_API_KEY in your .env file)
llm = ChatOpenAI(model="gpt-4o-mini", temperature=0)

# 2. List the tools the AI is allowed to use
tools = [get_upcoming_exams, get_current_holidays, get_student_results]

# 3. Create the System Prompt
prompt = ChatPromptTemplate.from_messages([
    ("system", """You are a helpful and polite virtual assistant for the Kaksha+ School ERP system.
    You answer questions from parents and students. 
    You MUST always use your provided tools to look up real-time information. 
    Do not make up dates or grades. If a tool returns no data, politely inform the user."""),
    ("human", "{input}"),
    ("placeholder", "{agent_scratchpad}"),
])

# 4. Build the Agent
agent = create_tool_calling_agent(llm, tools, prompt)
agent_executor = AgentExecutor(agent=agent, tools=tools, verbose=True)

def chat_with_kaksha(user_message: str, school_id: str, class_id: str, student_id: str):
    # INVISIBLE CONTEXT: We secretly inject the user's IDs into their message 
    # so the AI knows exactly what to pass into the Tools.
    
    contextualized_message = f"""
    The user asked: "{user_message}"
    
    For context, the user belongs to:
    school_id: {school_id}
    class_id: {class_id}
    student_id: {student_id}
    """
    
    response = agent_executor.invoke({"input": contextualized_message})
    return response["output"]
```

### Step 4: Build the API Endpoint (FastAPI)
Finally, we expose this agent as an API endpoint so your Flutter/Web app can talk to it.

Create `main.py`:
```python
from fastapi import FastAPI, Depends, Header
from pydantic import BaseModel
from agent import chat_with_kaksha

app = FastAPI()

class ChatRequest(BaseModel):
    message: str

# In a real app, you would extract school_id and student_id from the JWT Authorization token
# Here, we pass them as headers for simplicity
@app.post("/api/chatbot/ask")
async def ask_chatbot(
    request: ChatRequest, 
    x_school_id: str = Header(...),
    x_class_id: str = Header(...),
    x_student_id: str = Header(...)
):
    try:
        # Pass the message AND the secure context to the AI
        ai_response = chat_with_kaksha(
            user_message=request.message,
            school_id=x_school_id,
            class_id=x_class_id,
            student_id=x_student_id
        )
        
        return {"reply": ai_response}
        
    except Exception as e:
        return {"error": str(e)}

# Run this server using: uvicorn main:app --reload
```

---

## 4. How it looks in action!

1. A parent logs into the Kaksha+ app. The app knows their token contains `school_123` and `student_777`.
2. The parent types: *"How did my child do in the exams?"*
3. The Flutter app sends an HTTP POST request to `/api/chatbot/ask` with the message and the headers.
4. The AI receives the message. It realizes it needs results, so it autonomously calls `get_student_results(school_id="school_123", student_id="student_777")`.
5. The tool queries the database and returns: `"Term 1 Results: Math A+, Science B, English A"`.
6. The AI formulates a friendly response: *"I found the results! Your child scored an A+ in Math, a B in Science, and an A in English. Let me know if you need anything else!"*
