---
tags:
  - tech/RAG
---
Youtube: [RAG Crash Course for Beginners](https://www.youtube.com/watch?v=swvzKSOEluc)

---
RAG (Retrieval-Augmented Generation)
- Definition: a method 
- where an AI model first retrieves relevant information from **external sources** (documents, databases, web pages)
- then uses that **retrieved content** to generate a more **accurate** answer.


Q: How to get better responses?
- Prompt engineering (Improve results by writing bette)
- Fine-tuning: from train model
	- *Train* a pre-trained model further on a smaller, task-specific dataset
	- better for communication style
- RAG: knowledge from documents/database

- Q: When no to RAG?
	- If the main goal is not add missing knowledge via external documents
	- but to keep the model's output highly controlled and consistent, like
		- strict output schema (JSON), 
		- stable tone, 
		- or a fixed template,
		- prompting or fine-tuning is usually better.

Q: How we find relevant chunks? Retrieval
- **Lexical Search (keyword-based):** matches query terms with documents and ranks by term overlap.
	 - TF-IDF (Term Frequency – Inverse Document Frequency) 
		  - weights terms higher if they are **frequent** in a doc but rare in the corpus.
	- BM25: improves TF-IDF with TF saturation + document-length normalization.
		- If a term appears **many** times, BM25 gives diminishing returns (repeating the same word doesn’t keep boosting the score a lot).
		- If the chunk/document is *long*, BM25 reduces the score a bit, because long text naturally contains more words and shouldn’t get an unfair advantage.
- **Semantic Search**
	- Embedding retrieval:
		- ranks by meaning similarity, not exact word overlap (handles paraphrases/synonyms).

