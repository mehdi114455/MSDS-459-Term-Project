# MSDS 459 — Knowledge Engineering: Term Project

**Course:** MSDS 459 — Knowledge Engineering  
**Term:** Winter 2026  
**Syed Mehdi**  


## Healthcare Competitive Intelligence Knowledge Graph

This repository contains the full term project for MSDS 459, implementing a hybrid knowledge graph system for competitive intelligence in the publicly traded healthcare sector. 
The system integrates financial time-series data, FDA regulatory documents, and global media coverage to support relationship-centric analysis and predictive modeling.



## Project Architecture

The system is built on a dual-storage hybrid architecture:

**PostgreSQL** serves as the primary system of record, storing structured financial time-series data and semi-structured regulatory documents using JSONB fields. 
It provides ACID guarantees, referential integrity, and efficient aggregation.

**Memgraph** serves as a complementary graph-analytical layer. Entities (companies, regulatory documents, news articles, trading days, price observations) are modeled as nodes, 
and semantic relationships such as `MENTIONS` and `HAS_PRICE` are represented as directed edges. 
Data is exported from PostgreSQL into Memgraph, enabling multihop traversal queries that would require complex JOINs in a purely relational system.

This follows a subject–predicate–object triple structure, grounded in the symbolic knowledge representation framework described by Nickel et al. (2015).

---

## Repository Structure

```
.
├── Data/                          # All raw and processed data
│   ├── companies.csv              # Curated list of 18 public healthcare companies
│   ├── financial/yahoo/           # Stock price CSVs per ticker (Yahoo Finance)
│   ├── regulatory/fda_docs.jsonl  # FDA regulatory documents (crawled)
│   └── news/gdelt_articles.jsonl  # GDELT global news articles
│
├── Knowledge Base/                # Knowledge Graph
│   ├── 01_schema.sql              # PostgreSQL schema (tables, indexes, constraints)
│   ├── 02_queries.sql             # Sample analytical SQL queries
│   ├── 01_schema.cypher           # Memgraph node/edge schema (Cypher)
│   ├── 02_queries.cypher          # Sample graph traversal queries (Cypher)
│   ├── docker-compose.yml         # Multi-container setup (Postgres, Memgraph, Loader)
│   ├── Dockerfile.loader          # Loader container image definition
│   └── requirements.txt           # Python dependencies
│
├── Python Notebooks/              # Data collection and processing notebooks
│   ├── Analysis Pipeline.ipynb    # Predictive Model
│   └── Data Crawler.ipynb         # Data crawler
│
├── Technical Paper/               # Term project paper
│   └── Term_Project_Paper.pdf     # Final research paper
│
└── README.md
```

---

## Dataset

| Source | Description | Format |
|---|---|---|
| `companies.csv` | 18 curated public healthcare companies with tickers, sectors, and Wikidata IDs | CSV |
| Yahoo Finance | Daily OHLCV stock price data per company | CSV per ticker |
| FDA Crawler | Regulatory docs (recalls, approvals, enforcement actions) from FDA domains | JSONL |
| GDELT API | Global news articles mentioning target companies | JSONL |

---

## Knowledge Graph Schema

### Node Types
- `Company` — ticker, name, sector, exchange
- `RegulatoryDoc` — FDA documents with URL, title, timestamp, relevance score
- `NewsArticle` — GDELT articles with source, tone, and entity mentions
- `TradingDay` — calendar date
- `PricePoint` — open, high, low, close, adjusted close, volume

### Relationship Types
- `(RegulatoryDoc)-[:MENTIONS]->(Company)`
- `(NewsArticle)-[:MENTIONS]->(Company)`
- `(Company)-[:HAS_PRICE]->(PricePoint)`
- `(PricePoint)-[:ON_DATE]->(TradingDay)`

## Prerequisites

- **Docker Desktop** — [Download here](https://www.docker.com/products/docker-desktop/)
- **Python 3.9+** (for running scripts outside Docker)

---

## References

Nickel, M., Murphy, K., Tresp, V., & Gabrilovich, E. (2015). A review of relational machine learning for knowledge graphs. *Proceedings of the IEEE*, 104(1), 11–33. https://arxiv.org/abs/1503.00759
