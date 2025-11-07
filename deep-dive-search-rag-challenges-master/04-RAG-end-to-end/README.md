## Challenge 4: Build your RAG chatbot end-to-end through code

## Overview

⏲️ **Exercise duration:**  
~60 minutes

🎯 **Exercise objectives:**  
By the end, you’ll be able to:
- Initialize Python clients for **Azure AI Search** and **AI Foundry (Azure OpenAI)**.
- Retrieve relevant passages from an existing **keyword search index**.
- Prompt a **GPT‑4o‑mini** deployment to generate an answer grounded in the retrieved context.
- Run end‑to‑end queries with HR documents (PDF + Word) stored in Blob via the existing index.
- Reflect on prompt design and mitigation of hallucinations.

🔧 **Tools**  
- Python 
- Packages: `azure-search-documents`, `openai`, `python-dotenv`  
- Azure services: **Azure AI Search**, **Azure AI Foundry**  
- Reference docs:  
  - Python SDK [azure-search-documents](https://pypi.org/project/azure-search-documents/)
  - [Azure OpenAI (Python)](https://learn.microsoft.com/en-us/python/api/overview/azure/ai-projects-readme)

🔗 **Additional parameters**  
- Individual Work or in binome.  
- You already have an **existing search index** pointing to a Blob container with **3 PDFs + 1 Word** (HR interviews + HR agreement).  
- Teacher Q&A is available if you get blocked.

<br />

---

## 📃 Instructions

You will create **one file**: `chatbot.py`. It will:
1) Import libraries to generate an adapted answer based on grounded context.
2) Initialize **SearchClient** (existing index) and **AzureOpenAI** client (AI Foundry)  
3) Implement a function that: **(a)** performs query retrieval (e.g. keyword), **(b)** builds a prompt with the best passages, **(c)** asks the model, and **(d)** returns the answer  
4) Execute and try several user questions  
5) Bonus: reflect on prompt/assistant improvements (roles, instructions, guardrails)

<br />

### 0. Setup

Before you start, make sure you installed the dependencies:

```bash
pip install azure-search-documents azure-core python-dotenv
```

Ensure your `.env` already contains:
```dotenv
AZURE_SEARCH_ENDPOINT=https://your-search-service.search.windows.net
AZURE_SEARCH_ADMIN_KEY=your-admin-key
AZURE_SEARCH_INDEX_NAME=your-search-index

AZURE_OPENAI_ENDPOINT=https://your-openai-endpoint.openai.azure.com
AZURE_OPENAI_API_KEY=your-openai-key
AZURE_OPENAI_API_VERSION=2024-02-15-preview
AZURE_OPENAI_DEPLOYMENT_NAME=gpt-4o-mini
```

If you followed the previous challenges, your `tools.py` likely exposes three dicts we’ll reuse: `azure_search_project`, `name_ressources`, `AzureOpenAIFoundry`.

<br />

---

### 1️. Import libraries

Create a new python file `chatbot.py`. Add the minimal imports to Search with AI Search, to use Azure OpenAI and the tools config.


<details markdown=block>
<summary markdown=span><strong>Answer — imports</strong></summary>

```python
# chatbot.py
from openai import AzureOpenAI
from azure.search.documents import SearchClient
from tools import azure_search_project, name_ressources, AzureOpenAIFoundry
```
</details>

<br />

---

### 2️. Inititialize search client with existing index and AzureOpenAI client to connect with AI Foundry

**Theory:**  
- **SearchClient** queries your existing index (already built from Blob).  
- **AzureOpenAI (AI Foundry)** provides chat completions (we’ll use `gpt-4o-mini`).  
- Using **keyword search** keeps retrieval transparent: the index returns passages from `chunks` (split text).

**Task:**  
- Build a `SearchClient(endpoint=..., index_name=..., credential=...)` with your Search **endpoint**, **index name**, **credential** (same from the previous challenge).  
- Build an `AzureOpenAI(azure_endpoint=..., api_key=..., api_version=...)` client using values from `AzureOpenAIFoundry` (endpoint, API key, version).


<details markdown=block>
<summary markdown=span><strong>Answer — client setup</strong></summary>

```python
# 1) Azure Cognitive Search
search_client = SearchClient(
    endpoint=azure_search_project["endpoint"],
    index_name=name_ressources["index"],
    credential=azure_search_project["credential"],
)

# 2) Azure OpenAI (AI Foundry)
client = AzureOpenAI(
    azure_endpoint=AzureOpenAIFoundry["azure_endpoint"],
    api_key=AzureOpenAIFoundry["api_key"],
    api_version=AzureOpenAIFoundry["api_version"],
)
```
</details>

If you have no errors, you can continue.

<br />

---

### 3. Create a function to perform the search and the generative answer from AI Foundry

**Theory:**  
- **Step 1: Retrieve** with keyword search → get the top‑k passages (e.g., from `chunks`).  
- **Step 2: Grounded generation** → construct a prompt that **instructs the model to answer using only the provided context**.  
- **Step 3: Render** → return the generated text.

