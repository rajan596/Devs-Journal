# Elastic Search
Elastic Search is popular Open source, distributed Search Engine. Its based on Apache Lucene (which is polular high performance library based on Java for effective storage and retrieval of indexes)

## Introduction
- ES is based on Lucene which supports Horizontal scaling, Replication, Arbitory text Index creation

## Use cases
- Full text search
- Analytics store
- Auto complete, Catalogue product search
- Alerting Engine: Send alerts based on certain criteris
- ELK stack: Elastic search (Storage and Retrieval) + LogStash (Data Processing) + Kibana (Visualization)

## Powers
- Inverted Index. Keyword -> List of original products
- Advanced Text Analysis: It can do tokenization, stemming (converting word to its root form), Synonym support (we can teach ES which all words means the same thing)
- Ranking: It can rank the result by relevance.
- Fuzzy Matching (to solve typo problems) 

# References
- https://vivekbansal.substack.com/p/text-based-search-elasticsearch
- PhonePe
    - https://tech.phonepe.com/managing-elasticsearch-at-scale-at-phonepe-part-1/
    - https://tech.phonepe.com/managing-elasticsearch-at-scale-at-phonepe-part-2/
    - https://tech.phonepe.com/managing-elasticsearch-at-scale-at-phonepe-part-3/
