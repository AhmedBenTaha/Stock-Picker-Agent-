# 📈 Stock Picker Agent

An AI-powered stock research and selection system built with **CrewAI**, **Groq**, and **Serper**.

The system uses multiple specialized AI agents to discover trending companies, research them, and select a company based on the generated financial research.

---

## 🚀 Overview

Stock Picker Agent is a multi-agent financial research workflow that automates the process of:

1. Finding companies currently trending in financial news.
2. Performing detailed research on those companies.
3. Analyzing their market position and future outlook.
4. Selecting a company based on the research.
5. Sending a push notification with the final decision.

The project uses a **hierarchical CrewAI architecture**, where a Manager Agent delegates tasks to specialized agents.

---

## 🏗️ Architecture

```text
                         ┌─────────────────────┐
                         │    Manager Agent    │
                         │                     │
                         │  Task Delegation    │
                         └──────────┬──────────┘
                                    │
                   ┌────────────────┴────────────────┐
                   │                                 │
                   ▼                                 ▼
        ┌──────────────────────┐          ┌──────────────────────┐
        │ Trending Company     │          │ Financial Researcher │
        │ Finder               │          │                      │
        │                      │          │ • Market Position   │
        │ • Latest News        │          │ • Future Outlook    │
        │ • Trending Companies │          │ • Investment        │
        └──────────┬───────────┘          │   Potential         │
                   │                      └──────────┬───────────┘
                   │                                 │
                   └────────────────┬────────────────┘
                                    │
                                    ▼
                         ┌─────────────────────┐
                         │    Stock Picker     │
                         │                     │
                         │ • Analyze Research  │
                         │ • Select Company    │
                         │ • Notify User       │
                         └─────────────────────┘
```

---

## 🤖 Agents

### 1. Trending Company Finder

Responsible for discovering companies that are currently trending in the news.

**Responsibilities:**

- Search the latest financial news.
- Identify 2–3 trending companies.
- Return company names and ticker symbols.
- Explain why each company is trending.

**Tools & Technologies:**

- Serper Search
- Groq LLM
- Pydantic structured output

---

### 2. Financial Researcher

Performs deeper research on the companies discovered by the first agent.

**Responsibilities:**

- Analyze current market position.
- Research competitive landscape.
- Evaluate future outlook.
- Analyze investment potential.

**Tools & Technologies:**

- Serper Search
- Groq LLM
- Pydantic structured output

---

### 3. Stock Picker

Analyzes the research produced by the Financial Researcher.

**Responsibilities:**

- Compare researched companies.
- Select a company based on the research.
- Explain the decision.
- Identify companies that were not selected.
- Send a push notification to the user.

**Tools & Technologies:**

- Groq LLM
- Push notification tool

---

### 4. Manager Agent

The Manager controls the hierarchical workflow.

**Responsibilities:**

- Understand the overall objective.
- Delegate work to the appropriate agents.
- Coordinate the different stages of the workflow.
- Manage execution of the crew.

The project uses:

```python
Process.hierarchical
```

with:

```python
allow_delegation=True
```

---

## 🔄 Workflow

The complete workflow is:

```text
User
  │
  ▼
Technology Sector
  │
  ▼
Manager Agent
  │
  ▼
Find Trending Companies
  │
  ▼
Trending Company List
  │
  ▼
Financial Research
  │
  ▼
Research Report
  │
  ▼
Stock Selection
  │
  ▼
Push Notification
  │
  ▼
Final Decision
```

---

## 🧠 Structured Outputs

The project uses **Pydantic models** to enforce structured responses.

### Trending Companies

```python
class TrendingCompany(BaseModel):
    name: str
    ticker: str
    reason: str
```

The companies are returned as:

```python
class TrendingCompanyList(BaseModel):
    companies: list[TrendingCompany]
```

### Company Research

```python
class TrendingCompanyResearch(BaseModel):
    name: str
    market_position: str
    future_outlook: str
    investment_potential: str
```

The complete research output is:

```python
class TrendingCompanyResearchList(BaseModel):
    research_list: list[TrendingCompanyResearch]
```

This makes communication between agents more predictable and easier to process programmatically.

---

## 🛠️ Tech Stack

| Technology | Purpose |
|---|---|
| Python 3.12 | Main programming language |
| CrewAI | Multi-agent orchestration |
| CrewAI Tools | Agent tooling |
| Groq | LLM inference |
| GPT-OSS-120B | Main reasoning model |
| Serper | Web search |
| Pydantic | Structured outputs |
| LiteLLM | LLM abstraction |
| LanceDB | Vector database dependency |
| ONNX Runtime | Model/runtime dependency |
| uv | Python package and environment management |

---

## 📁 Project Structure

```text
stock_picker/
│
├── src/
│   └── stock_picker/
│       │
│       ├── main.py
│       ├── crew.py
│       │
│       ├── config/
│       │   ├── agents.yaml
│       │   └── tasks.yaml
│       │
│       └── tools/
│           └── push_tool.py
│
├── output/
│   ├── trending_companies.json
│   ├── research_report.json
│   └── decision.md
│
├── pyproject.toml
├── uv.lock
└── README.md
```

