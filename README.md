# Agentic RAG with LlamaIndex

A multi-document AI agent that can answer questions across multiple research papers using Retrieval-Augmented Generation (RAG). Built with LlamaIndex, Groq (LLaMA 3.1), and HuggingFace embeddings — no OpenAI required.


## What it does

- Loads and indexes multiple research PDFs (MetaGPT, LongLoRA, Self-RAG, and more)
- Gives the agent two tools per paper: a **vector search tool** (for specific questions) and a **summary tool** (for overviews)
- Uses a **ReAct agent** that reasons step-by-step about which tool to use and when
- Scales to 11+ papers using **ObjectIndex** for tool retrieval — the agent dynamically picks the right tools instead of loading all of them at once


## Demo queries

```
"Give me a summary of both Self-RAG and LongLoRA"
"Tell me about the evaluation dataset used in LongLoRA and the results"
"Compare and contrast LongLoRA and LoftQ — analyze the approach in each"
"Tell me about the evaluation dataset in MetaGPT and compare it against SWE-Bench"
```



## Tech stack

| Component | Tool |
|---|---|
| Framework | LlamaIndex (llama-index-core) |
| LLM | Groq API — LLaMA 3.1 8B Instant |
| Embeddings | HuggingFace — BAAI/bge-small-en-v1.5 (local, free) |
| Agent type | ReAct Agent |
| Vector store | In-memory VectorStoreIndex |
| PDF loading | SimpleDirectoryReader |


## Project structure

```
├── Lesson1/L1_Router_Engine.ipynb
├── Lesson2/L2_Tool_Calling.ipynb
├── Lesson3/L3-Build_an_Agent_Reasoning_Loop.ipynb
├── Lesson3/utils.py
├── Lesson4/L4_Building_Multi_Document_Agent.ipynb
├── Lesson4/utils.py
└── .gitignore
```


## Setup

**1. Clone the repo**
```bash
git clone https://github.com/prithvi174/Agentic-Rag-llamaIndex.git
cd Agentic-Rag-llamaIndex
```

**2. Install dependencies**
```bash
pip install llama-index llama-index-llms-groq llama-index-embeddings-huggingface groq python-dotenv nest_asyncio
```

**3. Add your Groq API key**

Create a `.env` file in each lesson folder:
```
GROQ_API_KEY=your_key_here
```

Get a free key at [console.groq.com](https://console.groq.com)

**4. Add research PDFs**

Place the required PDFs in each lesson folder. The notebooks expect files like `metagpt.pdf`, `longlora.pdf`, `selfrag.pdf` etc.

**5. Run the notebooks**

Open in Jupyter and run all cells.


## How it works

### Tool creation (utils.py)

For each PDF, `get_doc_tools()` creates two tools:

- `vector_tool_{name}` — embeds chunks locally, retrieves top-2 similar chunks for specific questions
- `summary_tool_{name}` — reads the full document and summarizes using tree_summarize mode

### Agent reasoning (ReAct loop)

The agent iteratively:
1. Thinks about what information it needs
2. Picks the right tool
3. Calls the tool and gets a result
4. Reasons over the result and decides next steps
5. Repeats until it has a complete answer

### Scaling to 11 papers (ObjectIndex)

With 22 tools (2 per paper × 11 papers), an `ObjectIndex` embeds the tool descriptions and retrieves only the top-3 relevant tools per query — the agent never sees tools it doesn't need.


## Key concepts

- RAG pipeline — chunking, embedding, vector search, generation
- Tool calling — wrapping query engines as callable agent tools
- ReAct reasoning — Thought → Action → Observation loop
- Multi-document agents — routing queries across many knowledge sources
- Tool retrieval — vector search on tool descriptions to scale agent tool usage


## Notes

- Free Groq tier has rate limits — `llama-3.1-8b-instant` works best
- Embeddings run locally via HuggingFace — no embedding API costs
- Based on the DeepLearning.AI "Building Agentic RAG with LlamaIndex" course



## Research Papers Used

Download and place in the respective lesson folders:

| Paper | Link |
|---|---|
| MetaGPT | https://openreview.net/pdf?id=VtmBAGCN7o |
| LongLoRA | https://openreview.net/pdf?id=6PmJoRfdaK |
| Self-RAG | https://openreview.net/pdf?id=hSyW5go0v8 |
| LoftQ | https://openreview.net/pdf?id=LzPWWPAdY4 |
| SWE-Bench | https://openreview.net/pdf?id=VTF8yNQM66 |
| Zipformer | https://openreview.net/pdf?id=9WD9KwssyT |
| Values in the Wild | https://openreview.net/pdf?id=yV6fD7LYkF |
| Finetuning Fair Diffusion | https://openreview.net/pdf?id=hnrB5YHoYu |
| Knowledge Card | https://openreview.net/pdf?id=WbWtOYIzIK |
| METRA | https://openreview.net/pdf?id=c5pwL0Soay |
| VR-MCL | https://openreview.net/pdf?id=TpD2aG1h0D |

