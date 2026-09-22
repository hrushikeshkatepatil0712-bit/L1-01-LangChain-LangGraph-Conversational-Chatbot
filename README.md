# langchain-langgraph-conversational-chatbot

# 🤖 LangChain + LangGraph Conversational Chatbot

A production-style conversational chatbot built with **LangChain**, **LangGraph**, and **Google Gemini 2.5 Flash**. The project demonstrates LLM integration, message-based conversation, state management, persistent conversation memory using thread IDs, and LangGraph workflow visualization.

---

## 📌 Project Overview

This project implements a simple but modular **conversational AI chatbot** using the LangChain and LangGraph ecosystem.

The chatbot uses **Google Gemini 2.5 Flash** as the Large Language Model (LLM) and **LangGraph** to define and execute the conversation workflow.

The project demonstrates how an AI application can:

* Integrate an LLM using LangChain
* Handle human and AI messages
* Maintain multi-turn conversations
* Store conversation state using `MemorySaver`
* Manage independent conversations using `thread_id`
* Build a graph-based LLM workflow with LangGraph
* Visualize the LangGraph workflow
* Load API keys securely using environment variables

---

## 🚀 Key Features

### 1. Google Gemini LLM Integration

The chatbot uses:

**Google Gemini 2.5 Flash**

through LangChain's `ChatGoogleGenerativeAI` integration.

```python
model = ChatGoogleGenerativeAI(
    model="gemini-2.5-flash",
    temperature=0
)
```

---

### 2. LangChain Message Handling

The project uses LangChain's message classes:

* `HumanMessage`
* `AIMessage`

This allows the application to represent conversations as structured messages.

Example:

```python
[
    HumanMessage(content="I am Rushi"),
    AIMessage(content="Hello, Rushi! How can I assist you today"),
    HumanMessage(content="What is my name")
]
```

---

### 3. Multi-Turn Conversation

The chatbot supports conversational context across multiple messages.

For example:

```text
User: I am Rushi
AI: Hello, Rushi! How can I assist you today.

User: What is my name?
AI: Your name is Rushi.
```

The conversation history is maintained through LangGraph state and checkpointing.

---

### 4. LangGraph Workflow

The chatbot is implemented as a LangGraph workflow.

Architecture:

```text
              ┌─────────────┐
              │    START    │
              └──────┬──────┘
                     │
                     ▼
              ┌─────────────┐
              │  Model Node │
              │ Gemini 2.5  │
              │    Flash    │
              └──────┬──────┘
                     │
                     ▼
              ┌─────────────┐
              │     END     │
              └─────────────┘
```

The workflow contains:

* `START`
* Model processing node
* `END`

---

## 🧠 Conversation Memory

The project uses LangGraph's `MemorySaver` to maintain conversation state.

```python
memory = MemorySaver()

app = workflow.compile(
    checkpointer=memory
)
```

A unique `thread_id` is used to identify each conversation.

```python
config = {
    "configurable": {
        "thread_id": "abc123"
    }
}
```

### Same Thread

When the same `thread_id` is used, the chatbot can access the previous conversation state.

```text
Thread: abc123

User: I am Rushi
AI: Hello, Rushi!

User: What is my name?
AI: Your name is Rushi.
```

### Different Thread

A different `thread_id` represents a separate conversation.

```python
config = {
    "configurable": {
        "thread_id": "abc124"
    }
}
```

This demonstrates how LangGraph can maintain separate conversation states.

---

## 🛠️ Technologies Used

| Technology              | Purpose                         |
| ----------------------- | ------------------------------- |
| Python                  | Programming language            |
| LangChain               | LLM application framework       |
| LangGraph               | Stateful workflow orchestration |
| Google Gemini 2.5 Flash | Large Language Model            |
| LangChain Google GenAI  | Gemini integration              |
| LangChain Core          | Message and model abstractions  |
| MemorySaver             | Conversation checkpointing      |
| python-dotenv           | Environment variable management |
| IPython                 | Graph visualization             |

---

## 📁 Project Structure

```text
langchain-langgraph-conversational-chatbot/
│
├── .env
├── .gitignore
├── README.md
├── requirements.txt
│
└── chatbot.py
```

### File Description

| File               | Description                                               |
| ------------------ | --------------------------------------------------------- |
| `chatbot.py`       | Main chatbot and LangGraph implementation                 |
| `.env`             | Stores API credentials locally                            |
| `.gitignore`       | Prevents sensitive/unnecessary files from being committed |
| `requirements.txt` | Python project dependencies                               |
| `README.md`        | Project documentation                                     |

---

## ⚙️ Installation

### 1. Clone the Repository

```bash
git clone https://github.com/hrushikeshkatepatil0712-bit/langchain-langgraph-conversational-chatbot.git
```

Navigate to the project:

```bash
cd langchain-langgraph-conversational-chatbot
```

---

### 2. Create a Virtual Environment

Windows:

```bash
python -m venv .venv
```

Activate it:

```bash
.venv\Scripts\activate
```

---

### 3. Install Dependencies

```bash
pip install -r requirements.txt
```

---

## 🔑 Environment Variables

Create a `.env` file in the project root.

```env
GOOGLE_API_KEY=your_google_api_key
```

The application loads environment variables using:

