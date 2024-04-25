# Description

This is my implementation of an LLM model for answering hard science multiple-choice questions (MCQs) based on Retrieval Augmented Generation (RAG). The inspiration for this project came from Kaggle's `[LLM Science Exam](https://www.kaggle.com/competitions/kaggle-llm-science-exam/overview)' competition. Some help was taken from [this](https://www.youtube.com/watch?v=nvvuTiE4BEk&t=1s) video of the YouTube channel *DataScienceCastnet* and a couple of other videos.

This solution is comprised of 3 steps:

1) Take a dataset of Wikipedia articles on various STEM topics and convert each article into an embedding of fixed-size.
2) Take a dataset of many science MCQs and their relevant Wikipedia text and train a BERT-like encoder to choose the correct option out of 5 choices with the Wikipedia text as the context.
3) Now for a new MCQ question, find the Wikipedia article (from step 1) most likely to contain the relevant context for the question, and let the model trained in step 2 answer the question with this added context.

The details of each step are explained below:

### 1) Generating embeddings of STEM Wikipedia articles

I used [this](https://www.kaggle.com/datasets/nbroad/wiki-20220301-en-sci) dataset of about 130000 Wikipedia articles. After a cleaning process that removes the references, external links etc, each article was converted into a 768-dimensional embedding by the sentence transformer [multi-qa-mpnet-base-dot-v1](https://huggingface.co/sentence-transformers/multi-qa-mpnet-base-dot-v1). The embeddings of all the articles are stored in the `retrieval-wiki-embeddings' folder.

### 2) Fine-tuning an encoder for MCQ answering task