---

## ⚙️ Installation

### 1. Clone the repository

```bash
git clone <YOUR_REPOSITORY_URL>
cd stock_picker
```

### 2. Install dependencies

The project uses Python 3.12.

```bash
uv sync
```

### 3. Activate the environment

```bash
source .venv/bin/activate
```

---

## 🔐 Environment Variables

Create a `.env` file in the project root:

```env
GROQ_API_KEY=your_groq_api_key
```

Add any additional credentials required by your push notification tool.

### API Keys

The project requires:

- **Groq API** — LLM inference
- **Serper API** — web search

Never commit your `.env` file or API keys to GitHub.

---

## ▶️ Running the Project

Run the complete CrewAI workflow:

```bash
crewai run
```

Or:

```bash
uv run crewai run
```

The project will execute the agents and generate the results inside the `output/` directory.

---

## 📄 Output

### Trending Companies

Generated in:

```text
output/trending_companies.json
```

Example:

```json
{
  "companies": [
    {
      "name": "Company Name",
      "ticker": "TICK",
      "reason": "Why the company is trending"
    }
  ]
}
```

### Research Report

Generated in:

```text
output/research_report.json
```

Contains:

- Company
- Market position
- Competitive analysis
- Future outlook
- Investment potential

### Final Decision

Generated in:

```text
output/decision.md
```

Contains the final company selection and supporting analysis.

---

## 🔔 Push Notifications

The Stock Picker Agent can send a notification after reaching the final selection.

The notification tool is located at:

```text
src/stock_picker/tools/push_tool.py
```

The Stock Picker agent calls this tool after analyzing the research.

---

## 🧩 CrewAI Configuration

Agents are configured in:

```text
src/stock_picker/config/agents.yaml
```

Tasks are configured in:

```text
src/stock_picker/config/tasks.yaml
```

Example agent configuration:

```yaml
trending_company_finder:
  role: >
    Financial News Analyst that finds trending companies in {sector}
    as of {current_date}

  goal: >
    Find 2-3 companies that are trending in the news.

  llm: groq/openai/gpt-oss-120b
```

---

## 🧪 Testing

Run the project tests with:

```bash
uv run pytest
```

For a quick dependency check:

```bash
uv run python -c "from crewai import Agent; from crewai_tools import SerperDevTool; import lancedb; print('CrewAI + Tools + LanceDB OK')"
```

---

## ⚠️ Rate Limits

The workflow can generate multiple LLM requests because it uses:

- A Manager Agent
- Multiple specialized agents
- Hierarchical delegation
- Structured outputs
- Tool calls

If Groq returns:

```text
429 Too Many Requests
```

the organization may have exceeded the current model's token-per-minute or request limit.

Possible solutions:

- Wait for the rate-limit window to reset.
- Reduce unnecessary agent iterations.
- Reduce prompt/output size.
- Use a smaller model where appropriate.
- Avoid unnecessary retries.
- Disable memory when it is not required.

---

## 🔒 Design Considerations

### Separation of Responsibilities

Each agent has a specific responsibility instead of having one agent perform the entire workflow.

### Structured Data

Pydantic models are used to make agent outputs predictable.

### Tool-Augmented Agents

Agents can access external information through search and notification tools.

### Hierarchical Orchestration

A Manager Agent coordinates the workflow and delegates tasks to specialized agents.

### Up-to-Date Research

The research agents use web search to retrieve current information rather than relying only on the model's internal knowledge.

---

## 🚧 Current Limitations

The project is currently a learning and portfolio implementation with several limitations:

- Financial information depends on external search results.
- LLM outputs require validation.
- API rate limits can affect execution.
- Search results may contain incomplete or conflicting information.
- The system does not guarantee investment performance.
- AI-generated analysis should not be treated as professional financial advice.

---

## 🔮 Future Improvements

Potential improvements include:

- Real-time market data integration.
- Stock price and financial statement APIs.
- Financial metrics such as P/E, EPS, revenue growth, and margins.
- Historical performance analysis.
- Portfolio tracking.
- Risk scoring.
- Backtesting.
- More robust source verification.
- Agent evaluation and observability.
- Better retry and rate-limit handling.
- Persistent vector-based financial memory.
- Automated scheduled stock research.

---

## 🎯 Learning Goals

This project demonstrates practical implementation of:

- Multi-agent systems
- CrewAI
- Hierarchical agents
- Agent delegation
- Tool calling
- Web search
- Structured LLM outputs
- Pydantic validation
- LLM orchestration
- API integration
- Agent-to-agent workflows
- Automated financial research

---

## 👨‍💻 Author

**Ahmed Elsayed Taha**

AI Engineer | LLM Engineer

- GitHub: [AhmedBenTaha](https://github.com/AhmedBenTaha)
- LinkedIn: [Ahmed Taha](https://www.linkedin.com/in/ahmedtaha26/)
- Hugging Face: [ApexVOrteX-1](https://huggingface.co/ApexVOrteX-1)
