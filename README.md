# Description

This is my implementation of an LLM model for answering hard science multiple-choice questions (MCQs) based on Retrieval Augmented Generation (RAG). The inspiration for this project came from Kaggle's `[LLM Science Exam](https://www.kaggle.com/competitions/kaggle-llm-science-exam/overview)' competition. Some help was taken from [this](https://www.youtube.com/watch?v=nvvuTiE4BEk&t=1s) video of the YouTube channel *DataScienceCastnet* and a couple of other videos.

This solution is comprised of 3 steps:

1) Take a dataset of Wikipedia articles on various STEM topics and convert each article into an embedding of fixed-size.
2) Take a dataset of many science MCQs and their relevant Wikipedia text and train a BERT-like encoder to choose the correct option out of 5 choices with the Wikipedia text as the context.
3) Now for a new MCQ question, find the Wikipedia article (from step 1) most likely to contain the relevant context for the question, and let the model trained in step 2 answer the question with this added context.

The details of each step are explained below:

### 1) Generating embeddings of STEM Wikipedia articles

For generating embeddings, the dataset [wiki-20220301-en-sci](https://www.kaggle.com/datasets/nbroad/wiki-20220301-en-sci) which contains about 130000 Wikipedia articles was used. After a cleaning process that removes the references, external links etc, each article was converted into a 768-dimensional embedding by the sentence transformer [multi-qa-mpnet-base-dot-v1](https://huggingface.co/sentence-transformers/multi-qa-mpnet-base-dot-v1). The embeddings of all the articles are stored [here](https://www.kaggle.com/datasets/garvitagarwal/retrieval-wiki-embeddings).

### 2) Fine-tuning an encoder for MCQ answering task

As the base encoder, the [deberta-v3-base](https://huggingface.co/microsoft/deberta-v3-base) model was used. Parameter efficient fine-tuning (PEFT) was done using LoRA (low-rank adaption) with rank = 8 and alpha = 8.

The training dataset was taken from [15k_gpt3.5-turbo.csv](https://www.kaggle.com/datasets/mgoksu/llm-science-exam-dataset-w-context-extended) that contains 15k examples of STEM MCQs with contexts and their correct answers.

The fine-tuned model was saved [here](https://www.kaggle.com/datasets/garvitagarwal/science-comp-trained-model).

### 3) Bring everything together and answer questions

This RAG model was evaluated on the [200 STEM MCQ](https://www.kaggle.com/competitions/kaggle-llm-science-exam/data?select=train.csv) questions provided in the LLM Science Exam Competition. For a given question, the question statement was turned into a sentence embedding again using the *multi-qa-mpnet-base-dot-v1* model. Next, the 10 most similar Wikipedia articles were identified using the nearest neighbor search functionality of the [Facebook AI Similarity Search (Faiss)](https://engineering.fb.com/2017/03/29/data-infrastructure/faiss-a-library-for-efficient-similarity-search/) library. 

**Re-ranking** of these 10 articles was done using the [bge-reranker-base](https://huggingface.co/BAAI/bge-reranker-base) model. After re-ranking the most similar article was used as the context to the fine-tuned *deberta-v3-base* model and the scores assigned by it to the 5 options were recorded. 

