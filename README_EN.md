# Clarity

<p align="center">
  <strong>Financial Analysis Agent Built on Native Claude-skill Architecture</strong>
</p>

<p align="center">
  English | <a href="./README.md">简体中文</a>
</p>

<p align="center">
  <a href="https://www.cooragent.com/">
    <img src="https://img.shields.io/badge/Powered%20by-Cooragent-blue?style=flat-square" alt="Powered by Cooragent">
  </a>
  <a href="https://github.com/cooragent/Clarity/stargazers">
    <img src="https://img.shields.io/github/stars/cooragent/Clarity?style=flat-square" alt="GitHub Stars">
  </a>
  <a href="https://github.com/cooragent/Clarity/issues">
    <img src="https://img.shields.io/github/issues/cooragent/Clarity?style=flat-square" alt="GitHub Issues">
  </a>
</p>

<p align="center">
  <a href="#features">Features</a> •
  <a href="#quick-start">Quick Start</a> •
  <a href="#usage">Usage</a> •
  <a href="#workflow">Workflow</a> •
  <a href="#architecture">Architecture</a>
</p>

> 🌟 **If you find this project helpful, please give us a Star! Your support keeps us improving!**
> 
> 💡 **Feel free to submit Issues or PRs. We value your feedback and suggestions!**

---

## Introduction

Clarity is a financial analysis agent built on the **native Claude-skill** architecture, supporting stock analysis, holdings tracking, screening strategies, and decision dashboards. Using the **Planning-with-Files** pattern with multi-agent collaboration for complex financial tasks.

### Key Features

- 🧠 **Claude-skill Native Architecture** - Following Anthropic's recommended patterns
- 🌐 **Multi-Market Coverage** - A-shares, HK stocks, US stocks with auto data source switching
- 📊 **6 Specialized Agents** - Fundamentals, technicals, news, sentiment, holdings, screening
- 🔔 **Multi-Channel Push** - WeChat Work, Feishu, Telegram, Email, etc.
- 🚀 **REST API & Web UI** - Complete interface and graphical dashboard

---

## Data Sources

Clarity integrates multiple financial data sources with automatic selection based on market type:

| Data Type | Source | Market Coverage | Notes |
|:----------|:-------|:----------------|:------|
| **A-share Quotes** | AkShare | Shanghai/Shenzhen/STAR/ChiNext | Real-time |
| **A-share Quotes** | EFinance | Shanghai/Shenzhen/STAR/ChiNext | Backup source |
| **Global Quotes** | yFinance | US/HK/A-shares | Worldwide |
| **Financials** | SimFin | US stocks | Financial statements |
| **News** | Finnhub | Global | Company news |
| **News** | Google News | Global | Aggregated news |
| **Social Sentiment** | Reddit | Global | Community discussions |
| **Technical Indicators** | Stockstats | Global | Technical analysis |
| **Web Search** | Serper API | Global | Enhanced search |
| **Content Extraction** | Jina AI | Global | Web parsing |

**Data Source Priority Strategy:**
- **A-shares**: AkShare (primary) → EFinance (backup) → yFinance (fallback)
- **HK stocks**: yFinance
- **US stocks**: yFinance + Finnhub + SimFin

---

## Features

| Feature | Description |
|:--------|:------------|
| **Stock Analysis** | 4-dimension deep analysis: technicals + fundamentals + news + sentiment |
| **Holdings Tracking** | Track Warren Buffett and other famous investors' latest holdings |
| **Stock Screening** | Natural language based stock filtering |
| **Decision Dashboard** | Daily market scan with recommended stocks |
| **Multi-Channel Push** | Auto push reports to WeChat Work, Feishu, Telegram, etc. |

---

## Quick Start

### Installation

```bash
git clone https://github.com/your-org/Clarity.git
cd Clarity

# Using uv (recommended)
uv sync
```

### Configuration

Create a `.env` file:

```bash
# ===== Required =====
OPENAI_API_KEY=your_openai_api_key           # OpenAI API (or compatible)
FINNHUB_API_KEY=your_finnhub_api_key         # Finnhub news (free tier available)

# ===== Optional: Enhanced Search (Recommended) =====
SERPER_API_KEY=your_serper_api_key           # Google Search API
JINA_API_KEY=your_jina_api_key               # Web content extraction

# ===== Optional: Notification Channels =====
WECHAT_WEBHOOK_URL=https://qyapi.weixin.qq.com/...     # WeChat Work
FEISHU_WEBHOOK_URL=https://open.feishu.cn/...          # Feishu/Lark
TELEGRAM_BOT_TOKEN=123456:ABC-DEF...                   # Telegram
TELEGRAM_CHAT_ID=123456789
EMAIL_SENDER=your@gmail.com                            # Email
EMAIL_PASSWORD=app_password

# ===== Optional: Qwen Model (Alibaba Qwen) =====
QWEN_API_KEY=your_dashscope_api_key
QWEN_BASE_URL=https://dashscope.aliyuncs.com/compatible-mode/v1
QWEN_MODEL=qwen-latest
# Usage: uv run run_agent.py --model qwen analyze AAPL
```

---

## Usage

### REST API

Clarity provides a complete RESTful API interface supporting all core functionalities.

```bash
# Start API server
uv run python api.py

# Custom port and host
API_PORT=8000 API_HOST=0.0.0.0 uv run python api.py
```

After the API server starts, visit http://localhost:8000/docs for interactive API documentation.

#### API Endpoints

| Endpoint | Method | Description | Example Payload |
|:---------|:-------|:------------|:----------------|
| `/health` | GET | Health check | - |
| `/api/v1/analyze` | POST | Stock analysis | `{"ticker": "AAPL", "model": "openai"}` |
| `/api/v1/track` | POST | Holdings tracking | `{"investor_name": "Warren Buffett"}` |
| `/api/v1/screen` | POST | Stock screening | `{"criteria": "high dividend yield"}` |
| `/api/v1/ask` | POST | Natural language query | `{"query": "analyze Apple stock"}` |
| `/api/v1/dashboard` | POST | Decision dashboard | `{"markets": ["A股"], "top_n": 10}` |
| `/api/v1/notification/channels` | GET | Get notification channels | - |

#### Usage Examples

```bash
# Analyze a stock
curl -X POST "http://localhost:8000/api/v1/analyze" \
  -H "Content-Type: application/json" \
  -d '{"ticker": "AAPL", "model": "openai"}'

# Track investor holdings
curl -X POST "http://localhost:8000/api/v1/track" \
  -H "Content-Type: application/json" \
  -d '{"investor_name": "Warren Buffett"}'

# Dashboard with push notification
curl -X POST "http://localhost:8000/api/v1/dashboard" \
  -H "Content-Type: application/json" \
  -d '{"markets": ["A股", "美股"], "top_n": 10, "push": true}'
```

#### Python Client Example

```python
import httpx
import asyncio

async def analyze_stock(ticker: str):
    async with httpx.AsyncClient() as client:
        response = await client.post(
            "http://localhost:8000/api/v1/analyze",
            json={"ticker": ticker, "model": "openai"}
        )
        return response.json()

# Run
result = asyncio.run(analyze_stock("AAPL"))
print(result["report"])
```

### Web UI

```bash
# activate env
source .venv/bin/activate

# Start Web interface
uv run python webui.py

# Create a public link (via Gradio Share)
uv run python webui.py --share
```

<video src="https://github.com/user-attachments/assets/678ece2c-2fd9-4214-8470-22e401647e4b" controls width="800"></video>

Visit http://localhost:7860 to use the graphical interface.

### CLI Commands

