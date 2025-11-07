## Challenge 3: Explore Search & Retrieval Techniques


## Overview

⏲️ **Exercise duration:**  
~60 minutes

🎯 **Exercise objectives:**  
By the end, you’ll be able to:
- Initialize a Python **SearchClient** to query an existing Azure AI Search index.
- Implement a **keyword search** function and interpret results (scores, highlights, fields).
- Experiment with filters/selection and iterate on queries.
- (Bonus) Understand where/how to extend to **semantic**, **vector**, or **hybrid** search.

🔧 **Tools**  
- Python
- Packages: `azure-search-documents`, `azure-core`, `python-dotenv`  
- Azure services: **Azure AI Search**  
- Reference docs:  
  - Python SDK [azure-search-documents](https://pypi.org/project/azure-search-documents/)
  - [Azure AI search](https://learn.microsoft.com/en-us/azure/search/search-lucene-query-architecture)

🔗 **Additional parameters**  
- Individual Work or in binome.  
- You already have an **existing index** pointing to a Blob container with **3 PDFs + 1 Word** (HR interviews + HR agreement).  
- Teacher Q&A is available if you get blocked.

<br />

---

## 📃 Instructions

You will create **one file**: `search.py`. It will:
1) Import libraries for retrieval tasks.
2) Initialize a **SearchClient** bound to an **existing index**.  
3) Implement a function to perform **keyword search**  
4) Execute and try different queries  
5) **Bonus**: Think about how to adapt code for **semantic**, **vector**, or **hybrid** search (after finishing the next challenge)

<br />

### 0. Setup

Before you start, make sure you installed the dependencies:

```bash
pip install azure-search-documents azure-core python-dotenv
```

Verify that you have an `.env` file containing these variables:

```dotenv
AZURE_SEARCH_ENDPOINT=https://your-search-service.search.windows.net
AZURE_SEARCH_ADMIN_KEY=your-admin-key          # or a query key
AZURE_SEARCH_INDEX_NAME=AG-search-index        # or the provided index name
```

Don't hesitate to use the `tools.py` parameters to connect to Azure AI Search. You will use `azure_search_project` and `name_ressources`.

<br />

---

### 1️. Import libraries

Create a new python file `search.py` and add the imports you need.


<details markdown=block>
<summary markdown=span>Answer — imports</summary>

```python
# search.py
from tools import azure_search_project, name_ressources
from azure.search.documents import SearchClient
from azure.search.documents.indexes import SearchIndexClient

```
</details>

<br />

---

### 2️ Inititialize client with existing index

**Theory:**  
To query an index you need to build a **SearchClient** and bind it to an existing index search deployed in Azure. You need the following information: **endpoint**, **index name**, and an **AdminKey**.

**Task:**  
- Find the correct parameters from the tools.py file. 
- Build a `SearchIndexClient(endpoint=..., credential=...)` to list all the existing indexes and find your index name.
- Store the client in a variable `client`.
- Use the function `list_indexes()` to get all indexes and print them.

<details markdown=block>
<summary markdown=span><strong>Answer — client setup</strong></summary>

```python
client = SearchIndexClient(
        endpoint=azure_search_project["endpoint"], credential=azure_search_project["credential"]
        )

# List existing indexes
indexes = client.list_indexes()

for index in indexes:
   index_dict = index.as_dict()
   print(json.dumps(index_dict, indent=2))
```
</details>


- Once you checked your index name in the list. Build a `SearchClient(endpoint=..., index_name=..., credential=...)` using your search index. This should be the same you defined in tools.py
- Store the client in a variable `search_client`


<details markdown=block>
<summary markdown=span><strong>Answer — client setup</strong></summary>

```python
search_client = SearchClient(
                endpoint   = azure_search_project["endpoint"],
                index_name = name_ressources["index"],
                credential = azure_search_project["credential"]
                )
```
</details>


<br />

---

### 3. Create a function to perform the search

