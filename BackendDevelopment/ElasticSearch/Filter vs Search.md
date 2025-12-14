# Elasticsearch: Search vs Filter (Product Example)

---

## Core Idea

> **Search is for relevance. Filter is for correctness.**
> The decision between **search** and **filter** is made **at query time**, not at index definition time.

---

## Product Field Setup

```java
name        → Text        // used for search
description → Text        // used for search
category    → Keyword    // used for filter
price       → Double     // used for filter
```

---

## 1️⃣ SEARCH — Full‑Text, Relevance‑Based

### When to use SEARCH

Use **search** when:

* User enters free‑text
* Relevance ranking matters
* Partial matches are required
* Fuzzy / typo tolerance is needed

### Example User Input

```
"iphone 14"
```

---

### Elasticsearch Search Query

```json
{
  "query": {
    "match": {
      "name": "iphone 14"
    }
  }
}
```

**Meaning:**
Find products whose **name text matches** `iphone 14` and rank them by relevance.

---

### Spring Boot (Search Query)

```java
Query query = new NativeSearchQueryBuilder()
        .withQuery(QueryBuilders.matchQuery("name", "iphone 14"))
        .build();
```

### Why this is SEARCH

* Uses analyzed text
* Computes relevance score
* Supports partial & fuzzy matches

---

## 2️⃣ FILTER — Exact Match, No Scoring

### When to use FILTER

Use **filter** when:

* Value must match exactly
* No relevance scoring is needed
* Performance matters
* Field comes from dropdowns / checkboxes

### Example Filters

```
category = electronics
price < 1000
```

---

### Elasticsearch Filter Query

```json
{
  "query": {
    "term": {
      "category": "electronics"
    }
  }
}
```

**Meaning:**
Return only products where category exactly equals `electronics`.

---

### Spring Boot (Filter Query)

```java
Query query = new NativeSearchQueryBuilder()
        .withFilter(QueryBuilders.termQuery("category", "electronics"))
        .build();
```

### Why this is FILTER

* No scoring
* Exact match
* Faster execution
* Cache‑friendly

---

## 3️⃣ Real‑World Query: SEARCH + FILTER (Most Common)

### User Requirement

> “Search iPhone products, but only electronics under $1000”

---

### Elasticsearch Bool Query

```json
{
  "query": {
    "bool": {
      "must": {
        "match": {
          "name": "iphone"
        }
      },
      "filter": [
        { "term": { "category": "electronics" }},
        { "range": { "price": { "lte": 1000 }}}
      ]
    }
  }
}
```

---

### Spring Boot Implementation

```java
Query query = new NativeSearchQueryBuilder()
        .withQuery(
            QueryBuilders.boolQuery()
                .must(QueryBuilders.matchQuery("name", "iphone")) // SEARCH
                .filter(QueryBuilders.termQuery("category", "electronics")) // FILTER
                .filter(QueryBuilders.rangeQuery("price").lte(1000)) // FILTER
        )
        .build();
```

---

## 4️⃣ Common Mistake: Using FILTER for Text Search

❌ **Wrong**

```json
"filter": {
  "term": {
    "name": "iphone"
  }
}
```

### Why this is wrong

* `term` expects exact match
* No tokenization
* No relevance scoring
* Partial matches fail

---

## 5️⃣ Common Mistake: Using SEARCH for Filters

❌ **Wrong**

```json
"match": {
  "category": "electronics"
}
```

### Why this is wrong

* Category is not free text
* Scoring is unnecessary
* Slower than filter queries

---

## 6️⃣ Interview‑Ready Explanation

> “In Elasticsearch, search vs filter is decided at query time. We use `match` queries for full‑text fields like product name to leverage relevance scoring, and `term` or `range` queries inside filters for exact‑match fields like category and price. This ensures both relevance and performance.”

---

## 7️⃣ One‑Line Rules to Remember

| Use Case                        | Use                      |
| ------------------------------- | ------------------------ |
| Free‑text input                 | SEARCH (`match`)         |
| Dropdown / checkbox             | FILTER (`term`, `range`) |
| Relevance ranking               | SEARCH                   |
| Exact match                     | FILTER                   |
| Performance‑critical conditions | FILTER                   |

---

## Final Takeaway

> **Search decides *what is relevant*. Filter decides *what is allowed*.**
> Both are combined at query time to build efficient, real‑world Elasticsearch queries.

---