```bash
# Analyze a stock
uv run python run_agent.py analyze AAPL
uv run python run_agent.py analyze NVDA --date 2025-01-15
uv run python run_agent.py --model qwen analyze AAPL

# Track investor holdings
uv run python run_agent.py track "Warren Buffett"

# Screen stocks
uv run python run_agent.py screen "high dividend yield tech stocks"

# Natural language query
uv run python run_agent.py ask "analyze Apple stock"

# Decision dashboard
uv run python run_agent.py dashboard                           # Scan A-shares + US stocks
uv run python run_agent.py dashboard -m A股 港股              # Scan specific markets
uv run python run_agent.py dashboard -n 20 -o report.md       # Top 20, save to file
uv run python run_agent.py dashboard --push                   # Scan and push notifications
uv run python run_agent.py dashboard -p --push-to telegram    # Push to Telegram only
uv run python run_agent.py dashboard --interval 30            # Run every 30 minutes
uv run python run_agent.py dashboard -i 60 --push             # Run hourly and push
```

### Python Code

```python
import asyncio
from clarity import FinancialAgentOrchestrator, AgentConfig, TaskType

async def main():
    orchestrator = FinancialAgentOrchestrator()

    # Analyze a stock
    result = await orchestrator.run(
        task_type=TaskType.STOCK_ANALYSIS,
        target="AAPL",
    )
    print(result["report"])

asyncio.run(main())
```

```python
# Using notification service
from clarity.core import NotificationService

notification = NotificationService()
notification.send("# Test Report\nThis is a Markdown message")
```

---

## How It Works

### Planning-with-Files Pattern

The system uses three persistent files to manage long tasks, solving LLM "forgetting" problem:

| File | Purpose |
|:-----|:--------|
| `task_plan.md` | Task plan, phase status, agent assignments |
| `findings.md` | Research findings, API data, analysis results |
| `progress.md` | Execution logs, error records, retry tracking |

**Execution Flow:** MasterAgent plans → WorkingAgent executes → SubAgents work → StateChecker validates → Generate report

---

## Architecture

### Core Agents

| Agent | Responsibility |
|:------|:--------------|
| **MasterAgent** | Task planning, result synthesis |
| **WorkingAgent** | Execution coordination, flow control |
| **StateChecker** | State validation, error retry |
| **Fundamentals Analyst** | Financial statements, fundamentals |
| **Technical Analyst** | Technical indicators (MACD, RSI, Bollinger) |
| **News Analyst** | News collection & sentiment analysis |
| **Sentiment Analyst** | Social media sentiment monitoring |
| **Holdings Hunter** | Institutional holdings tracking (SEC 13F) |
| **Alpha Hound** | Stock screening & scoring |

### Directory Structure

```
Clarity/
├── api.py               # REST API server
├── webui.py             # Gradio Web interface
├── run_agent.py         # CLI command tool
└── clarity/
    ├── core/            # Core agents & tools
    └── dataflows/       # Data source integrations
```

---

## Support & Contributing

This project is supported by cooragent team. Cooragent is an AI agent platform , dedicated to making everyone a commander of AI agents, which adapt, evolve, and stay aligned with users.

### 🌟 Support Us

If you find this project helpful:

- ⭐ **Star the project** - This is the best encouragement for us!
- 🐛 **Submit Issues** - Report bugs or suggest features
- 🔀 **Submit PRs** - Contributions to code and documentation are welcome
- 💬 **Join the community** - Share your experience with other users

### 📮 Contact Us

- 🌐 FeiShu: [cooragent](https://applink.feishu.cn/client/chat/chatter/add_by_link?link_token=251mf86f-8106-4361-81aa-05fa856abc05)
- 📧 Feedback: Please submit via [GitHub Issues](https://github.com/cooragent/Clarity/issues)

---


## Star History

<a href="https://star-history.com/#cooragent/Clarity&Date">
 <picture>
   <source media="(prefers-color-scheme: dark)" srcset="https://api.star-history.com/svg?repos=cooragent/Clarity&type=Date&theme=dark" />
   <source media="(prefers-color-scheme: light)" srcset="https://api.star-history.com/svg?repos=cooragent/Clarity&type=Date" />
   <img alt="Star History Chart" src="https://api.star-history.com/svg?repos=cooragent/Clarity&type=Date" />
 </picture>
</a>
