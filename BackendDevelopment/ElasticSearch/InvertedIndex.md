# Inverted Index in Elasticsearch

An **inverted index** in Elasticsearch is a crucial data structure that maps unique words (terms) to the documents where they appear. It enables lightning-fast full-text searches by reversing the typical document-to-word relationship, allowing instant lookups from a term to its document locations rather than scanning every document.

It’s built by analyzing text into tokens, creating a dictionary of these tokens, and linking each token to a posting's list

## How It Works

**1. Analysis:**  
When you index a document, Elasticsearch analyzes its text fields and breaks them down into individual words (tokens).

**2. Tokenization:**  
- For `text` fields, words are tokenized (e.g., `"Elasticsearch"` becomes `"elasticsearch"`).  
- For `keyword` fields, the whole value is indexed as a single token.

**3. Indexing:**  
Each unique token is added to the inverted index dictionary along with a posting's list

**4. Search:**  
When you search for a term, Elasticsearch looks it up in the inverted index and instantly retrieves the relevant documents, making full-text searches extremely fast.

---

## Example

**Documents:**
- Doc 1: `Elasticsearch is fast`  
- Doc 2: `Fast data retrieval`

**Inverted Index Table:**

| Term         | Documents |
|---------------|------------|
| elasticsearch | Doc 1      |
| is            | Doc 1      |
| fast          | Doc 1, Doc 2 |
| data          | Doc 2      |
| retrieval     | Doc 2      |

This structure allows Elasticsearch to quickly find all documents containing **“fast”** by simply checking its postings list for that term.

---
```

