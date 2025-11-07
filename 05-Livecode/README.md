# Bonus Challenge: RAG with **Vector**, **Hybrid**, and **Semantic** Search 

--- 
## Overview 

⏲️ **Exercise duration:**  
~60 minutes

🎯 **Exercise objectives:**  
By the end, you’ll be able to:
- Implement RAG retrieval with **vector**, **semantic**, and **hybrid** search modes using Azure AI Search.
- Compare trade‑offs between lexical, semantic, vector, and hybrid strategies.
- (Thinking task) Sketch how to ingest non‑Blob data sources (Azure SQL & Cosmos DB) into Azure AI Search.


🔧 **Tools**  
- Python 
- [Azure AI Search](https://learn.microsoft.com/azure/search/search-what-is-azure-search)  
- [Azure AI Foundry](https://ai.azure.com/)  
- [Azure Blob Storage](https://learn.microsoft.com/azure/storage/blobs/) (you will find the 3 PDF files here)  

🔗 **Additional parameters**  
- Individual Work or in binome.  
- Start from scratch, but you can reuse ideas from the previous challenge. 
- For **vector/hybrid**, ensure your index has a **vector field** and that documents were indexed with **embeddings**. For **semantic**, ensure the index has a **semantic configuration**.

<br />

---

##  📃 Instructions

Create **one file**: `rag_bonus.py`.

### 1️. Import libraries


<details markdown=block>
<summary markdown=span>**Hint 💡**</summary>

You’ll need: `SearchClient`, `AzureKeyCredential`, `AzureOpenAI`, `VectorSearch, HnswAlgorithmConfiguration`, `VectorSearchProfile` for vector/hybrid.
</details>


<br />

---

### 2️. Initialize clients (Search + AI Foundry)

You will need the same clients you used before, but also these two:

```python
# Client to interact with the index (search, update, upload)
search_client = SearchClient(
    endpoint=azure_search_project["endpoint"],
    index_name=name_ressources["index"],
    credential=azure_search_project["credential"]
)

# Client Azure OpenAI pour générer les embeddings (text-embedding-3-large)
openai_client = AzureOpenAI(
    azure_endpoint=AzureOpenAIFoundry["azure_endpoint"],
    api_key=AzureOpenAIFoundry["api_key"],
    api_version=AzureOpenAIFoundry["api_version"]
)
```

You will also need to reset the 4 elements (indexer, index, skillset and data sources). Then, build the data source and skillset as before. 

<br />

---

### 3. Build your index and indexer with Vector search

When building the index, don't forget to add the Semantic configuration. 

```python
semantic_config = SemanticConfiguration(
        name="semantic-config",
        prioritized_fields=SemanticPrioritizedFields(
            title_field=SemanticField(field_name="title"),
            content_fields=[SemanticField(field_name="content")],
        ),
    )
```

You will also need to build a Vector Search object to define in the index. You can use the HNSW algorithm.

```python
# Search Vector configurations (HNSW algorithm)
vector_search = VectorSearch(
    algorithms=[HnswAlgorithmConfiguration(name="hnsw")],
    profiles=[VectorSearchProfile(name="vector-profile", algorithm_configuration_name="hnsw")],
)
```


Then, you will need to add a field in the fields list.

```python
SearchField(
            name="contentVector",
            type=SearchFieldDataType.Collection(SearchFieldDataType.Single),
            searchable=True,
            vector_search_dimensions=AzureOpenAIFoundry["embedding_dimensions"],
            vector_search_profile_name="vector-profile",
        ),
```

Add everything in the SearchIndex.

```python
index = SearchIndex(
        ...
        fields = fields,
        ## parameter to define vector config
        vector_search=vector_search,
        ##

    )
```

The indexer remains the same as before.


Finally, build a function to generate the embedding of each document and upload the index. This is done after creating the data source, skillset, index and indexer.

```python
def generate_and_upload_embeddings():
    
    # simple pause to let Azure AI Search finish the creation of index
    time.sleep(30)  

    #Retrieve documents from Azure Search
    results = ...

    # Building embeddings 
    updated_docs = []
    for i, doc in enumerate(results):
        content = doc.get("content", "")
        if not content:
            continue

        # Call Azure OpenAI → Build embedding
        emb = openai_client.embeddings.create(
            input = ...,
            model = ...
        ).data[0].embedding

        # Prepare the enriched documents to insert in the index.
        updated_docs.append({
            "id"           : ...
            "content"      : ...
            "title"        : ...
            "contentVector": ...
        })
        

    # Update documents vectorized in Azure AI Search.
    if updated_docs:
        search_client.upload_documents(documents=updated_docs)

```



<br />

---

### 4. Retrieval functions

Build a function that allows you to perform vector search, hybrid search, semantic search. Then, execute and compare results. 




--- 
# 🥇 Key Learning Points 

- **Semantic** improves intent match without embeddings; **vector** captures meaning via embeddings; **hybrid** combines both.  
- **Grounded prompting** and short instructions reduce hallucinations.  
- **Index design** (vector field, semantic config, analyzers) drives quality.  
- Azure AI Search can index **Blob**, **Azure SQL**, **Cosmos DB** — pipeline stays: data source → (skillset) → index → indexer. How could you add these sources in your RAG? 

<br />

---

###  When to use what: 

1. Start with Keyword if you need speed, filtering, facets, explainability, and exact-term matching.

2. Add Semantic if answers feel “close but not quite” and you want better passage-level relevance and optional answer snippets, without running an embedding pipeline.

3. Add Vector (or Hybrid) when content/queries are paraphrastic, multi-lingual, or less term-specific; vector improves recall for similar meaning.

4. Best quality often comes from Hybrid + Semantic (lexical + vector fused → semantic re-rank).