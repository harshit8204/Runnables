# 🔗 LangChain Runnables

> A hands-on collection of LangChain Runnable patterns — covering sequential chains, parallel execution, passthrough, tool calling, and custom tools — built with Mistral AI.

---

## 🔍 What Is This?

This repository is a practical implementation of **LangChain's Runnable interface** — the core building block behind every LangChain chain. Each file isolates one specific concept, making it easy to understand how chains are composed, how data flows through them, and how tools integrate with LLMs.

---

## 📁 Project Structure

```
runnables/
├── sequencerunnable.py      # Basic sequential chain using pipe operator
├── parallelrunnable.py      # Run multiple chains in parallel simultaneously
├── runnablepassthrough.py   # Pass original input alongside transformed output
├── toolcalling.py           # Bind a tool to LLM and handle tool call response
├── owntool.py               # Create a custom tool using @tool decorator
├── newssummarizer.py        # Real-world chain: fetch news → summarize with LLM
└── requirements.txt
```

---

## 🧠 Concepts Covered

### 1. `sequencerunnable.py` — Sequential Chain
The simplest chain pattern using the `|` pipe operator:
```python
chain = prompt | model | parser
result = chain.invoke("Machine Learning")
```
`Prompt → Mistral AI → StrOutputParser` — data flows left to right through each component.

---

### 2. `parallelrunnable.py` — Parallel Execution
Run two different chains simultaneously using `RunnableParallel`:
```python
chain = RunnableParallel({
    "short":    short_prompt   | model | parser,
    "detailed": detailed_prompt | model | parser
})
```
Both chains execute at the same time — useful when you need multiple perspectives or formats from the same input without waiting for one to finish before starting the other.

---

### 3. `runnablepassthrough.py` — Passthrough + Chaining
Generate code from a prompt, then pass that code into a second chain that explains it — using `RunnablePassthrough` to carry the intermediate output forward:
```python
seq2 = RunnableParallel({
    "code":        RunnablePassthrough(),
    "explanation": explain_prompt | model | parser
})
chain = seq | seq2
```
Result contains both the generated code and its plain-English explanation.

---

### 4. `toolcalling.py` — Tool Binding and Invocation
Bind a custom tool to an LLM, let the model decide when to call it, then process the tool result back through the LLM:
```python
llm_with_tool = llm.bind_tools([get_text_length])
result = llm_with_tool.invoke(message)

if result.tool_calls:
    tool_message = tools[tool_name].invoke(result.tool_calls[0])
```
Demonstrates the full tool calling loop: LLM decides → tool executes → LLM responds with result.

---

### 5. `owntool.py` — Custom Tool with `@tool` Decorator
Create a LangChain-compatible tool from any Python function using the `@tool` decorator:
```python
@tool
def get_greeting(name: str) -> str:
    """Generate a greeting message for a user"""
    return f"Hello {name}, Welcome to the AI world"
```
The docstring becomes the tool's description — what the LLM reads to decide whether to use it.

---

### 6. `newssummarizer.py` — Real-World Chain
A complete end-to-end chain that searches the web for live news and summarizes it into bullet points:
```python
chain = prompt | llm | StrOutputParser()
news_result = search_tool.run("Latest AI news of 2026")
result = chain.invoke({"news": news_result})
```
Uses Tavily Search to fetch real-time results, then passes them through a summarization prompt.

---

## 🛠️ Tech Stack

| Component | Technology |
|---|---|
| LLM | Mistral AI (`mistral-small-2603`, `mistral-small-2506`) |
| Framework | LangChain Core |
| News Search | Tavily Search API |
| Output Parsing | `StrOutputParser` |
| Runnables | `RunnableParallel`, `RunnablePassthrough`, `RunnableLambda` |
| Language | Python 3.10+ |

---

## 🚀 Getting Started

### 1. Clone the Repository

```bash
git clone https://github.com/harshit8204/Runnables.git
cd Runnables/runnables
```

### 2. Create a Virtual Environment

```bash
python -m venv venv
source venv/bin/activate        # Windows: venv\Scripts\activate
```

### 3. Install Dependencies

```bash
pip install -r requirements.txt
pip install tavily-python rich
```

### 4. Set Up Environment Variables

Create a `.env` file in the `runnables/` folder:

```env
MISTRAL_API_KEY=your_mistral_api_key_here
TAVILY_API_KEY=your_tavily_api_key_here
```

---

## ▶️ Run Each Script

```bash
# Basic sequential chain
python sequencerunnable.py

# Parallel chains
python parallelrunnable.py

# Passthrough + code explainer
python runnablepassthrough.py

# Tool calling loop
python toolcalling.py

# Custom tool demo
python owntool.py

# Live news summarizer
python newssummarizer.py
```

---

## 📦 Core Dependencies

```
langchain
langchain-core
langchain-mistralai
langchain-community
python-dotenv
tavily-python
rich
```

---

## 👤 Author

**Harshit Pal** — [@harshit8204](https://github.com/harshit8204)