**Task:**  
- Implement `ask_azure_chatbot(query: str, top_k: int = 3)`  
- Use `search_client.search(search_text=query, top=top_k)`  
- Collect a few `chunks` into a `context_text` string  
- Call `client.chat.completions.create(...)` with a concise **system** message and a **user** message that includes question + context
- Return the model’s answer



- Function signature: 
```python
def ask_azure_chatbot(query: str, top: int = 3):
    
    # Retrieve documents
    

    # Verify that you get some retrieved documents
  

    # Build the prompt chain with query and context
    messages = [
        {
            "role": "system",
            "content": (
                "your system prompt"
            ),
        },
        {
            "role": "user",
            "content": f"the query with context",
        },
    ]
    
    # give prompt chain to the generative model with retrieved context
    completion = client.chat.completions.create(
        model=...,
        messages=...,
    )

    # Render the answer


    # return the answer generated
```

<br />


<details markdown=block>
<summary markdown=span><strong>Answer — ask_azure_chatbot()</strong></summary>

```python
def ask_azure_chatbot(query: str, top_k: int = 3):
    print(f"Searching Azure Cognitive Search for: "{query}"")

    # Step 1 — Retrieve passages
    results = search_client.search(search_text=query, top=top_k)
    contexts = [doc.get("chunks", "").strip() for doc in results if doc.get("chunks")]

    if not contexts:
        print("No passages found in the index.")
        return "No supporting context was found in Azure Search."

    print(f"Retrieved {len(contexts)} passage(s) from Azure Search.")
    context_text = "\n\n".join(contexts)

    # Step 2 — Ask the model with grounded context
    messages = [
        {
            "role": "system",
            "content": (
                "You are a helpful assistant. Answer concisely using ONLY the provided context. "
                "If the answer is not in the context, say you do not know."
            ),
        },
        {
            "role": "user",
            "content": f"Question: {query}\n\nContext:\n{context_text}",
        },
    ]

    completion = client.chat.completions.create(
        model=AzureOpenAIFoundry["chat_deployment"],
        messages=messages,
    )

    # Step 3 — Render the answer
    answer = completion.choices[0].message.content.strip()
    print(f"Answer: {answer}")
    return answer
```
</details>



**Quality tips (optional):**
- Limit context size to avoid hitting token limits (e.g., keep top‑3 passages, truncate long chunks).
- Add **source listing** (file name/path) alongside the answer for traceability.
- Try **temperature=0–0.3** for factual answers; higher values increase creativity (and risk of drift).


<br />

---

### 4. Execute the code and try different queries. For now, we try a keyword search

**Theory:**  
Start with targeted HR queries (e.g., “probation period length”, “remote work policy”, “salary review process”, "what is the position of Sarah?"). Evaluate if answers cite information that appears in the HR agreement or interview notes.

**Task:**  
- Add a `__main__` block to run test queries and/or prompt in a loop.


<details markdown=block>
<summary markdown=span><strong>Answer — main runner</strong></summary>


```python
if __name__ == "__main__":
    print("==============================================")
    print(" RAG Chatbot — Azure OpenAI + Azure AI Search (Keywords)")
    print("==============================================\n")
    try:
        print("Your question:")
        question = input("")
        print()
        response = ask_azure_chatbot(question, top_k=3)
        print("\nAssistant:\n", response)
    except KeyboardInterrupt:
        print("Session ended by user.")
```
</details>


<br />

---

### 5. Bonus: Reflect on using a generative model for the final answer

**Prompt & role design: what to improve?**
- **Stronger system prompt**: forbid speculation, require quotes/snippets from sources, ask for a short **“evidence”** section.  
- **Citation style**: append `(Source: <filename>)` when possible.  
- **Safety**: if the question asks for personal data not present in docs, respond with a refusal or guidance.  
- **Formatting**: return answers with bullets/sections for HR stakeholders.




**Search techniques**
- How would you implement semantic, vector or hybrid search with the chatbot?
- What are the pros/cons of each technique(engineering)?

<details markdown=block>
<summary markdown=span><strong>Answers</strong></summary>
- **Keyword (lexical)**: ✅ transparent, fast; ❌ misses paraphrases.  
- **Semantic**: ✅ better intent match; ❌ requires semantic config & higher tier.  
- **Vector**: ✅ captures meaning; ❌ requires embeddings + vector fields + more engineering.  
- **Hybrid**: ✅ combines lexical + vector (often best); ❌ more moving parts.
</details>


**Next steps (not required here):**
- Try **semantic** mode (`query_type="semantic"`, `semantic_configuration_name="..."`).  
- Try **vector/hybrid** retrieval using embeddings and a vector field (then combine with `search_text`).  
- Implement **max‑tokens** and **context window** management.


<br />

--- 
# 🥇 Key Learning Points 

- How to wire **retrieval** (Azure AI Search) to **generation** (AI Foundry) in Python.  
- Why **grounded prompts** reduce hallucinations and how to craft them.  
- How to use **keyword** search in a RAG system (in the Livecode, we will analyse  **semantic** vs **vector** vs **hybrid**).  
- Evaluate the impact of small `top_k`, highlight snippets, low temperature, and logging improve reliability.

