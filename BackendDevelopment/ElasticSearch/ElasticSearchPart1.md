# Elasticsearch with Spring Boot 

---

## PART 1: ELASTICSEARCH — DETAILED CONCEPT 

---

## 1️⃣ What problem does Elasticsearch solve?

Traditional relational databases such as **PostgreSQL** or **MySQL**:

* Store data in **rows and columns**
* Are optimized for:

  * ACID transactions
  * Data integrity
  * Joins and relationships
* Are **not optimized** for large-scale text search

### Example problem:

```sql
SELECT * FROM products WHERE name LIKE '%iphone%';
```

Issues:

* Slow on large datasets
* No relevance ranking
* No fuzzy or typo-tolerant search
* Limited filtering performance

---

### What Elasticsearch provides:

* Full-text search
* Relevance scoring
* Fast filtering at scale
* Near real-time analytics

> Elasticsearch is a **search engine database**, not a transactional database.

---

## 2️⃣ Core Idea: Inverted Index (Most Important Concept)

Instead of storing data row-wise:

| id | name                      |
| -- | ------------------------- |
| 1  | Spring Boot Elasticsearch |

Elasticsearch builds an **inverted index**:

```
spring → [1]
boot → [1]
elasticsearch → [1]
```

When you search:

```
"spring elastic"
```

Elasticsearch:

* Finds matching documents
* Calculates relevance score
* Returns results ordered by relevance

➡️ This is why Elasticsearch is extremely fast for search.

---

## 3️⃣ Elasticsearch Data Model

| Concept  | Description                                |
| -------- | ------------------------------------------ |
| Index    | Collection of documents (similar to table) |
| Document | JSON object (similar to row)               |
| Field    | Key-value inside document                  |
| Mapping  | Schema definition                          |
| Shard    | Horizontal partition for scalability       |
| Replica  | Copy of shard for fault tolerance          |

---

## 4️⃣ Why Elasticsearch with Spring Boot?

Spring Boot acts as:

* REST API layer
* Business logic layer
* Integration layer with Elasticsearch

Spring Boot communicates with Elasticsearch using **REST APIs**.

---

## 5️⃣ Real-World Architecture (Very Important)

```
Client
  |
Spring Boot REST API
  |
PostgreSQL  ← Source of truth
Elasticsearch ← Search & analytics
```

### Data Flow:

1. Save data in PostgreSQL
2. Sync same data to Elasticsearch
3. Read/search from Elasticsearch
4. Update ES on DB changes (Kafka / async events)

This is known as the **Search Index Pattern**.

---

# PART 2: SPRING BOOT + ELASTICSEARCH IMPLEMENTATION

---

## 6️⃣ Spring Boot Setup

### Maven Dependency

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-elasticsearch</artifactId>
</dependency>
```

---

## 7️⃣ Elasticsearch Configuration

### `application.yml`

```yaml
spring:
  elasticsearch:
    uris: http://localhost:9200
```

Elasticsearch runs as a separate service (local or Docker).

---

## 8️⃣ Define Elasticsearch Document

```java
@Document(indexName = "products")
public class ProductDocument {

    @Id
    private String id;

    @Field(type = FieldType.Text)
    private String name;

    @Field(type = FieldType.Text)
    private String description;

    @Field(type = FieldType.Double)
    private Double price;

    @Field(type = FieldType.Keyword)
    private String category;
}
```

### Key Notes:

* `Text` → analyzed (used for full-text search)
* `Keyword` → exact match (used for filtering)

---

## 9️⃣ Repository Layer

```java
public interface ProductSearchRepository
        extends ElasticsearchRepository<ProductDocument, String> {

    List<ProductDocument> findByNameContaining(String name);

    List<ProductDocument> findByCategory(String category);
}
```

Spring Data automatically converts these methods into Elasticsearch queries.

---

## 🔟 Service Layer

```java
@Service
public class ProductSearchService {

    private final ProductSearchRepository repository;

    public ProductSearchService(ProductSearchRepository repository) {
        this.repository = repository;
    }

    public void indexProduct(ProductDocument product) {
        repository.save(product);
    }

    public List<ProductDocument> searchByName(String keyword) {
        return repository.findByNameContaining(keyword);
    }

    public List<ProductDocument> searchByCategory(String category) {
        return repository.findByCategory(category);
    }
}
```

---

## 1️⃣1️⃣ Controller Layer

```java
@RestController
@RequestMapping("/api/search")
public class ProductSearchController {

    private final ProductSearchService service;

    public ProductSearchController(ProductSearchService service) {
        this.service = service;
    }

    @PostMapping("/index")
    public String index(@RequestBody ProductDocument product) {
        service.indexProduct(product);
        return "Indexed successfully";
    }

    @GetMapping("/name")
    public List<ProductDocument> searchByName(@RequestParam String q) {
        return service.searchByName(q);
    }

    @GetMapping("/category")
    public List<ProductDocument> searchByCategory(@RequestParam String category) {
        return service.searchByCategory(category);
    }
}
```

---

## 1️⃣2️⃣ Advanced Search (Custom Queries)

```java
@Service
public class AdvancedSearchService {

    private final ElasticsearchOperations operations;

    public AdvancedSearchService(ElasticsearchOperations operations) {
        this.operations = operations;
    }

    public List<ProductDocument> search(String keyword) {

        Query query = new NativeSearchQueryBuilder()
                .withQuery(QueryBuilders.multiMatchQuery(
                        keyword, "name", "description"))
                .build();

        return operations
                .search(query, ProductDocument.class)
                .stream()
                .map(SearchHit::getContent)
                .toList();
    }
}
```

Supports:

* Multi-field search
* Relevance scoring
* Fuzzy queries

---

## 1️⃣3️⃣ Keeping Data in Sync (Production Pattern)

```java
@Transactional
public void createProduct(Product product) {
    productRepository.save(product);
    productSearchService.indexProduct(mapToDocument(product));
}
```

Best practice:

* PostgreSQL → primary datastore
* Elasticsearch → secondary search index

---

## 1️⃣4️⃣ Elasticsearch vs Database (Interview Explanation)

> PostgreSQL is the system of record handling transactions and consistency, while Elasticsearch is used as a secondary datastore optimized for full-text search, filtering, and relevance-based queries.

---

## 1️⃣5️⃣ Common Mistakes to Avoid

* ❌ Using Elasticsearch as primary database
* ❌ Expecting ACID transactions
* ❌ Complex joins
* ❌ High-frequency updates without batching

---

## Final One-Line Summary

> Elasticsearch in Spring Boot is used as a distributed, search-optimized datastore that indexes domain data for fast full-text search, relevance ranking, and analytics, while relational databases manage transactional consistency.

---
