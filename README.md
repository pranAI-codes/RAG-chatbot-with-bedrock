[RAG-chatbot.md](https://github.com/user-attachments/files/23129386/RAG-chatbot.md)
<img src="https://cdn.prod.website-files.com/677c400686e724409a5a7409/6790ad949cf622dc8dcd9fe4_nextwork-logo-leather.svg" alt="NextWork" width="300" />

# Set Up a RAG Chatbot in Bedrock

**Project Link:** [View Project](http://learn.nextwork.org/projects/ai-rag-bedrock)

**Author:** Pranay  
**Email:** pranaystar14@gmail.com

---

## Set Up a RAG Chatbot in Bedrock

![Image](http://learn.nextwork.org/excited_turquoise_smart_kiwi/uploads/ai-rag-bedrock_d5e8f1g2)

---

## Introducing Today's Project!

RAG (Retrieval Augmented Generation) is a way to train an LLM on your own personal documents, essentially adding your data to it's knowledge base. In this project, I will demonstrate RAG by creating my own knowledge base in AWS bedrock, choose an appropriate model and finally sync my documents from S3 to the knowledge base. 

### Tools and concepts

Services I used were AWS bedrock for setting up a knowledge base, S3 buckets to store my data and how RAG is applied in AWS using Embedding models and OpenSearch in this case.
Key concepts I learnt include RAG, vector stores and basic model training architechture. 

### Project reflection

This project took me approximately 60 minutes.
The most challenging part was figuring out how to get around model acces being retired, but then I realised AWS has given access to everyone and I did not need to ask for access and could direcly test the model.
The most rewarding part was tinkering around with the various configurations of the model like generation prompts, guard rails, source chunks and query mods.

I chose to do this project today because I am eager to learn more about the working of RAG and its architechture.

---

## Understanding Amazon Bedrock

Amazon Bedrock is essentially a marketplace for multiple AI models. I'm using Bedrock in this project to create a knowledge base to train the model on, using my own documents.

My Knowledge Base is connected to S3 because I need a storage solution for my own documents to train the model on. 

KBase is connected to S3 with vector store.

In an S3 bucket, I uploaded details of the Nextwork projects I have done and plan to do. My S3 bucket is in the same region as my Knowledge Base because bedrock is region specific, this means it can only access data that is located witihin the same region

![Image](http://learn.nextwork.org/excited_turquoise_smart_kiwi/uploads/ai-rag-bedrock_b5c8d1e2)

---

## My Knowledge Base Setup

My Knowledge Base uses a vector store, which means if searching for a particular text, it will also search for related texts for the same topic, Example: if I search for an "ML textbook" it will also show me results for AI, Deep Learning, LLMs, etc. becaise it understands the meaning behind the words. When I query my Knowledge Base, OpenSearch will grab the most relevant chunks of text to answer the question I post to my model. (Bedrock will go into OpenSearch vector store and get the relevant ansewrs).

Embeddings are a method of tranlating documents into a format that computers can better understand. The embeddings model I'm using is Titan Text Embeddings v2 because it's fast, accurate and integrates well with AWS Services.

Chunking is the process of breaking text into smaller parts called tokens (tokens are essentially words for the model). In my Knowledge Base, chunks are set to the defualt value of generating 300 tokens.

![Image](http://learn.nextwork.org/excited_turquoise_smart_kiwi/uploads/ai-rag-bedrock_p9r2s5t8)

---

## AI Models

AI models are important for my chatbot because the model is the brains behind the chatbot it will essentially convert the KBase results into human-like text responses .Without AI models, my chatbot would only return raw text chunks that it retrieved form the KBase. 

Since AWS has retired the model access system recently, I accessed models directly through the Test Knowledge base feature.

Context: In order to use AI models in Bedrock, you need to explicitly request access from AWS first. This is because:

Some models are expensive to use, so AWS is double checking that you're consciously opting-in to using them.
AWS needs to make sure they have enough capacity i.e. servers in your region to support the model you want to use.
Some models have different rules and conditions that you need to review and accept before you can start using them. For example, Anthropic models need you to fill out a form that tells them what you're going to use the model for.

 But since recently AWS has retired this page and given access to all users directly.

![Image](http://learn.nextwork.org/excited_turquoise_smart_kiwi/uploads/ai-rag-bedrock_model-access-proof)

---

## Syncing the Knowledge Base

Even though I already connected my S3 bucket when creating the Knowledge Base, I still need to sync because setting up the KBase only setup the creates the infrastructure but doesn't actually move the data from my S3 to my KBase

The sync process involves three steps:

1. Ingesting: this is the step of retreving the data from the data source (my S3 bucket).

2. Processing: Bedrock will chunk the data into smaller pieces and emdbed the data The Titan Text Embeddings V2 is responsible for this.

3. Sorting: Bedrock will preprocess the data and store it in the vector store, OpenSearch in this case.

![Image](http://learn.nextwork.org/excited_turquoise_smart_kiwi/uploads/ai-rag-bedrock_sync-screenshot)

---

## Testing My Chatbot

To test my chatbot, the first message I sent was "hello". 
I used the AI model Llama 3.1 8B 

When I asked about topics unrelated to my data, my chatbot responded saying that it cannot help me with this request.
This proves that the model only used the pdfs I uploaded to it to train itslef, and hence couldn't answer any question outsdide the data that was present in my project pdfs.

You can change the response generation setting to Retrieve only: Data Sources. When I tested this, my chatbot responded with the exact text that was present in the pdfs. Here is the response:
1. This project absolutely met our goals and we could see a fully functioning web app by the end.Natasha Ong NextWork Student NextWork.org     Presentation tier     For the presentation tier, we will set...
 2. The second half of the code is all about sending responses back e.g. sucess messages, item not found,     error messages.     The code uses AWS SDK, which is a set of tools that makes it easier for de...

and so on, but the response to the same prompt with the model was: 

NextWork is integrated with AWS services such as S3, DynamoDB, Lambda, and CloudFront.[1][2][3]  
Additionally, it uses AWS SDK to interact with DynamoDB.[4]

![Image](http://learn.nextwork.org/excited_turquoise_smart_kiwi/uploads/ai-rag-bedrock_d5e8f1g2)

---

## Upgrading My Chatbot

In a project extension, I looked into increasing the number of source chunks, which are essentially the number of chunks the chatbot will use to generate its responses. This will improve my chatbot's responses by giving it more data and context to generate it's response.

I also added a custom generation prompt that tells my chatbot to mention the skills the student learnt from the project as well as informed it that it is working with AWS services.

After adjusting the source chunks and the generation prompt, my chatbot's response listed projects from more pdfs. initally source chunks was set to 5 by default and there were no additional generation prompts in place.
Here is the response: (here it read only from 7 pdfs)

The projects listed in the database are Deploy Backend with Kubernetes, Threat Detection with GuardDuty, Presentation tier, Logic tier, Data tier, Exploring DeepSeek, Chatbox, Advanced Navigation, Debugging and Logs, Advanced Browser Configuration, and SQL Injection

Then I changed the source chunks to 10 and it read from  9 pdfs. then I bumped it up to 15 and added the generation prompt to tell me the skills I've learnt and it cited 15 sources and gave a proper list of the skills.

![Image](http://learn.nextwork.org/excited_turquoise_smart_kiwi/uploads/ai-rag-bedrock_improved-response)

---

---
