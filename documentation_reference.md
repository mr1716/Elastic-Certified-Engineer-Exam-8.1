# Documentation References For Topics On Exam

## Data Management 
https://www.elastic.co/guide/en/elasticsearch/reference/current/data-management.html

Define an index that satisfies a given set of requirements <br>
https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-create-index.html

Define and use an index template for a given pattern that satisfies a given set of requirements
https://www.elastic.co/guide/en/elasticsearch/reference/8.1/mapping.html <br>
https://www.elastic.co/guide/en/elasticsearch/reference/8.1/index-templates.html

Define and use a dynamic template that satisfies a given set of requirements
https://www.elastic.co/guide/en/elasticsearch/reference/8.1/dynamic-templates.html

Define an Index Lifecycle Management policy for a time-series index <br>
https://www.elastic.co/guide/en/elasticsearch/reference/8.8/set-up-lifecycle-policy.html

Define an index template that creates a new data stream <br>
https://www.elastic.co/guide/en/elasticsearch/reference/current/set-up-a-data-stream.html
https://www.elastic.co/guide/en/elasticsearch/reference/current/data-streams.html 

## Searching Data 
Write and execute a search query for terms and/or phrases in one or more fields of an index
https://www.elastic.co/guide/en/elasticsearch/reference/8.1/query-dsl-match-query.html
https://www.elastic.co/guide/en/elasticsearch/reference/8.1/query-dsl-match-query-phrase.html
https://www.elastic.co/guide/en/elasticsearch/reference/8.1/query-dsl-multi-match-query.html

Term Level / Phrase queries <br>
https://www.elastic.co/guide/en/elasticsearch/reference/8.1/term-level-queries.html
https://www.elastic.co/guide/en/elasticsearch/reference/8.1/full-text-queries.html
https://www.elastic.co/guide/en/elasticsearch/reference/8.1/query-dsl-match-query-phrase.html
https://www.elastic.co/guide/en/elasticsearch/reference/8.1/query-dsl-wildcard-query.html
https://www.elastic.co/guide/en/elasticsearch/reference/8.1/query-dsl-regexp-query.html
https://www.elastic.co/guide/en/elasticsearch/reference/8.1/query-dsl-range-query.html

Write and execute a search query that is a Boolean combination of multiple queries and filters
https://www.elastic.co/guide/en/elasticsearch/reference/8.1/compound-queries.html
https://www.elastic.co/guide/en/elasticsearch/reference/8.1/query-dsl-bool-query.html

Write an asynchronous search
https://www.elastic.co/guide/en/elasticsearch/reference/8.1/async-search.html

Write and execute metric and bucket aggregations <br>
https://www.elastic.co/guide/en/elasticsearch/reference/8.1/search-aggregations.html
https://www.elastic.co/guide/en/elasticsearch/reference/8.1/search-aggregations.html#return-only-agg-results
https://www.elastic.co/guide/en/elasticsearch/reference/8.1/search-aggregations-metrics.html
https://www.elastic.co/guide/en/elasticsearch/reference/8.1/search-aggregations-metrics-extendedstats-aggregation.html
https://www.elastic.co/guide/en/elasticsearch/reference/8.1/search-aggregations-bucket.html
https://www.elastic.co/guide/en/elasticsearch/reference/8.1/search-aggregations-pipeline.html

Write and execute a query that searches across multiple clusters <br>
https://www.elastic.co/guide/en/elasticsearch/reference/8.1/search-search.html

Write and execute a search that utilizes a runtime field <br>

## Developing Search Applications 
Highlight the search terms in the response of a query <br>
https://www.elastic.co/guide/en/elasticsearch/reference/8.1/highlighting.html

Sort the results of a query by a given set of requirements <br>
https://www.elastic.co/guide/en/elasticsearch/reference/8.1/sort-search-results.html

Implement pagination of the results of a search query <br>
https://www.elastic.co/guide/en/elasticsearch/reference/8.1/search-request-from-size.html <br>
https://www.elastic.co/guide/en/elasticsearch/reference/8.1/paginate-search-results.html <br>

Define and use index aliases <br>
https://www.elastic.co/guide/en/elasticsearch/reference/8.1/indices-aliases.html <br>

Define and use a search template <br>
https://www.elastic.co/guide/en/elasticsearch/reference/8.1/search-template.html <br>

## Data Processing 
Define a mapping that satisfies a given set of requirements <br>
https://www.elastic.co/guide/en/elasticsearch/reference/8.1/mapping.html <br>
https://www.elastic.co/guide/en/elasticsearch/reference/8.1/mapping-types.html <br>
https://www.elastic.co/guide/en/elasticsearch/reference/8.1/doc-values.html <br>

Define and use a custom analyzer that satisfies a given set of requirements <br>
and <br>
Define and use multi-fields with different data types and/or analyzers <br>
https://www.elastic.co/guide/en/elasticsearch/reference/8.1/mapping-types.html <br>
https://www.elastic.co/guide/en/elasticsearch/reference/8.1/analyzer.html <br>
https://www.elastic.co/guide/en/elasticsearch/reference/8.1/analysis-standard-analyzer.html <br>
https://www.elastic.co/guide/en/elasticsearch/reference/8.1/analysis-custom-analyzer.html#_configuration <br>

