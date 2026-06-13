# Kaksha+ ERP AI Chatbot: Microservice API Approach

In enterprise environments, it is often a major security risk (and a bad architectural practice) to give a new AI Python service direct access to the main production SQL database.

Instead, the **AI Layer should act as an independent Microservice**. When the AI needs data (like exams or holidays), it should make standard HTTP REST API calls to your main backend (written in .NET, Java, Node.js, etc.), exactly like your Flutter app does!

This guide explains how to build the AI Chatbot using the **API-Driven Tool Calling** approach.

---

## 1. The Architecture (Microservice Flow)

1.  **The User Asks:** "When is the math exam?"
2.  **The Interceptor (Python):** The Python AI service receives the message along with the user's secure **JWT Authorization Token** from the frontend.
3.  **The AI Thinks:** The LLM realizes it needs to look up the exam schedule.
4.  **The Tool Call:** The LLM triggers our predefined Python tool: `fetch_upcoming_exams()`.
5.  **The API Request:** Instead of running SQL, our Python tool makes an HTTP `GET` request to your main Java/.NET backend (e.g., `https://api.kakshaplus.com/v1/exams`). It attaches the user's JWT token to the request.
6.  **The Validation:** Your main Java/.NET backend validates the token, runs its normal business logic, and returns the JSON data to Python.
7.  **The Answer:** The LLM reads the JSON, formats it into a friendly sentence, and replies to the user.

---

## 2. The Tech Stack
*   **Main Backend:** .NET / Java Spring Boot / Node.js (Handles Database & Auth)
*   **AI Microservice:** Python (FastAPI)
*   **AI Engine:** OpenAI `gpt-4o-mini`
*   **Orchestration:** `LangChain` + `requests` (for HTTP calls)

---

## 3. Step-by-Step Implementation

### Step 1: Install Dependencies
```bash
pip install fastapi langchain langchain-openai python-dotenv uvicorn requests
```

### Step 2: Define Your "API Tools"
Instead of writing SQL queries, our tools will use the `requests` library to call your existing backend endpoints. 

Create a file named `api_tools.py`:
```python
import requests
from langchain.tools import tool

# We use a global variable to store the token temporarily during the request cycle.
# In a production environment, you would use ContextVars (from the 'contextvars' module)
# to ensure thread safety in FastAPI.
CURRENT_USER_TOKEN = ""
MAIN_BACKEND_URL = "https://api.kakshaplus.com/v1"

@tool
def get_upcoming_exams() -> str:
    """Fetches the upcoming exam schedule for the current student/class."""
    
    headers = {"Authorization": f"Bearer {CURRENT_USER_TOKEN}"}
    
    try:
        # The Python AI Service calls your main .NET/Java backend
        response = requests.get(f"{MAIN_BACKEND_URL}/students/me/exams", headers=headers)
        
        if response.status_code == 200:
            # Return the JSON response as a string to the AI
            return str(response.json())
        else:
            return f"Failed to fetch exams. Backend returned status code: {response.status_code}"
            
    except Exception as e:
        return f"Error contacting the main server: {str(e)}"

@tool
def get_current_holidays() -> str:
    """Fetches the holiday list for the current month."""
    
    headers = {"Authorization": f"Bearer {CURRENT_USER_TOKEN}"}
    
    try:
        response = requests.get(f"{MAIN_BACKEND_URL}/school/holidays/current-month", headers=headers)
        if response.status_code == 200:
            return str(response.json())
        return "No holidays found."
    except Exception as e:
        return "Error contacting the main server."

@tool
def get_student_results() -> str:
    """Fetches the latest exam results and grades for the student."""
    
    headers = {"Authorization": f"Bearer {CURRENT_USER_TOKEN}"}
    
    try:
        response = requests.get(f"{MAIN_BACKEND_URL}/students/me/results", headers=headers)
        if response.status_code == 200:
            return str(response.json())
        return "Results not available."
    except Exception as e:
        return "Error contacting the main server."
```

### Step 3: Create the AI Agent
This remains almost identical to the database approach, but we pass the API-based tools.

Create a file named `agent.py`:
```python
import os
import api_tools # Import our API tool module
from langchain_openai import ChatOpenAI
from langchain.agents import create_tool_calling_agent, AgentExecutor
from langchain_core.prompts import ChatPromptTemplate

llm = ChatOpenAI(model="gpt-4o-mini", temperature=0)

tools = [api_tools.get_upcoming_exams, api_tools.get_current_holidays, api_tools.get_student_results]

prompt = ChatPromptTemplate.from_messages([
    ("system", """You are a helpful virtual assistant for Kaksha+ ERP.
    You answer questions from parents and students. 
    You MUST use your provided tools to look up real-time information. 
    The tools return JSON data from the main server. Parse the JSON and explain it in a friendly, conversational tone."""),
    ("human", "{input}"),
    ("placeholder", "{agent_scratchpad}"),
])

agent = create_tool_calling_agent(llm, tools, prompt)
agent_executor = AgentExecutor(agent=agent, tools=tools, verbose=True)

def chat_with_kaksha(user_message: str, auth_token: str):
    # Set the token so the tools can use it for the HTTP requests
    api_tools.CURRENT_USER_TOKEN = auth_token
    
    response = agent_executor.invoke({"input": user_message})
    return response["output"]
```

### Step 4: Build the API Endpoint (FastAPI)
Now, the FastAPI endpoint doesn't need to accept `school_id` or `student_id` directly. It only needs the `Authorization` token, which it blindly passes to the Java/.NET backend for validation!

Create `main.py`:
```python
from fastapi import FastAPI, Depends, Header, HTTPException
from pydantic import BaseModel
from agent import chat_with_kaksha

app = FastAPI()

class ChatRequest(BaseModel):
    message: str

@app.post("/api/chatbot/ask")
async def ask_chatbot(
    request: ChatRequest, 
    authorization: str = Header(None) # Extract the "Bearer eyJhbGci..." token
):
    if not authorization:
        raise HTTPException(status_code=401, detail="Missing Authorization token")
        
    # Remove the "Bearer " prefix if present
    token = authorization.replace("Bearer ", "")
    
    try:
        # Pass the message AND the user's token to the AI
        ai_response = chat_with_kaksha(
            user_message=request.message,
            auth_token=token
        )
        
        return {"reply": ai_response}
        
    except Exception as e:
        return {"error": str(e)}
```

---

## 4. Why this Architecture is Superior for Enterprise

1.  **Zero Database Risk:** The Python AI service does not possess database credentials. It is physically impossible for the AI to accidentally drop a table or run a malicious SQL injection.
2.  **Centralized Security:** Your main .NET/Java backend is already an expert at validating tokens, checking roles, and restricting data based on `school_id`. By forcing the AI to call those APIs, you guarantee that a user from School A can *never* fetch data from School B, because the main backend will reject their token.
3.  **No Duplicate Logic:** You don't have to rewrite your business logic (like calculating GPAs or handling timezone logic) in Python. The AI just consumes the exact same APIs that your Flutter app uses!