```python
from dotenv import load_dotenv

load_dotenv()
```

### ⚠️ Security

Never commit your `.env` file or API keys to GitHub.

Add the following to `.gitignore`:

```gitignore
.env
.venv/
__pycache__/
*.pyc
.ipynb_checkpoints/
```

---

## ▶️ Running the Project

After activating the virtual environment and configuring your API key:

```bash
python chatbot.py
```

The application will execute the conversational workflow and demonstrate memory using different conversation threads.

---

## 💬 Example Conversation

### Thread `abc123`

```text
User: I am Rushi

AI: Hello, Rushi! How can I assist you today?

User: What is my name?

AI: Your name is Rushi.
```

### New Thread `abc124`

```text
User: What is my name?

AI: I don't have your name in this conversation yet.
```

The example demonstrates that conversation memory is associated with the thread.

---

## 🔄 How the Application Works

The application follows this flow:

```text
User Input
    │
    ▼
HumanMessage
    │
    ▼
LangGraph State
    │
    ▼
Model Node
    │
    ▼
Gemini 2.5 Flash
    │
    ▼
AIMessage
    │
    ▼
MemorySaver
    │
    ▼
Conversation State
```

---

## 🧩 Core Implementation

### Define the Graph

```python
from langgraph.graph import StateGraph, START, END, MessagesState

workflow = StateGraph(
    state_schema=MessagesState
)
```

### Create Model Node

```python
def call_model(state: MessagesState):
    response = model.invoke(state["messages"])
    return {
        "messages": response
    }
```

### Add Node and Edges

```python
workflow.add_node(
    "model",
    call_model
)

workflow.add_edge(
    START,
    "model"
)

workflow.add_edge(
    "model",
    END
)
```

### Compile with Memory

```python
from langgraph.checkpoint.memory import MemorySaver

memory = MemorySaver()

app = workflow.compile(
    checkpointer=memory
)
```

### Invoke the Application

```python
config = {
    "configurable": {
        "thread_id": "abc123"
    }
}

output = app.invoke(
    {
        "messages": [
            HumanMessage(content="I am Rushi")
        ]
    },
    config
)
```

---

## 📊 LangGraph Visualization

The project also generates a visual representation of the workflow using Mermaid.

```python
from IPython.display import Image, display

display(
    Image(
        app.get_graph().draw_mermaid_png()
    )
)
```

The resulting graph represents:

```text
START → MODEL → END
```

---

## 🎯 Learning Objectives

This project was developed to understand the fundamentals of building conversational AI applications using modern LLM frameworks.

### Concepts Covered

* Large Language Models
* LangChain
* LangGraph
* Prompt and message handling
* `HumanMessage`
* `AIMessage`
* `MessagesState`
* StateGraph
* Graph nodes and edges
* Conversation memory
* Checkpointing
* Thread-based conversations
* Environment variables
* Gemini API integration
* Workflow visualization

---

## 🔮 Future Improvements

The current project provides the foundation for a more advanced conversational AI system.

Possible improvements include:

* [ ] Streamlit chatbot interface
* [ ] FastAPI backend
* [ ] Persistent database-backed conversation history
* [ ] Conversation summarization
* [ ] Long-term memory
* [ ] Tool calling
* [ ] Web search integration
* [ ] RAG integration
* [ ] Vector database integration
* [ ] Human-in-the-loop workflows
* [ ] LangGraph conditional routing
* [ ] Multiple specialized agents
* [ ] Authentication and user management
* [ ] Docker deployment
* [ ] Cloud deployment

---

## 📈 Project Architecture

```text
                   ┌─────────────────────┐
                   │       User          │
                   └──────────┬──────────┘
                              │
                              ▼
                   ┌─────────────────────┐
                   │   HumanMessage      │
                   └──────────┬──────────┘
                              │
                              ▼
                   ┌─────────────────────┐
                   │     LangGraph       │
                   │    StateGraph       │
                   └──────────┬──────────┘
                              │
                              ▼
                   ┌─────────────────────┐
                   │    Model Node       │
                   │ Gemini 2.5 Flash    │
                   └──────────┬──────────┘
                              │
                              ▼
                   ┌─────────────────────┐
                   │     AIMessage       │
                   └──────────┬──────────┘
                              │
                              ▼
                   ┌─────────────────────┐
                   │    MemorySaver      │
                   │  Thread Checkpoint  │
                   └─────────────────────┘
```

---

## 🔐 API Key Security

This project requires a Google API key.

For security:

* Keep API keys inside `.env`
* Never hard-code API keys in Python files
* Never upload `.env` to GitHub
* Use environment variables for deployment
* Rotate the API key immediately if it is accidentally exposed

---

## 👨‍💻 Author

**Hrushikesh Kate**

B.Tech Computer Engineering (AI & ML)

**Areas of Interest:**

* Artificial Intelligence
* Machine Learning
* Generative AI
* Agentic AI
* LangChain
* LangGraph
* RAG
* AI Automation
* Data Science & Analytics

---

## ⭐ Acknowledgment

This project was created as part of hands-on learning and implementation of modern **LLM orchestration and conversational AI workflows** using LangChain and LangGraph.

---

## 📜 License

This project is intended for educational and learning purposes.