Use the Reindex API and Update By Query API to reindex and/or update documents <br>
https://www.elastic.co/guide/en/elasticsearch/reference/8.1/docs-update-by-query.html <br>

Define and use an ingest pipeline that satisfies a given set of requirements, including the use of Painless to modify documents <br>
https://www.elastic.co/guide/en/elasticsearch/reference/8.1/ingest.html <br>
https://www.elastic.co/guide/en/elasticsearch/reference/8.1/ingest-apis.html <br>
https://www.elastic.co/guide/en/elasticsearch/reference/8.1/ingest.html#access-source-fields <br>
https://www.elastic.co/guide/en/elasticsearch/reference/8.1/ingest.html#access-metadata-fields <br>
https://www.elastic.co/guide/en/elasticsearch/reference/8.1/ingest.html#access-ingest-metadata  <br>
https://www.elastic.co/guide/en/elasticsearch/reference/8.1/processors.html <br>
https://www.elastic.co/guide/en/elasticsearch/reference/8.1/script-processor.html <br>
https://www.elastic.co/guide/en/elasticsearch/reference/8.1/set-processor.html <br>
https://www.elastic.co/guide/en/elasticsearch/reference/8.1/append-processor.html <br>

Configure an index so that it properly maintains the relationships of nested arrays of objects <br>
https://www.elastic.co/guide/en/elasticsearch/reference/8.1/nested.html  <br>

## Cluster Management 
Diagnose shard issues and repair a cluster's health <br>
https://www.elastic.co/guide/en/elasticsearch/reference/8.1/cluster-health.html <br>
https://www.elastic.co/guide/en/elasticsearch/reference/8.1/cat-health.html <br>
https://www.elastic.co/guide/en/elasticsearch/reference/8.1/cat-indices.html <br>
https://www.elastic.co/guide/en/elasticsearch/reference/8.1/cluster-allocation-explain.html <br>
https://www.elastic.co/guide/en/elasticsearch/reference/8.1/cat-shards.html <br>

Backup and restore a cluster and/or specific indices <br>
https://www.elastic.co/guide/en/elasticsearch/reference/8.1/snapshots-take-snapshot.html <br>
https://www.elastic.co/guide/en/elasticsearch/reference/8.1/modules-snapshots.html (references other documentation) <br>
https://www.elastic.co/guide/en/elasticsearch/reference/8.1/snapshot-restore.html <br>
https://www.elastic.co/guide/en/elasticsearch/reference/8.1/snapshots-register-repository.html#self-managed-repo-types <br>
https://www.elastic.co/guide/en/elasticsearch/reference/8.1/restore-snapshot-api.html#restore-snapshot-api-index-settings <br>
https://www.elastic.co/guide/en/elasticsearch/reference/8.1/snapshot-restore.html <br>
https://www.elastic.co/guide/en/elasticsearch/reference/8.1/snapshots-register-repository.html <br>
https://www.elastic.co/guide/en/elasticsearch/reference/8.1/snapshot-restore.html <br>
https://www.elastic.co/guide/en/elasticsearch/reference/8.1/api-conventions.html#api-date-math-index-names <br>
https://www.elastic.co/guide/en/elasticsearch/reference/8.1/snapshots-take-snapshot.html#back-up-config-files <br>
https://www.elastic.co/guide/en/elasticsearch/reference/8.1/security-backup.html (just a reference to the following 2 links) <br>
https://www.elastic.co/guide/en/elasticsearch/reference/8.1/snapshots-take-snapshot.html#back-up-config-files <br>
https://www.elastic.co/guide/en/elasticsearch/reference/8.1/snapshots-take-snapshot.html#cluster-state-snapshots <br>
https://www.elastic.co/guide/en/elasticsearch/reference/7.13/restore-security-configuration.html <br>

Configure a snapshot to be searchable <br>
https://www.elastic.co/guide/en/elasticsearch/reference/8.1/searchable-snapshots.html <br>
https://www.elastic.co/guide/en/elasticsearch/reference/8.1/ilm-searchable-snapshot.html <br>
https://www.elastic.co/guide/en/elasticsearch/reference/8.1/searchable-snapshots-api-mount-snapshot.html <br>

Configure a cluster for cross cluster search <br>
https://www.elastic.co/guide/en/elasticsearch/reference/8.1/modules-cross-cluster-search.html <br>

Implement cross-cluster replication <br>
https://www.elastic.co/guide/en/elasticsearch/reference/8.1/xpack-ccr.html <br>
https://www.elastic.co/guide/en/elasticsearch/reference/8.1/ccr-getting-started.html (reference to the following 2 links) <br>
https://www.elastic.co/guide/en/elasticsearch/reference/8.1/ccr-getting-started-tutorial.html <br>
https://www.elastic.co/guide/en/elasticsearch/reference/8.1/remote-clusters-connect.html <br>
https://www.elastic.co/guide/en/elasticsearch/reference/8.1/built-in-roles.html <br>
https://www.elastic.co/guide/en/elasticsearch/reference/8.1/security-privileges.html <br>


## Helpful terms <br>
What is a datastream? <br>
https://www.elastic.co/guide/en/elasticsearch/reference/current/data-streams.html
