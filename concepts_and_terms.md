# Attempts to provide some clarity on concepts and terms that are found on the exam.
This isnt in any particular order

## Sub-Aggregations
These allow you to embed aggregations inside other aggregations.<br>
Therefore, sub-aggregations allow continuously refine and separate groups of criteria of interest, then apply metrics at various levels in the aggregation hierarchy to generate your report.
<br>
## Bucket vs Metric Aggregations
The difference is that metric aggregations take the data and aggregate some extracted values. Bucket aggregations on the otherhand just bucket data according to some criteria.
<br>
Metric Aggregations:<br>
The aggregations in this family compute metrics based on values extracted in one way or another from the documents that are being aggregated. The values are typically extracted from the fields of the document (using the field data), but can also be generated using scripts.

Bucket Aggregations:<br>
Bucket aggregations don’t calculate metrics over fields like the metrics aggregations do, but instead, they create buckets of documents. Each bucket is associated with a criterion (depending on the aggregation type) which determines whether or not a document in the current context "falls" into it. In other words, the buckets effectively define document sets. In addition to the buckets themselves, the bucket aggregations also compute and return the number of documents that "fell into" each bucket.

## Index and Index Template
What is an index and index template? What is difference between the 2, when would use 1 vs the other, and benefits of 1 vs the other!

## Dynamic Template
These allow you greater control of how Elasticsearch maps your data beyond the default dynamic field mapping rules. You enable dynamic mapping by setting the dynamic parameter to true or runtime. You can then use dynamic templates to define custom mappings that can be applied to dynamically added fields based on the matching condition

## Index Lifecycle Management
You can configure index lifecycle management (ILM) policies to automatically manage indices according to your performance, resiliency, and retention requirements.

## Analyzer

## Data Stream
A data stream lets you store append-only time series data across multiple indices while giving you a single named resource for requests. Data streams are well-suited for logs, events, metrics, and other continuously generated data.

## Mapping 
Mapping is the process of defining how a document, and the fields it contains, are stored and indexed. <br>
Each document is a collection of fields, which each have their own data type. When mapping your data, you create a mapping definition, which contains a list of fields that are pertinent to the document. <br>
A mapping definition also includes metadata fields, like the _source field, which customize how a document’s associated metadata is handled. <br>

## Multi-Fields
It is often useful to index the same field in different ways for different purposes. For instance, a string field could be mapped as a text field for full-text search, and as a keyword field for sorting or aggregations. Alternatively, you could index a text field with the standard analyzer, the english analyzer, and the french analyzer.
<br>
This is the purpose of multi-fields. Most field types support multi-fields via the fields parameter.

## Ingest Pipeline
Ingest pipelines let you perform common transformations on your data before indexing.  
<br>
A pipeline consists of a series of configurable tasks called processors. Each processor runs sequentially, making specific changes to incoming documents. After the processors have run, Elasticsearch adds the transformed documents to your data stream or index.

## Pagination
Pagination allows you to break up the search results into more manageable chunks.<br> A great way to think of this is that if you do a search engine search with lets say 10 Million results, the search engine will break those results into chunks of 20, before you need to go to the next page. That's pagination.

## Index Aliases
An index alias is a secondary name for one or more indices. Most Elasticsearch APIs accept an alias in place of a data stream or index name.
<br>
You can change the data streams or indices of an alias at any time. If you use aliases in your application’s Elasticsearch requests, you can reindex data with no downtime or changes to your app’s code.

## Search Templates
A search template is a stored search you can run with different variables.

## Term Query
Query the data based upon a term. 

## Phrase Query
Query the data based upon a phrase. 

## Asynchronous Search
Asynchronous search lets you submit a search request that gets executed asynchronously, monitor the progress of the request, and retrieve results at a later stage. You can also retrieve partial results as they become available but before the search has completed.

## Cross Cluster Search
Cross-cluster search lets you run a single search request against one or more remote clusters. For example, you can use a cross-cluster search to filter and analyze log data stored on clusters in different data centers.

## Cross Cluster Replication
Using an active-passive model, cross-cluster replication, you can replicate indices across clusters to for example:<br>
- Continue handling search requests in the event of a datacenter outage <br>
- Prevent search volume from impacting indexing throughput <br>
- Reduce search latency by processing search requests in geo-proximity to the user<br>
 
 
