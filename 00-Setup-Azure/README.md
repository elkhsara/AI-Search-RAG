To install [OpenAI](https://github.com/openai/openai-python) with Anaconda, we will follow these steps:

1. Create an environment using Anaconda Navigator.
2. Activate the environment
3. Install and test OpenAI in the new environment


## 1. Create an environment using Anaconda Navigator

- Open **Anaconda Navigator**.

- In the left menu, click **Environments**.

<figure style="width: 80%">
  <img alt="00-setupo.png" src="https://learn.lewagon.com/rails/active_storage/blobs/redirect/eyJfcmFpbHMiOnsibWVzc2FnZSI6IkJBaHBBMS9NQmc9PSIsImV4cCI6bnVsbCwicHVyIjoiYmxvYl9pZCJ9fQ==--bc1fd911fb8945852ea5b8f91e566faea38f3214/Anaconda-Navigator-environment-1.png?disposition=attachment" />
  <figcaption>Opening Anaconda Environments
</figcaption>
</figure>



- At the bottom of your environments list, click **Create**. 


<figure style="width: 80%">
  <img alt="01-setupo.png" src="https://learn.lewagon.com/rails/active_storage/blobs/redirect/eyJfcmFpbHMiOnsibWVzc2FnZSI6IkJBaHBBMkRNQmc9PSIsImV4cCI6bnVsbCwicHVyIjoiYmxvYl9pZCJ9fQ==--3bbb202942f1e5c1c64beec8884ff6d40654754d/Anaconda-Navigator-environment-2-create.png?disposition=attachment" />
<figcaption>Creating a new environment
</figcaption>
</figure>




- Enter `azure-env` for the name of your environment with the last python version 3.11.xx (e.g. 3.11.5, 3.11.9)

<figure style="width: 80%">
  <img alt="02-setupo.png" src="https://learn.lewagon.com/rails/active_storage/blobs/redirect/eyJfcmFpbHMiOnsibWVzc2FnZSI6IkJBaHBBMkhNQmc9PSIsImV4cCI6bnVsbCwicHVyIjoiYmxvYl9pZCJ9fQ==--d2c9924a4e7fd96a2c4707b2bca8fa16f308ce4e/Anaconda-Navigator-environment-3-create.png?disposition=attachment" />
<figcaption>Entering a name for the new environment
</figcaption>
</figure>



- Click **Create**.


- After the creation of the new environment, go back to the `Home` and install `Jupyter Notebook`. 

<figure style="width: 80%">
  <img alt="03-setupo.png" src="https://learn.lewagon.com/rails/active_storage/blobs/redirect/eyJfcmFpbHMiOnsibWVzc2FnZSI6IkJBaHBBMkxNQmc9PSIsImV4cCI6bnVsbCwicHVyIjoiYmxvYl9pZCJ9fQ==--fa0925be0aa31829d499b6e1f15e918602b83f91/Anaconda-Navigator-environment-4-create.png?disposition=attachment" />
</figure>



If this does not work, close `Anaconda Navigator` and open it as Administrator. Then, try to reinstall jupyter notebook again.


<figure style="width: 80%">
  <img alt="04-setupo.png" src="https://learn.lewagon.com/rails/active_storage/blobs/redirect/eyJfcmFpbHMiOnsibWVzc2FnZSI6IkJBaHBBMlBNQmc9PSIsImV4cCI6bnVsbCwicHVyIjoiYmxvYl9pZCJ9fQ==--9798e1aee0700f98489147e53b366680dd610c18/Anaconda-Navigator-environment-5-create.png?disposition=attachment" />
</figure>



## 2. Activate the environment

- Go to Environments in the Anaconda Navigator and click the green play icon (▶️) next to your new environment.

- Click **Open Terminal**.

<figure style="width: 80%">
  <img alt="05-setupo.png" src="https://learn.lewagon.com/rails/active_storage/blobs/redirect/eyJfcmFpbHMiOnsibWVzc2FnZSI6IkJBaHBBMlRNQmc9PSIsImV4cCI6bnVsbCwicHVyIjoiYmxvYl9pZCJ9fQ==--b36517c3f602d950d0cb6ff80ef6dbf15a222eaa/Anaconda-Navigator-environment-6-create.png?disposition=attachment" />
<figcaption>Open a terminal with the new environment.
</figcaption>
</figure>



- A terminal will open with your environment activated. Your environment's name will appear in parentheses at the beginning of your terminal's prompt to show that it's activated.


## 3. Install and test Azure OpenAI in the new environment

- [Install OpenAI](https://github.com/openai/openai-python) using `pip install` in the terminal:
```bash
pip install openai
pip install azure-search-documents
```


<figure style="width: 80%">
  <img alt="06-setupo.png" src="https://learn.lewagon.com/rails/active_storage/blobs/redirect/eyJfcmFpbHMiOnsibWVzc2FnZSI6IkJBaHBBMlhNQmc9PSIsImV4cCI6bnVsbCwicHVyIjoiYmxvYl9pZCJ9fQ==--e524bad16db3dbfd8c3da66766767f1f06459c40/Anaconda-Navigator-environment-7-create.png?disposition=attachment" />
<figcaption>Installing OpenAI library
</figcaption>
</figure>


At the end of the installation, you should see a `succesful` message as shown in the Figure:

<figure style="width: 80%">
  <img alt="07-setupo.png" src="https://learn.lewagon.com/rails/active_storage/blobs/redirect/eyJfcmFpbHMiOnsibWVzc2FnZSI6IkJBaHBBMmJNQmc9PSIsImV4cCI6bnVsbCwicHVyIjoiYmxvYl9pZCJ9fQ==--11121c4ec08c422a0e4de1d6f985ce913ed00cc1/Anaconda-Navigator-environment-8-create.png?disposition=attachment" />
<figcaption>OpenAI installed successfully
</figcaption>
</figure>


- Then, [Install python-dotenv](https://pypi.org/project/python-dotenv/) using `pip install` in the terminal. This library is useful to keep API tokens and other confidential information safe locally when developing with python. 
```bash
pip install python-dotenv
```

- To validate your installation, go to the Anaconda Navigator and open VS Code using the environment `azure-env` . Create a new folder  `azuretest` and create an empty file  `app.py`. In the file, copy the following code:

```python
# Import main libraries
import os
from dotenv import load_dotenv 
from openai import AzureOpenAI

# loading variables from .env file
load_dotenv() 


# Authenticate the client using your key and endpoint 
client = AzureOpenAI(
    api_key=os.getenv("AZURE_OPENAI_API_KEY"),  
    api_version="2024-12-01-preview",
    azure_endpoint=os.getenv("AZURE_OPENAI_ENDPOINT")
)

# Interact with GPT-4o-mini deployment
response = client.chat.completions.create(
    model="gpt-4o-mini", # model = "deployment_name".
    messages=[
        {"role": "user", "content": "Hey! This is my first GenAI App. What do you recommend?"}
    ]
)

print(response.choices[0].message.content)
```


<figure style="width: 80%">
  <img alt="09-setupo.png" src="https://learn.lewagon.com/rails/active_storage/blobs/redirect/eyJfcmFpbHMiOnsibWVzc2FnZSI6IkJBaHBBMmpNQmc9PSIsImV4cCI6bnVsbCwicHVyIjoiYmxvYl9pZCJ9fQ==--42926e47727178b1c626a9246b463f57cddab32d/Anaconda-Navigator-environment-9-create.png?disposition=attachment" />
</figure>



- Then, download this [.env](/rails/active_storage/blobs/redirect/eyJfcmFpbHMiOnsibWVzc2FnZSI6IkJBaHBBOTNNQmc9PSIsImV4cCI6bnVsbCwicHVyIjoiYmxvYl9pZCJ9fQ==--9c8576a4fc2ae038831e2d76096d6c047a201429/.env?disposition=attachment) file containing the environment variables you will need to query Azure OpenAI models (here we are using GPT-4o-mini).  Move this file in the  `azuretest` folder and **check that the name of the file is `.env`, it is not a .txt file, it is a .env file**. Open the file in VS Code, it should contain something like this:

<figure style="width: 80%">
  <img alt="08-setupo.png" src="https://learn.lewagon.com/rails/active_storage/blobs/redirect/eyJfcmFpbHMiOnsibWVzc2FnZSI6IkJBaHBBMmZNQmc9PSIsImV4cCI6bnVsbCwicHVyIjoiYmxvYl9pZCJ9fQ==--eb2b40f011d0db73c47d68a8fe7c645b31582ff7/Anaconda-Navigator-environment-9b-create.png?disposition=attachment" />
</figure>

- Then, go back to the terminal and find the new repertory containing your file `app.py`. Run the command:

```bash
cd  <your_path>\azuretest
python app.py
```

⚠️**Please do not play with this code or modify the prompt. Execute this code only to get an answer once. Then you can move on to the challenges. It's important to understand that every time you use it, it has a cost and consumes the training credits.**


- You should obtain something like this: 


<figure style="width: 80%">
  <img alt="10-setupo.png" src="https://learn.lewagon.com/rails/active_storage/blobs/redirect/eyJfcmFpbHMiOnsibWVzc2FnZSI6IkJBaHBBMm5NQmc9PSIsImV4cCI6bnVsbCwicHVyIjoiYmxvYl9pZCJ9fQ==--3b28d57f2f9a8f17048b8003a9cb5300a703a4cc/Anaconda-Navigator-environment-10-create.png?disposition=attachment" />
<figcaption>Testing the OpenAI library  in terminal
</figcaption>
</figure>


- We'll explain this code in more detail during training. For now, you can see that our GPT-4o-mini model deployed from Azure can respond through your code. 

- Now, you can close the terminal.

If you were able to send your prompt, then you've successfully installed OpenAI library!
