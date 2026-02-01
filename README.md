# Deep Research Agent Pipeline

An automated research pipeline built with the OpenAI Agent SDK that takes a query, performs comprehensive web research, synthesizes findings into a detailed report, and delivers it via email.

## Overview

This project demonstrates an **orchestrated sequential pipeline** of specialized AI agents working together to conduct deep research on any topic. The system automatically plans searches, gathers information from the web, writes a comprehensive report, and emails the results.

## Architecture

The pipeline follows a **fixed orchestration pattern** with four specialized agents coordinated by a central manager:

```
User Query
    ↓
Planner Agent → generates search strategy (5 search terms)
    ↓
Search Agent × N → performs parallel web searches & summarizes results
    ↓
Writer Agent → synthesizes findings into detailed markdown report
    ↓
Email Agent → formats & sends report via email
    ↓
Done
```

## Components

### 1. `research_manager.py`
**Central orchestrator** that coordinates the entire pipeline.
- Manages the sequential workflow
- Handles parallel search execution with `asyncio`
- Provides progress updates via async generator
- Includes OpenAI trace logging for debugging

### 2. `planner_agent.py`
**Planning specialist** that designs the research strategy.
- Analyzes the user query
- Generates 5 targeted web search terms
- Provides reasoning for each search
- Output: `WebSearchPlan` (structured list of searches)

### 3. `search_agent.py`
**Research specialist** that gathers information.
- Performs web searches using OpenAI's `WebSearchTool`
- Summarizes results into concise 2-3 paragraph summaries (<300 words)
- Focuses on key facts, ignoring fluff
- Runs in parallel for all planned searches

### 4. `writer_agent.py`
**Synthesis specialist** that creates the final report.
- Takes original query + all search summaries
- Creates an outline and structured report
- Generates detailed markdown report (5-10 pages, 1000+ words)
- Output: `ReportData` with summary, full report, and follow-up questions

### 5. `email_agent.py`
**Delivery specialist** that sends the report.
- Converts markdown report to clean HTML
- Sends via SendGrid API
- Generates appropriate subject line

### 6. `deep_research.py`
**Web UI entry point** for running the pipeline in the browser.
- Gradio interface with a textbox for the query and a Run button
- Streams progress updates and the final report as they are produced
- Launches in the browser; run with `python deep_research.py`

## Workflow Paradigm

This system uses an **orchestrated sequential pipeline** approach:
- **Fixed sequence**: Always runs planner → search → writer → email
- **Central control**: `ResearchManager` explicitly coordinates all agents
- **No routing**: Flow doesn't branch based on input type
- **Parallel execution**: Search agents run concurrently for efficiency

## Key Features

- ✅ **Automated research planning** - AI determines optimal search strategy
- ✅ **Parallel web searches** - Multiple searches run simultaneously
- ✅ **Comprehensive reports** - Detailed, well-structured markdown output
- ✅ **Email delivery** - Automatic HTML email formatting and sending
- ✅ **Progress tracking** - Real-time status updates during execution
- ✅ **OpenAI tracing** - Built-in trace logging for debugging

## Technology Stack

- **OpenAI Agent SDK** (`openai-agents`) - Agent framework
- **Gradio** - Web UI for the research pipeline
- **SendGrid** - Email delivery
- **Python asyncio** - Parallel execution
- **Pydantic** - Structured outputs and validation
- **python-dotenv** - Environment variable loading

## Setup

1. Install dependencies:
```bash
pip install openai-agents sendgrid gradio python-dotenv
```

2. Set environment variables:
```bash
OPENAI_API_KEY=your_openai_key
SENDGRID_API_KEY=your_sendgrid_key
```

3. Configure email addresses in `email_agent.py`:
   - Update `from_email` with your verified SendGrid sender
   - Update `to_email` with your recipient

## Usage

### Web UI (recommended)

Run the Gradio app and use it in your browser:

```bash
python deep_research.py
```

Enter a research topic, click **Run** (or press Enter), and watch progress updates and the final report stream in.

### Programmatic

Use the pipeline from your own code:

```python
from research_manager import ResearchManager
import asyncio

async def main():
    manager = ResearchManager()
    async for status in manager.run("What are the latest developments in quantum computing?"):
        print(status)

asyncio.run(main())
```

## Output

The pipeline yields:
1. OpenAI trace URL for debugging
2. Status updates at each stage
3. Final markdown report with:
   - Executive summary
   - Detailed findings (1000+ words)
   - Suggested follow-up questions

## Agent Models

All agents use `gpt-4o-mini` for cost-effective, high-quality results.

## Notes

- The planner generates 5 searches by default (configurable in `planner_agent.py`)
- Search summaries are kept concise (<300 words) to optimize token usage
- Failed searches are handled gracefully (returns `None`, excluded from report)
- Email requires verified SendGrid sender domain

## License

MIT