**Theory:**  
**Keyword (lexical) search** matches terms in searchable fields (e.g., `title`, `chunks`). Some of the parameters you can control:
- `search_text`: your query  
- `top`: number of results  
- `select`: a list of which fields to return  
- `query_type`: `simple` (default) or `semantic` (if semantic search is enabled on the index)
- You can find more information in the [documentation](https://learn.microsoft.com/en-us/python/api/azure-search-documents/azure.search.documents.searchclient?view=azure-python)

**Task:**  
- Implement a function `search_keywords(query: str, top: int)`.  
- Show the total number of retrieved documents.
- Print result **score**, **title**, and a short **snippet** (from highlights or a field)


- Function signature: 
```python
def search_keywords(query: str, top: int):
    
    # Retrieve documents
    results = search_client.search(...)

    # Print total number of retrieved documents
    total_count = results.get_count()

    # Analyse each retrieved document
    for i, r in enumerate(results):
        ...
    

    # return max possible retrieved docs
```

<details markdown=block>
<summary markdown=span><strong>Answer — search_keywords()</strong></summary>

```python
def search_keywords(query: str, top: int):

    # Retrieve documents
    results = search_client.search(
        search_text=query,
        top=top,
        query_type="simple",  # try "semantic" later if enabled on your index
    )

    # Total number of retrieved documents
    print(f"🔎 Retrieving documents for Query: {query}")
    total_count = results.get_count()
    print(f"Total number of documents matching the query : {total_count}\n")

    # Analyse each retrieved document
    for i, result in enumerate(results):
        print(f"Document {i+1}")
        print(f"Score : {result.get('@search.score', 'N/A')}")
        print(f"Title : {result.get('title', '(without title)')}")
        text = result.get("chunks") or result.get("content") or "(document with no text)"
        print(f"Snippet : {text[:400]}...\n")
    
    return results[:max(top,total_count)]
    
```
</details>


Note that the documents retrieved in the search result are ranked by the score. By default, search index assign a relevance score to each document. For the keyword search, the score is computed based on the [term frequency–inverse document frequency (TF/IDF)](https://en.wikipedia.org/wiki/Tf%E2%80%93idf). For example, in a hypothetical index with all CV of employees, from documents that matched the query the `engineer`, documents matching on `engineer` are considered more relevant than documents matching on `lawyer`. 


<br />

---

### 4. Execute the code and try different queries. 

**Theory:**  
For now, we implemented a keyword search. Start with simple, specific keywords. If your index contains HR interviews and agreements, try job titles, names, departments, constraints, etc. Observe how scores and snippets change.

**Task:**  
- Add a `main` block to run a few fixed queries
- Bonus: Add an interactive prompt.


<details markdown=block>
<summary markdown=span><strong>Answer — main runner</strong></summary>


```python
if __name__ == "__main__":
    print("=== Azure AI Search — Keyword search ===")

    # Try a few queries related to HR interviews/agreements
    sample_queries = [
        "interview",
        "probation period",
        "remote work",
        "salary review",
        "agreement",
        "What is the surname of Sarah?"
    ]

    for q in sample_queries:
        search_keywords(q, top=5)

    # Optional: interactive prompt
    # while True:
    #     q = input("Enter a query (or blank to quit): ").strip()
    #     if not q:
    #         break
    #     search_keywords(q, top=5)
```
</details>

<br />

---

### 5. Bonus (do after the next challenge): Think about vector / hybrid / semantic search

**Theory on search retrieval methods:**  
- **Keyword (lexical)**: precise with exact terms; may miss paraphrases/synonyms.  
- **Semantic**: improves relevance using semantic signals. Requires a **semantic configuration** on the index; then set `query_type="semantic"` and `semantic_configuration_name="..."`.  We did this code in the previous challenge when creating the index. But we did not exploit it yet.
- **Vector**: uses embeddings to capture meaning beyond exact words. Requires a **vector field** in the index and embeddings created at indexing time.  
- **Hybrid**: combines keyword + vector (often best relevance).

**Task (thinking only for now):**  
- Identify **where** to change your code to enable these modes.
- Try to analyse the difference of keyword search with or without semantic search. 


<details markdown=block>
<summary markdown=span><strong>Answer — semantic search</strong></summary>

- Semantic search (if enabled on index)
```python
results = search_client.search(
    search_text=query,
    top=top,
    select=",".join(select_fields),
    highlight_fields=highlight_fields,
    query_type="semantic",
    semantic_configuration_name="semantic-config",  # must exist on the index
)
```
</details>

- For semantic, ensure your service tier supports it and the index has a `semantic-config`.

- Let's stop for now, we will continue exploring other retrieve techniques (Vector and Hybrid) during the Livecode.

- Congratulations, now you know the basics to retrieve documents from a search index deployed in Azure AI Search.

<br />

---
# 🥇 Key Learning Points

- **SearchClient** is used to **query**, while Index clients manage schema and pipelines.  
- **Keyword search** relies on lexical matching; tune fields, highlights, `select`, and filters.  
- **Result inspection** (score, snippet, path) helps debug relevance and content coverage.  
- **Modes**:  
  - *Keyword* = exact terms, transparent behavior  
  - *Semantic* = better intent/relevance (requires semantic config)  
  - *Vector* = meaning-based similarity (requires embeddings + vector field)  
  - *Hybrid* = combines strengths for best overall relevance  
  - We will explore Vectore and Hybrid during the Livecode
- Your code structure makes it easy to **swap query modes** by adjusting parameters where you call `search_client.search(...)`.  


That's it! Before going to the next challenge, help other students that are struggling with the exercise.