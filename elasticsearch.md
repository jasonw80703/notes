# ElasticSearch

**ES is a server that can process JSON requests and give you back JSON data**

Indexing caching layer for fast querying.

Index -> "database"

MySQL -> DB -> Tables -> Cols/Rows
ES -> Indices -> Types -> Documents with Properties

An ES cluster contains multiple indices, which contain multiple Types and hold documents of data with properties.

### Searching and Querying

```
# localhost:9200/[index]/[type]/[operation]
curl -XGET localhost:9200/SubaruFactory/Cars/SubaruImprezza
```
