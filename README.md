# Description

This is my implementation of an LLM model for answering hard science multiple-choice questions (MCQ) based on Retrieval Augmented Generation (RAG). The inspiration for this project came from Kaggle's `[LLM Science Exam](https://www.kaggle.com/competitions/kaggle-llm-science-exam/overview)' competition. Some help was taken from [this](https://www.youtube.com/watch?v=nvvuTiE4BEk&t=1s) video of the YouTube channel *DataScienceCastnet* and a couple of other videos.

This solution is comprised of 3 steps:

1) Take a dataset of wikipedia articles on various STEM topics and convert each article into an embedding of fixed-size.
2) Take a dataset of many science MCQs and their relevant wikipedia text and train a 
