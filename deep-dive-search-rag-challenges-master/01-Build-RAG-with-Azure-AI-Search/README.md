## Challenge 1: Build your RAG with Azure AI Search (no Code)

## Overview 

⏲️ **Exercise duration:** 60 minutes  
*(Estimated; depends on portal speed and resource availability.)*

🎯 **Exercise objectives:**  
By the end of this exercise, you will be able to:
- Explain the building blocks of **Retrieval-Augmented Generation (RAG)**.
- Create a **data source, index, indexer, and skillset** in **Azure AI Search** with Azure Portal (no Code).
- Deploy a **chat/generation model** and set up a **Project** in **Azure AI Foundry**, then **chat with your data** in a no-code workflow.
- Validate your search pipeline in Azure AI Search (**Search explorer**) and verify that all components were created correctly.
- Ask grounded questions to your indexed PDFs (3 annual employee interviews) through AI Foundry.

🔧 **Tools**  
- [Azure Portal](https://portal.azure.com/)  
- [Azure AI Search](https://learn.microsoft.com/azure/search/search-what-is-azure-search)  
- [Azure AI Foundry](https://ai.azure.com/)  
- [Azure Blob Storage](https://learn.microsoft.com/azure/storage/blobs/) (you will find the 3 PDF files here)  


🔗 **Additional parameters**  
- Complete **individually**.  
- Instructor available for environment checks (resource group, region alignment, permissions).  
- All resources should be created in the **same region** to avoid cross-region latency/limits.


 <br />

---

## 📃 Instructions

### 1. Theory Recap: What is RAG (fast & practical)

**RAG elements:**
- **Retrieval**: find relevant passages from your knowledge base (e.g. the PDFs that will be indexed in **Azure AI Search**).
- **Augmented Generation**: a **generation model** (LLM) uses the retrieved passages as **grounding** to answer questions accurately.

**Azure components you’ll touch upon:**
- **Data Source**: pointer to your Blob container with PDFs.  
- **Index**: the searchable schema (fields for id, content, metadata, vectors).  
- **Indexer**: the tool that reads the PDFs, extracts text, runs skills (OCR, chunking, embeddings), and writes to the index.  
- **Skillset**: the AI enrichments (e.g., text extraction, splitting, translation).  
- **Embeddings**: numeric vectors for semantic similarity search.  
- **Generation model**: the chat/completions model you’ll use in **AI Foundry** to answer questions grounded by search results.

**Your dataset:** 3 PDFs capturing annual interviews (performance reviews of 3 employees in the company.  
Expect fields like employee name, objectives, strengths, areas to improve, and action items.


 <br />

---

### 2. Setup

**Goal**: Check that you have a Azure AI Foundry project with a deployed model, a Blob container with PDFs, and an AI Search service ready.



#### 2.1 Verify you have access to these Azure resources (in the same region)

1. **Resource Group** `rag-sg-q4` for the exercise.  
2. **Azure Blob Storage**: `ragsgaifoundrystorage`, check in the **Blob service** → **Container** (e.g., `rh-interviews`) with **Private** access.  
3. **Azure AI Search** `rag-sg-aisearch` to create the search index for each data source.
4. **Azure AI Foundry**: open **https://ai.azure.com** and make sure you can create a **Project** in your subscription/region.

<details markdown=block>
<summary markdown=span>**Hint 💡**</summary>
If you see region or quota errors, call a teacher.  
</details>


 <br />
#### 2.2 Create your Azure AI Foundry project

- Go to the Azure AI Hub resource in Azure to start creating GenAI applications. 


<figure style="width: 80%">
  <img alt="01-rag-no-code" src="https://learn.lewagon.com/rails/active_storage/blobs/redirect/eyJfcmFpbHMiOnsibWVzc2FnZSI6IkJBaHBBMnpNQmc9PSIsImV4cCI6bnVsbCwicHVyIjoiYmxvYl9pZCJ9fQ==--2dfe086883b15f15e932d7d1ddcdbb6bcafcfcac/01-rag-no-code.png?disposition=attachment" />
</figure>


- Open [Azure AI Studio](https://ai.azure.com) and sign in using your provided credentials.  

- Launch the Azure AI Foundry Portal. This might ask you to connect with your account again.

- There are two main spaces to know, the project studio and the management center. The first time you open AI Foundry, you might be land in the default project `agazureaifoundry`. 

<figure style="width: 80%">
  <img alt="02-rag-no-code" src="https://learn.lewagon.com/rails/active_storage/blobs/redirect/eyJfcmFpbHMiOnsibWVzc2FnZSI6IkJBaHBBMjNNQmc9PSIsImV4cCI6bnVsbCwicHVyIjoiYmxvYl9pZCJ9fQ==--2158cf53285063b0022424152a89370aa92d59a8/02-rag-no-code.png?disposition=attachment" />
</figure>


- If this is the case, go to the `Management Center` by clicking the button at the bottom of the left menu. Then, you will see the following page: 

<figure style="width: 80%">
  <img alt="03-rag-no-code" src="https://learn.lewagon.com/rails/active_storage/blobs/redirect/eyJfcmFpbHMiOnsibWVzc2FnZSI6IkJBaHBBMjdNQmc9PSIsImV4cCI6bnVsbCwicHVyIjoiYmxvYl9pZCJ9fQ==--eff5d2b16c7a70f6f967229b17b5864527789824/03-rag-no-code.png?disposition=attachment" />
</figure>


- Then, at the Management Center, let's create a New Project. To do so, click on the blue button **+ New Project**. Give a name to the project using your name initials, e.g. `az-ai-foundry-yourname-project` and create. 


<figure style="width: 80%">
  <img alt="04-rag-no-code" src="https://learn.lewagon.com/rails/active_storage/blobs/redirect/eyJfcmFpbHMiOnsibWVzc2FnZSI6IkJBaHBBMi9NQmc9PSIsImV4cCI6bnVsbCwicHVyIjoiYmxvYl9pZCJ9fQ==--715d8560b946ced98b929cb549c110e6667215e4/04-rag-no-code.png?disposition=attachment" />
</figure>



- After the new project has been created, you can see it in the Management Center. Then, click on the button `Go to the Project` on the right menu. 


<figure style="width: 80%">
  <img alt="05-rag-no-code" src="https://learn.lewagon.com/rails/active_storage/blobs/redirect/eyJfcmFpbHMiOnsibWVzc2FnZSI6IkJBaHBBM0RNQmc9PSIsImV4cCI6bnVsbCwicHVyIjoiYmxvYl9pZCJ9fQ==--47e9790bc37e9c7bd4366a1d75916fa4fec7444a/05-rag-no-code.png?disposition=attachment" />
</figure>


- Then, you'll see the project preview with the API key and the url of the Azure OpenAI service endpoint. We'll need both to build our Web App. 

<figure style="width: 80%">
  <img alt="06-rag-no-code" src="https://learn.lewagon.com/rails/active_storage/blobs/redirect/eyJfcmFpbHMiOnsibWVzc2FnZSI6IkJBaHBBM0hNQmc9PSIsImV4cCI6bnVsbCwicHVyIjoiYmxvYl9pZCJ9fQ==--0a1bbfacbefb819d81f3f38c12a7aada050f83f2/06-rag-no-code.png?disposition=attachment" />
</figure>


 <br />

#### 2.3 Deploy a Generative model in your project


- Go to your new  project `az-ai-foundry-yourname-project` and click on `Model catalog` option on the left menu. 


<figure style="width: 80%">
  <img alt="07-rag-no-code" src="https://learn.lewagon.com/rails/active_storage/blobs/redirect/eyJfcmFpbHMiOnsibWVzc2FnZSI6IkJBaHBBM0xNQmc9PSIsImV4cCI6bnVsbCwicHVyIjoiYmxvYl9pZCJ9fQ==--a1be85d4c33a00f00d0b7b88b74e09728d0e649b/07-rag-no-code.png?disposition=attachment" />
</figure>

- Search the `gpt-4o-mini` model and select it.


<figure style="width: 80%">
  <img alt="08-rag-no-code" src="https://learn.lewagon.com/rails/active_storage/blobs/redirect/eyJfcmFpbHMiOnsibWVzc2FnZSI6IkJBaHBBM1RNQmc9PSIsImV4cCI6bnVsbCwicHVyIjoiYmxvYl9pZCJ9fQ==--5478295af74811d6f87f2b3cad7049c4ea531299/08-rag-no-code.png?disposition=attachment" />
</figure>


- Here you can find the information of the model, such as the last day of training, pricing estimation and benchmark with other models. Click the `Use the Model` button.

<figure style="width: 80%">
  <img alt="09-rag-no-code" src="https://learn.lewagon.com/rails/active_storage/blobs/redirect/eyJfcmFpbHMiOnsibWVzc2FnZSI6IkJBaHBBM1BNQmc9PSIsImV4cCI6bnVsbCwicHVyIjoiYmxvYl9pZCJ9fQ==--18dd5f3ffc66a9c632d57caf40dbb40185d60e41/09-rag-no-code.png?disposition=attachment" />
</figure>


- Give a name to your deployment (keep it simple because you are going to use it, `gpt-4o-mini-yourname`) and keep the Default values for Deployment Type (Global Standard), Model Version (2024-07-18) and Connected AI resource. Choose **15K** for the Tokens per minute Rate Limit. Then, deploy the model.


<figure style="width: 80%">
  <img alt="10-rag-no-code" src="https://learn.lewagon.com/rails/active_storage/blobs/redirect/eyJfcmFpbHMiOnsibWVzc2FnZSI6IkJBaHBBM1hNQmc9PSIsImV4cCI6bnVsbCwicHVyIjoiYmxvYl9pZCJ9fQ==--26f6ec4674c8e053da56ea040d964a72de7686e6/10-rag-no-code.png?disposition=attachment" />
</figure>



- After the Model is deployed, go to the option `My assets > Models + endpoints` at the left menu. Then, click on your model.


<figure style="width: 80%">
  <img alt="11-rag-no-code" src="https://learn.lewagon.com/rails/active_storage/blobs/redirect/eyJfcmFpbHMiOnsibWVzc2FnZSI6IkJBaHBBM2ZNQmc9PSIsImV4cCI6bnVsbCwicHVyIjoiYmxvYl9pZCJ9fQ==--d17f7628d2dc51edd9cc27c50d9f41ece28fc9de/11-rag-no-code.png?disposition=attachment" />
</figure>



- You will see the details of the endpoint (Target URI) and the API key of the Model (normaly the same you see before). We will need them for the Web App.

<figure style="width: 80%">
  <img alt="12-rag-no-code" src="https://learn.lewagon.com/rails/active_storage/blobs/redirect/eyJfcmFpbHMiOnsibWVzc2FnZSI6IkJBaHBBM2pNQmc9PSIsImV4cCI6bnVsbCwicHVyIjoiYmxvYl9pZCJ9fQ==--01695078d10a2cb19df57eed0159f6dc3796c3ec/12-rag-no-code.png?disposition=attachment" />
</figure>


- Go back to the `Model catalog` option on the left menu. Look for the 
`text-embedding-3-large` Embedding model (we are going to use it in the next challenges).

<figure style="width: 80%">
  <img alt="13-rag-no-code" src="https://learn.lewagon.com/rails/active_storage/blobs/redirect/eyJfcmFpbHMiOnsibWVzc2FnZSI6IkJBaHBBM25NQmc9PSIsImV4cCI6bnVsbCwicHVyIjoiYmxvYl9pZCJ9fQ==--0eeae8644e0cba2fbcda59db92e19e896e60bf52/13-rag-no-code.png?disposition=attachment" />
</figure>


 - Click the `Use the Model` button. Give a name to your deployment  and keep the Default values for Deployment Type (Global Standard), Model Version (1). Choose **50K** for the Tokens per minute Rate Limit. Then, deploy the model.

<figure style="width: 80%">
  <img alt="14-rag-no-code" src="https://learn.lewagon.com/rails/active_storage/blobs/redirect/eyJfcmFpbHMiOnsibWVzc2FnZSI6IkJBaHBBM3JNQmc9PSIsImV4cCI6bnVsbCwicHVyIjoiYmxvYl9pZCJ9fQ==--095ebbd2f26a23556fcff54719d60a427c781c27/14-rag-no-code.png?disposition=attachment" />
</figure>


- After the Model is deployed, go to the option `My assets > Models + endpoints` at the left menu and check you have your models. 


<figure style="width: 80%">
  <img alt="15-rag-no-code" src="https://learn.lewagon.com/rails/active_storage/blobs/redirect/eyJfcmFpbHMiOnsibWVzc2FnZSI6IkJBaHBBM3ZNQmc9PSIsImV4cCI6bnVsbCwicHVyIjoiYmxvYl9pZCJ9fQ==--38cb226ff5005295eacb93ad9a2ccae6d9f31bb4/15-rag-no-code.png?disposition=attachment" />
</figure>

Now, you are ready to create your RAG chatbot.


 <br />

---

### 3. Build the Search pipeline (no-code) in Azure AI Search


- Open the **Azure AI Search** resource in the Azure Portal.  
- Click **Import data (new)** (top menu).

<figure style="width: 80%">
  <img alt="16-rag-no-code" src="https://learn.lewagon.com/rails/active_storage/blobs/redirect/eyJfcmFpbHMiOnsibWVzc2FnZSI6IkJBaHBBM3pNQmc9PSIsImV4cCI6bnVsbCwicHVyIjoiYmxvYl9pZCJ9fQ==--e5687126c905cc01dd3582deb42a9549e5d94407/16-rag-no-code.png?disposition=attachment" />
</figure>


- In the **Data source menu**, choose **Azure Blob Storage**. 

<figure style="width: 80%">
  <img alt="17-rag-no-code" src="https://learn.lewagon.com/rails/active_storage/blobs/redirect/eyJfcmFpbHMiOnsibWVzc2FnZSI6IkJBaHBBMzNNQmc9PSIsImV4cCI6bnVsbCwicHVyIjoiYmxvYl9pZCJ9fQ==--21c27299ce65478f85d8ce311e0013dc476f805d/17-rag-no-code.png?disposition=attachment" />
</figure>

- Then, you will be ask to select a scenario. For now, let's select the `Keyword Search`.  


<figure style="width: 80%">
  <img alt="18-rag-no-code" src="https://learn.lewagon.com/rails/active_storage/blobs/redirect/eyJfcmFpbHMiOnsibWVzc2FnZSI6IkJBaHBBMzdNQmc9PSIsImV4cCI6bnVsbCwicHVyIjoiYmxvYl9pZCJ9fQ==--7c2125494bae655d64f2a834a63b849fc4b8fbf4/18-rag-no-code.png?disposition=attachment" />
</figure>


- In the Azure Blob **Configuration**: Select the resource group `RAG_SG`, the Storage Account `ragsgaifoundrystorage` and the **`rh-interviews`** container with the 3 annual interviews.  

<figure style="width: 80%">
  <img alt="20-rag-no-code" src="https://learn.lewagon.com/rails/active_storage/blobs/redirect/eyJfcmFpbHMiOnsibWVzc2FnZSI6IkJBaHBBNERNQmc9PSIsImV4cCI6bnVsbCwicHVyIjoiYmxvYl9pZCJ9fQ==--5987f4eecd200dc0ffa18e67303410c1eb14b426/20-rag-no-code.png?disposition=attachment" />
</figure>

- Then, select Next, this might take few minutes.

<figure style="width: 80%">
  <img alt="20b-rag-no-code" src="https://learn.lewagon.com/rails/active_storage/blobs/redirect/eyJfcmFpbHMiOnsibWVzc2FnZSI6IkJBaHBBNEhNQmc9PSIsImV4cCI6bnVsbCwicHVyIjoiYmxvYl9pZCJ9fQ==--dfe6e7c00a1fd9cc18e633dc8d93e605fe5fd354/20b-rag-no-code.png?disposition=attachment" />
</figure>


- Add a **Skillset** (enrichments) with the **Phrase extraction** and **Extract Entities** cognitive skills. Select the option "Use a free AI service".

<figure style="width: 80%">
  <img alt="21-rag-no-code" src="https://learn.lewagon.com/rails/active_storage/blobs/redirect/eyJfcmFpbHMiOnsibWVzc2FnZSI6IkJBaHBBNExNQmc9PSIsImV4cCI6bnVsbCwicHVyIjoiYmxvYl9pZCJ9fQ==--05dbba0683319e1419eb7cb26d6ee5143e7f38d7/21-rag-no-code.png?disposition=attachment" />
</figure>



- Define the **Index** schema. Ensure fields such as:
  - `id` (key, Edm.String)  
  - `content` (full text)  
  - `metadata_storage_name` (filename)  
  - `persons` (obtained with the enrichment)  
  - `locations` (obtained with the enrichment)  

<figure style="width: 80%">
  <img alt="22-rag-no-code" src="https://learn.lewagon.com/rails/active_storage/blobs/redirect/eyJfcmFpbHMiOnsibWVzc2FnZSI6IkJBaHBBNFBNQmc9PSIsImV4cCI6bnVsbCwicHVyIjoiYmxvYl9pZCJ9fQ==--9a34c2967588e7a1fd3e16ed23c304867b8a3a29/22-rag-no-code.png?disposition=attachment" />
</figure>

- Once you selected the fields, click on next.

- Enable semantic ranker (for the retrieval) and schedule the indexing  once (on-demand).

<figure style="width: 80%">
  <img alt="23-rag-no-code" src="https://learn.lewagon.com/rails/active_storage/blobs/redirect/eyJfcmFpbHMiOnsibWVzc2FnZSI6IkJBaHBBNFRNQmc9PSIsImV4cCI6bnVsbCwicHVyIjoiYmxvYl9pZCJ9fQ==--4cac12b15dd12a26633d20aa7bf333f717083cb3/23-rag-no-code.png?disposition=attachment" />
</figure>


- Verify the information an click **Create** to build the search index, skillset, data source and indexer. 

<figure style="width: 80%">
  <img alt="24-rag-no-code" src="https://learn.lewagon.com/rails/active_storage/blobs/redirect/eyJfcmFpbHMiOnsibWVzc2FnZSI6IkJBaHBBNFhNQmc9PSIsImV4cCI6bnVsbCwicHVyIjoiYmxvYl9pZCJ9fQ==--cc0765b61dfa9226c47a6c33d3e0a1879248b2c1/24-rag-no-code.png?disposition=attachment" />
</figure>

- If everything runs correctly, you will obtain the following output.

<figure style="width: 80%">
  <img alt="25-rag-no-code" src="https://learn.lewagon.com/rails/active_storage/blobs/redirect/eyJfcmFpbHMiOnsibWVzc2FnZSI6IkJBaHBBNGJNQmc9PSIsImV4cCI6bnVsbCwicHVyIjoiYmxvYl9pZCJ9fQ==--c9336fc87d6d5463db4a5cad63a00ae03058b85b/25-rag-no-code.png?disposition=attachment" />
</figure>


 <br />

---

### 4. **Validate** the Search setup (Azure AI Search)

#### 4.1 Verify components exist
- In your Search resource, check the left-hand tabs:
  - **Indexes**: your new index is listed and **Ready**.  
  - **Indexers**: status is **Success** (or view error logs).  
  - **Data sources**: the Blob data source is present.  
  - **Skillsets**: your skillset shows enrichment steps.

<figure style="width: 80%">
  <img alt="26b-rag-no-code" src="https://learn.lewagon.com/rails/active_storage/blobs/redirect/eyJfcmFpbHMiOnsibWVzc2FnZSI6IkJBaHBBNG5NQmc9PSIsImV4cCI6bnVsbCwicHVyIjoiYmxvYl9pZCJ9fQ==--5bfda5c9e98af00c5dc5989d4b7326b086446d4d/26b-rag-no-code.png?disposition=attachment" />
</figure>

<figure style="width: 80%">
  <img alt="26c-rag-no-code" src="https://learn.lewagon.com/rails/active_storage/blobs/redirect/eyJfcmFpbHMiOnsibWVzc2FnZSI6IkJBaHBBNHJNQmc9PSIsImV4cCI6bnVsbCwicHVyIjoiYmxvYl9pZCJ9fQ==--cc4e157668ab5afe6e19c47b9c1222ece33c0b3f/26c-rag-no-code.png?disposition=attachment" />
</figure>

<figure style="width: 80%">
  <img alt="26s-rag-no-code" src="https://learn.lewagon.com/rails/active_storage/blobs/redirect/eyJfcmFpbHMiOnsibWVzc2FnZSI6IkJBaHBBNHZNQmc9PSIsImV4cCI6bnVsbCwicHVyIjoiYmxvYl9pZCJ9fQ==--d050bf6d4b2055e61a379aaf2b328b1d05722e11/26s-rag-no-code.png?disposition=attachment" />
</figure>

<figure style="width: 80%">
  <img alt="26e-rag-no-code" src="https://learn.lewagon.com/rails/active_storage/blobs/redirect/eyJfcmFpbHMiOnsibWVzc2FnZSI6IkJBaHBBNHpNQmc9PSIsImV4cCI6bnVsbCwicHVyIjoiYmxvYl9pZCJ9fQ==--b9b6e1ea92438f8e184f8b6be820edddf6619d02/26e-rag-no-code.png?disposition=attachment" />
</figure>


#### 4.2 Test in **Search explorer**
- Open **Search explorer** (in the Search resource).  
- Try a basic query:
   - **Full-text**: search an employee’s name or keywords like “objectives”, “strengths”, “improvement”.
- Try with and without **semantic** ranker and see if there is an improved relevance.
- Check that results show snippets from the annual interview PDFs and relevant filenames.

<figure style="width: 80%">
  <img alt="26-rag-no-code" src="https://learn.lewagon.com/rails/active_storage/blobs/redirect/eyJfcmFpbHMiOnsibWVzc2FnZSI6IkJBaHBBNGpNQmc9PSIsImV4cCI6bnVsbCwicHVyIjoiYmxvYl9pZCJ9fQ==--7a9a2fa0975bf878e5488c1ea579eede4cf09056/26-rag-no-code.png?disposition=attachment" />
</figure>


<details markdown=block>
<summary markdown=span>**Hint 💡**</summary>
If results look empty, re-run the **Indexer** and check the **Index** has a `content` field populated. 
</details>

 <br />

---

### 5. Connect Search to AI Foundry and **Chat with your data**

#### 5.1 Add a connection to Azure AI Search (in your Foundry Project)
- In your **Project** → **Data / Connections** → **+ New connection**.  
- Choose **Azure AI Search** → Select your Search service and the **index** you created.  
- Save the connection.

#### 5.2 Create a no-code Chat grounded on your index
- Go to **Playgrounds** → **Chat**. 

<figure style="width: 80%">
  <img alt="27-rag-no-code" src="https://learn.lewagon.com/rails/active_storage/blobs/redirect/eyJfcmFpbHMiOnsibWVzc2FnZSI6IkJBaHBBNDNNQmc9PSIsImV4cCI6bnVsbCwicHVyIjoiYmxvYl9pZCJ9fQ==--98e3b156b79a9b2ddd12f5bc1e6c663485d64529/27-rag-no-code.png?disposition=attachment" />
</figure>


- Select your **deployed chat/generation model**.  
- Click **Add your data** (or **Grounding** / **Connect data** depending on UI).  

<figure style="width: 80%">
  <img alt="28-rag-no-code" src="https://learn.lewagon.com/rails/active_storage/blobs/redirect/eyJfcmFpbHMiOnsibWVzc2FnZSI6IkJBaHBBNDdNQmc9PSIsImV4cCI6bnVsbCwicHVyIjoiYmxvYl9pZCJ9fQ==--4422e61900dd77f783449adb83de90f17d799493/28-rag-no-code.png?disposition=attachment" />
</figure>


- Choose the **Azure AI Search** connection you just created and select your **index**.  

<figure style="width: 80%">
  <img alt="29-rag-no-code" src="https://learn.lewagon.com/rails/active_storage/blobs/redirect/eyJfcmFpbHMiOnsibWVzc2FnZSI6IkJBaHBBNC9NQmc9PSIsImV4cCI6bnVsbCwicHVyIjoiYmxvYl9pZCJ9fQ==--5ecc21d49e77d86af55e4bcdb156aefb180699b4/29-rag-no-code.png?disposition=attachment" />
</figure>

<figure style="width: 80%">
  <img alt="30-rag-no-code" src="https://learn.lewagon.com/rails/active_storage/blobs/redirect/eyJfcmFpbHMiOnsibWVzc2FnZSI6IkJBaHBBNURNQmc9PSIsImV4cCI6bnVsbCwicHVyIjoiYmxvYl9pZCJ9fQ==--9c0a53270e0c8657b36241a073683bad89ef128d/30-rag-no-code.png?disposition=attachment" />
</figure>

- In the Data management configuration, select Semantic Search type and select the corresponding one to your index.

<figure style="width: 80%">
  <img alt="31-rag-no-code" src="https://learn.lewagon.com/rails/active_storage/blobs/redirect/eyJfcmFpbHMiOnsibWVzc2FnZSI6IkJBaHBBNUhNQmc9PSIsImV4cCI6bnVsbCwicHVyIjoiYmxvYl9pZCJ9fQ==--be5e250c0dbe7d048cf937dca26db1a1adc1ccdf/31-rag-no-code.png?disposition=attachment" />
</figure>

- In the Data connection, select the API authentificaiton type. 

<figure style="width: 80%">
  <img alt="32-rag-no-code" src="https://learn.lewagon.com/rails/active_storage/blobs/redirect/eyJfcmFpbHMiOnsibWVzc2FnZSI6IkJBaHBBNUxNQmc9PSIsImV4cCI6bnVsbCwicHVyIjoiYmxvYl9pZCJ9fQ==--aacb046d56d307359c08264d4d76dc0c308448d4/32-rag-no-code.png?disposition=attachment" />
</figure>

- Save the configuration.

<figure style="width: 80%">
  <img alt="33-rag-no-code" src="https://learn.lewagon.com/rails/active_storage/blobs/redirect/eyJfcmFpbHMiOnsibWVzc2FnZSI6IkJBaHBBNVBNQmc9PSIsImV4cCI6bnVsbCwicHVyIjoiYmxvYl9pZCJ9fQ==--11a2f068d05b0aeb7126eee8beb253440ccfcef4/33-rag-no-code.png?disposition=attachment" />
</figure>


 <br />

---

### 6. Ask questions about the annual interviews
- Example prompts to try:
  - “List each employee’s top 3 objectives from their annual interview.”
  - “What areas for improvement were mentioned and for whom?”
  - “Summarize the action items per employee with due dates if present.”
  - “Compare the strengths highlighted across the three interviews.”
- Observe citations/passages (if shown) and how the model grounds its answer on the retrieved chunks.


<figure style="width: 80%">
  <img alt="34-rag-no-code" src="https://learn.lewagon.com/rails/active_storage/blobs/redirect/eyJfcmFpbHMiOnsibWVzc2FnZSI6IkJBaHBBNVRNQmc9PSIsImV4cCI6bnVsbCwicHVyIjoiYmxvYl9pZCJ9fQ==--999b59ae3fd9371262c5bb9b980489a6ad01e401/34-rag-no-code.png?disposition=attachment" />
</figure>


Congratulations! You have build your first RAG with Azure AI Search and Azure AI Foundry. 



 <br />
 
---

# 🥇 Key Learning Points

- **Conceptual**: You can articulate how **Retrieval (Search)** + **Generation (LLM)** combine to form a **RAG** workflow.
- **Azure AI Search**: You created a **Data source**, **Skillset** (with **embeddings**), **Index**, and **Indexer**, and validated them in **Search explorer**.
- **Azure AI Foundry**: You created your **own Project**, **deployed a generation model**, connected the **Search index**, and performed **no-code, grounded chat**.
- **Data practicalities**: You worked with real-world PDFs (annual interviews), handled extraction, and asked targeted, grounded questions.
- **Quality levers**: Chunking, embeddings, and filters directly influence retrieval quality and, therefore, answer quality.

That's it! Take a small break before diving into the next exercise.