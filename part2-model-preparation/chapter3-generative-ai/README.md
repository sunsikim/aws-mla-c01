# Generative AI on AWS

Bedrock provides unified, serverless API for generative AI foundation models like OpenAI does with ChatGPT(to say, it is what Azure is to OpenAI). IAM user(not root user) with appropriate permission can select among following API endpoints for application development:

* **bedrock** : manage, deploy and train models
* **bedrock-runtime** : inference against these models(ex. Converse, InvokeModel)
* **bedrock-agent** : augmented bedrock with custom extensions like web or vector DB
* **bedrock-agent-runtime** : inference against Bedrock agent

Before anything, IAM user should first request access to the model(as in huggingface). By doing so, account will pay price to the third party vendors through AWS. After that, following functionalities can be utilized. Note that utilizable models are not limited to the list that Bedrock model catalog provides; user can use their own model by importing it from S3 or Sagemaker.

1. [Bedrock Playground](#bedrock-playground)
1. [Fine tuning](#fine-tuning)
1. [Bedrock Knowledge Base](#bedrock-knowledge-bases-retrieval-augemented-generationrag)
1. [Bedrock Guardrails](#bedrock-guardrails)
1. [LLM Agents](#llm-agents)

## Bedrock Playground

This is OpenAI chat web UI equivalent where user can try out some prompts without any complicated configuration.

## Fine-tuning

Expensive in short-run, but saves input token since it would have learned the proper way to respond to certain task instruction, so gets cost effective in the long run. 

* **custom models** : this is terminology for fine-tuned models in Bedrock, and only models from Titan, Cohere and Meta can be customized for now.
* **VPC with PrivateLink** : extra security option when building custom model in Bedrock using sensitive private data.
* **continued pre-training** : another option that user can choose with unlabeled data to continue masked mlm.

## Bedrock Knowledge Base: Retrieval-Augemented Generation(RAG)

Open book exam for LLM where answers lies in external database. General characteristic of RAG can be summarized as:

* Pros
    * faster and cheaper way to *incorporate new/proprietary information* into GenAI compared to fine-tuning. 
    * simplifies information update to *simplified down to vector DB update*(otherwise, user should fine-tune model)
* Cons
    * retrieved information has to be included in the prompt, so *input token usage can rise*. 
    * even though precise information is upserted, *result is none deterministic* because the result is generated from varying prompt.

These are some AWS specific contents.

* **general workflow** : user query -> semantic search on OpenSearch -> augmented user query -> Bedrock -> response
* **encoding service** : *Cohere* or *Amazon Titan* as vector encoder whose dimension and chunking strategy is customizable.
* **vector storage** : In AWS ecosystem, OpenSearch is used as vector storage and takes embeddings of data(ex. text, JSON) in S3 as input.

To add more details on chunking strategy on top of naive default strategy(data is chunked by 300 tokens):

* **semantic chunking** : uses foundational model to group relevant sentences
* **hierarchical chunking** : uses smaller chunks as children of a larger chunk
* **lambda-based** : use Lambda function where custom strategy is implemented

## Bedrock Guardrails

Currently works with text foundation models to filter out malicious output to be provided to clients.

* word, topic level filtering can be enabled, and PII removal is also possible
* contextual grounding check minimizes hallucination based on grounding and relevance measure

## LLM Agents

This is a way to attach functions to LLM by giving description on which functions to use for what purpose.

1. client request arrives to agent core 
1. planning module decides the (Lambda) function to use
1. result of the function reinforces the prompt and returns final response to client

For such high level outline to be realized, implementation of the planning module is crucial. How it is implemented in AWS is:

* **action group** : pair of prompt(describes when to use) and lambda function
* **lambda implementation** : function must accompany parameters with clear description(+. name, type, nullable), which will be used to extract value from the prompt
* **knowledge bases incorporation** : given description on when to use it, agent will search it when the condition is met

When implemented, create an alias(i.e. endpoint) for an agent with two throughput options: (1) on-demand throughput (2) provisioned throughput. Then, use `InvokeAgent` request on this alias ID on Bedrock agent runtime endpoint.