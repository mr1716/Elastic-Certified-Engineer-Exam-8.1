# A Unified Example
## Goal
The goal of this is to provide a unified and easy example for someone to go through and be able to study for the Elasticsearch Engineer exam using unified data.

### Notes:
If you dont want to use 2 environments and perform the cross cluster work, then you can skip those sections and only configure 1 Elasticsearch machine. 
### What this does cover:
Everything else on the topic list for the 8.1 exam as of June 15th, 2023. 

# Getting Started
### Environment Setup
To setup the environment, install the version of Elasticsearch that the exam requires. This is version 8.1. There are 2 ways to configure your environment, with each method having their own benefits.
1) Docker: <br>
The docker instance for 8.1.0 can be found at: https://hub.docker.com/layers/library/elasticsearch/8.1.0/images/sha256-cb74057b1647351b128a4417510c6ce398d8e5d5db16be2bc305fbafaf2f4a87?context=explore

2) Manually Install <br>
To install Elasticsearch on a system such as VM or laptop or desktop, download the release from Elasticsearch, ensure that the required version of Java and other required software dependencies are installed, and then install and start Elasticsearch. https://www.elastic.co/downloads/past-releases/elasticsearch-8-1-0


## Lets Get Started:
This example will walk you through the majority of the Elasticsearch Certified Engineer Exam using some 2024 solar eclipse totality information for state parks. This will start with setting up multiple clusters, which wont be necessary for the exam, since all you will need to do is start the systems. 
<br>
The way that this will work is that it will walk you through creating an environment and then help you perform the steps to perform the tasks for the exam.

# Configuring The Environment (Multiple Clusters)
### You might need to configure multiple clusters on the exam, but rather cross cluster searches and cross-cluster replication. 
<b> There are other cluster management topics that dont necessarily require multiple clusters such as:
1) Diagnose shard issues and repair a cluster's health
2) Backup and restore a cluster and/or specific indices
3) Configure a snapshot to be searchable
</b>

### Configuring Multi-Node Elasticsearch Cluster
1) Install elasticsearch on both hosts
2) On each node, open the Elasticsearch configuration file and set the name of your Elasticsearch cluster.
3) Set Descriptive Names
4) Disable Memory Swapping
5) Define Roles Of Elasticsearch Nodes
6) Bind Elasticsearch to Non-loopback Address
7) Discovery and cluster formation settings
8) Set JVM Heap Size
9) Validate Maximum Processes And Open File Descriptor
10) Ensure Enough Virtual Memory
11) Ensure To Open Elasticsearch Ports On Firewall such as FirewallD 
12) Run Elasticsearch
Follow the steps at to configure the cluster <br>
https://kifarunix.com/setup-multi-node-elasticsearch-cluster

# Configuration Using Exam Topics
### Check Cluster Health \*
Check The Cluster Health to verify that the cluster is properly configured.

```json
GET _cluster/health
```
OR
```json
GET _cat/health?v
```
### Find Broken Indices \*
Find broken indices by checking them all, or which ones are red, yellow, and green.
To do this, ensure that index that you created is NOT broken and is green!

```json
GET /_cat/indices
GET _cat/indices?health=red
GET _cat/indices?health=yellow
GET _cat/indices?health=green
```
### Explaining Index Health 
```json
GET _cluster/allocation/explain
{
  "index": "broken_index"
}
```

### View the shard allocation for the index
```json

GET _cat/shards/broken_index?v&s=index

```
### Repair And Index
Repairing an index can be done in many ways such as reducing the number of replicas required. This would be done to matche the number of replia nodes available. In the event that a single node cluster is running, the number of replicas can be set to 0. 

This might not need to be repaired, but if you do notice issues, here's how you would do it!

```json
PUT /broken_index/_settings
{
    "number_of_replicas": 0
    
}
```

### Backup and restore a cluster and/or specific indices 

### Configure a snapshot to be searchable 
Searchable snapshots let you use snapshots to search infrequently accessed and read-only data in a very cost-effective fashion. The cold and frozen data tiers use searchable snapshots to reduce your storage and operating costs.

Searchable snapshots eliminate the need for replica shards, potentially halving the local storage needed to search your data. Searchable snapshots rely on the same snapshot mechanism you already use for backups and have minimal impact on your snapshot repository storage costs
```json
xpack.searchable.snapshot.shared_cache.size=100mb
```

### Cross Cluster Replication
(how is this done? insert here)

### Define an index that satisfies a given set of requirements
Insert explanation here
```json
  PUT totality-2024-raw
  {
    "settings": {
      "number_of_shards": 1,
      "number_of_replicas": 0
    }
  }
```
The expected response should be similar to:
```json
{
  "acknowleged": true,
  "shards_acknowleged": true,
  "index": "totality-2024-raw"
}
```
### Define and use an index template for a given pattern that satisfies a given set of requirements
What we will do is create several different index template
Create an index for totality for everything. And for each state


Question: Create a template for the state parks that includes:
name, street address, city, state, zipcode, coverage %, eclipse date, totality minutes, totality seconds, start time, maximum totality time, and end of the totality time.
```json
PUT _template/totality_2024-tmpl
{
  "index_patterns": ["totality-2024-*"],
  "settings": {
    "number_of_shards": 1,
    "number_of_replicas": 0
  },
  "mappings": {
    "_source": {
      "enabled": false
    },
    "properties": {
    "name" :  { "type": "keyword" },
    "street_address" :  { "type": "keyword" },
    "city" :  { "type": "keyword" },    
    "state" :  { "type": "keyword" },
    "zip_code" :  { "type": "keyword" },    
    "coverage" :  { "type": "keyword" },
    "eclipse_date" :  { "type": "date" },
    "totality_minutes" :  { "type": "integer" },
    "totality_seconds" :  { "type": "integer" },
    "start_time" :  { "type": "text" },
    "max_time" :  { "type": "text" },
    "end_time" :  { "type": "text" }
    }
  }
}
```
Bonus/Other Questions:
Question: Create a template for the state parks that are in totality that includes the mapping properties from above but add in the following: <br> state abbreviation <br>
```json

    "properties": {
    ...
    "state_abbrev" :  { "type": "keyword" },
    ...
```

Verify The results:
```json
GET _template/totality-2024-tmpl

//output
{
  "totality-2024-tmpl": {
    "order": 0,
    "index_patterns": [
      "totality-*"
    ],
    "settings": {
      "index": {
        "number_of_shards": "1",
        "number_of_replicas": "0"
      }
    },
    "mappings": {
      "_source": {
        "enabled": false
      },
      "properties": {
        "coverage": {
          "type": "keyword"
        },
        "street_address": {
          "type": "keyword"
        },
        "eclipse_date": {
          "type": "date"
        },
        "start_time": {
          "type": "date"
        },
        "totality_minutes": {
          "type": "integer"
        },
        "city": {
          "type": "keyword"
        },
        "totality_seconds": {
          "type": "integer"
        },
        "name": {
          "type": "keyword"
        },
        "end_time": {
          "type": "date"
        },
        "state": {
          "type": "keyword"
        },
        "max_time": {
          "type": "date"
        },
        "zip_code": {
          "type": "keyword"
        }
      }
    },
    "aliases": {}
  }
}
```

### Define and use a dynamic template that satisfies a given set of requirements
This isnt a greatest example, but it works.This doesnt do the best job of showing what it can do, but the thing is that these are used instead of the tempalte above to create a more dynamic template and index<br>
What we can do is use a dynamic template to define the totality minutes and seconds fields as longs, from strings.
```json
PUT dynamic-totality-raw
{
  "mappings": {
    "dynamic_templates": [
      {
        "longs_as_strings": {
          "match_mapping_type": "string",
          "match":   "totality_*",
          "unmatch": "*_text",
          "mapping": {
            "type": "long"
          }
        }
      }
    ]
  }
}
//Output
{
  "_index" : "dynamic-totality-raw22",
  "_id" : "1",
  "_version" : 1,
  "result" : "created",
  "_shards" : {
    "total" : 2,
    "successful" : 1,
    "failed" : 0
  },
  "_seq_no" : 0,
  "_primary_term" : 1
}
```
The next step is to add in data before the final step of verifying that it worked as intended.
'''json
PUT dynamic-totality-raw/_doc/1
{
  "age": 22,
  "totality_minutes": "5", 
  "totality_seconds": "0" ,
  "email": "fake@domain.com",
  "name":"Boaty McBoatface"
}
'''

### Lets Upload The Data
If running this in a single node environment, use the file named full-eclipse-data.json for the examples. It makes performing the examples significantly easier. Plus, the examples were designed for that purpose. A cross-cluster implementation will be implemented later. Will require copying the work done to the other clusters. <br>
The file named full-eclipse-data.json has all of the data we're going to use. It is found [here]([https://github.com/mr1716/Elastic-Certified-Engineer-Exam-8.1/blob/main/solar_eclipse_2024.json](https://github.com/mr1716/Elastic-Certified-Engineer-Exam-8.1/blob/main/example-date/full-eclipse-data.json)) <br> 

To find the data broken down by state into each line, check out the file unique-clusters.json [here](https://github.com/mr1716/Elastic-Certified-Engineer-Exam-8.1/blob/main/example-date/unique-clusters.json) <br> The benefit of this is that is broken down by each state each line. DO NOT attempt to upload this as 1 file. It is meant to be used to upload 1 line per cluster and provide the benfits of cross cluster search. <br>

#### To upload this into elasticsearch, run: <br>
 curl -k -u "elastic:Password01" -s -H "Content-Type: application/x-ndjson" -XPUT localhost:9200/totality_info/_bulk --data-binary "@full-eclipse-data.json"; echo

<br>
The reason for this is to be able to practice with cross cluster replication and cross cluster search!

### Define and use index aliases \*
### part 1

:question: Define an index alias for `totality-raw` called `totality-all`

```json
POST /_aliases
{
  "actions": [
    {
      "add": {
        "index": "totality-raw",
        "alias": "totality-all"
      }
    }
  ]
}
//output:
{
  "acknowledged": true
}
```

To verify: <br>

Check that the document count matches using 
```json
GET totality-all/_count

//output
{
  "count": 0,
  "_shards": {
    "total": 1,
    "successful": 1,
    "skipped": 0,
    "failed": 0
  }
}
```
### part 2

:question: Define an index alias for `totality-raw` called `totality-full`

:question: Apply a filter to only show the state parks where the totality is 100%.
Alternatives include aliases for each state, zipcode, states with totality of X minutes, or Timezones.

1. check that the field you want to filter is a keyword
```json
GET totality-raw/_mapping/field/coverage

// Output 

{
  "totality-raw": {
    "mappings": {
      "coverage": {
        "full_name": "coverage",
        "mapping": {
          "coverage": {
            "type": "keyword"
          }
        }
      }
    }
  }
}
```

2. apply the alias with the filter

**Hint**: you need to use the `.keyword` field here, or you will get zero results.

```json
POST /_aliases
{
  "actions": [
    {
      "add": {
        "index": "totality-raw",
        "alias": "totality-full",
        "filter": {
          "term": {
            "coverage.keyword": "100%"
          }
        }
      }
    }
  ]
}
```
Lets take a look at the alias:
```json
GET _alias/totality-full
```
And lets verify the output:
```json
{
  "acknowledged": true
}
```

3. test

```json
GET totality-full/_count

// Output 

{
  "count" : 50,
  "_shards" : {
    "total" : 1,
    "successful" : 1,
    "skipped" : 0,
    "failed" : 0
  }
}
```

4. :question: BONUS: Run a query to do the same on `totality-raw` index

Extra bonus: only print the total hits

```json
POST totality-raw/_search?filter_path=hits.total.value
{
  "query": {
    "match": {
      "coverage": "100%"
    }
  }
}
// Output 
{
  "hits" : {
    "total" : {
      "value" : 50
    }
  }
}
```
</details>
<hr> 

## Use the Reindex API and Update By Query API to reindex and/or update documents

### Part 1
:question: Reindex the `totality-raw` index into `totality-state-parks-raw`.
:question: Then reindex `totality-state-parks-raw` into `totality-state-parks-full` index where only the state parks that have 100% totality coverage are present.

> :warning: 
Reindex requires _source to be enabled for all documents in the source index.

:warning: check your templates, to make sure they are not forcing `"_source": { "enabled": false },` as this will break reindexing.

```json
POST _reindex
{
  "source": { "index": "totality-raw"  },
  "dest":   { "index": "totality-state-parks-raw" }
}

GET totality-state-parks/_count?filter_path=count

// Output 

{
  "count" : 187
}
```

reindex into `totality-full`

:bulb: do the term query first, then once you are happy with the output, convert it into a `_reindex`
```json
POST _reindex
{
  "source": { 
    "index": "totality-state-parks-raw",
    "query": {
      "match": {
        "coverage": "100%"
      }
    }
  },
  "dest":   { "index": "totality-state-parks-full" }
}
```

Check
```json
GET totality-state-parks-full/_count?filter_path=count

// Output 

{
  "count" : 50
}
```

Check again (fix this later)
```json
GET /totality-state-parks-full/_search?filter_path=*.*.*.coverage

// Output 

{
  "hits" : {
    "hits" : [
      {
        "_source" : {
          "coverage" : "100%"
        }
      },
      {
        "_source" : {
          "coverage" : "100%"
        }
      },
    ...
```
</details>

### Part 2
:question: Give all state parks with 100% coverage in `totality-raw` a 25% bonus increase on their total time (just for the sake of this example) :)

<details>
  <summary>View Solution (click to reveal)</summary>

Get some example docs
```json
GET /totality-raw/_search?q=coverage:100%
```
THe output should look similar to:
```json
{
  "took": 7,
  "timed_out": false,
  "_shards": {
    "total": 1,
    "successful": 1,
    "skipped": 0,
    "failed": 0
  },
  "hits": {
    "total": {
      "value": 51,
      "relation": "eq"
    },
    "max_score": 1.3054422,
    "hits": [
      {
        "_index": "totality-ui",
        "_id": "QIYm6IgBqywhrlXquKQb",
        "_score": 1.3054422
      },
      {
        "_index": "totality-ui",
        "_id": "RIYm6IgBqywhrlXquKQb",
        "_score": 1.3054422
      },
      {
        "_index": "totality-ui",
        "_id": "ToYm6IgBqywhrlXquKQb",
        "_score": 1.3054422
      },
      {
        "_index": "totality-ui",
        "_id": "VIYm6IgBqywhrlXquKQb",
        "_score": 1.3054422
      },
      {
        "_index": "totality-ui",
        "_id": "VoYm6IgBqywhrlXquKQb",
        "_score": 1.3054422
      },
      {
        "_index": "totality-ui",
        "_id": "fIYm6IgBqywhrlXquKQb",
        "_score": 1.3054422
      },
      {
        "_index": "totality-ui",
        "_id": "hIYm6IgBqywhrlXquKQb",
        "_score": 1.3054422
      },
      {
        "_index": "totality-ui",
        "_id": "iIYm6IgBqywhrlXquKQb",
        "_score": 1.3054422
      },
      {
        "_index": "totality-ui",
        "_id": "sIYm6IgBqywhrlXquKQb",
        "_score": 1.3054422
      },
      {
        "_index": "totality-ui",
        "_id": "toYm6IgBqywhrlXquKQb",
        "_score": 1.3054422
      }
    ]
  }
}
```


Update the accounts - take note of the number of `updated` docs
```json
POST totality-ui/_update_by_query
{
  "script": {
    "source": "ctx._source.totality_minutes=99",
    "lang": "painless"
  },
  "query": {
    "match": {
      "state.keyword": "Vermont"
    }
  }
}
// Output

{
  "took" : 369,
  "timed_out" : false,
  "total" : 493,
  "updated" : 493,
  "deleted" : 0,
  "batches" : 1,
  "version_conflicts" : 0,
  "noops" : 0,
  "retries" : {
    "bulk" : 0,
    "search" : 0
  },
  "throttled_millis" : 0,
  "requests_per_second" : -1.0,
  "throttled_until_millis" : 0,
  "failures" : [ ]
}
```
Get those same ids again to check the balances have increased
```json
GET totality-raw/_search
{
  "query": {
    "match": {
      "street_address.keyword": "43 Great Bay Lane"
    }
  }
}
//Output
...
          "street_address" : "43 Great Bay Lane",
          "eclipse_date" : "2024-04-08",
          "totality_minutes" : 99,
...
```
:bonus question: Give all state parks with 100% coverage in `totality-raw` a 20% decrease on their total time to return to their normal values
(repeat process from above)
</details>
<hr>

### Define and use an ingest pipeline that satisfies a given set of requirements, including the use of Painless to modify documents
:question: Apply a pipeline called `all-pipelines` to the data in `state-parks` with the following requirements: (these are all possible options, but do as many as necessary to gain an understanding of ingest pipelines conceptually, how they work, their syntax, and how to write them.

- Add a `tag` called `pipeline_ingest` to show that the document was ingested via the pipeline 
- Change the start, max, and end times from the applicable timezone to UTC (and replace the values)
- Calculate the amount of total seconds in totality and add it to a new field called full_totality_time
- Ensure that every park has 'State Park' in their name. If not, then add it to the name.
- For those state parks with 100% coverage, add in 2 new fields: totality_start_time and totality_end_time. These values are calculated by taking the max_time and adding/subtracting 1/2 of the totality time. An example of this is if there is 2 minutes of totality and maximum totality occurs at 1:05pm, then totality_start_time would be 1:04pm and totality_end_time would be 1:06pm.

Then reindex the data with that new pipeline
```json
POST _ingest/pipeline/_simulate
{
  "pipeline": {
    "processors": [
      {
        "append": {
          "field": "tags",
          "value": ["pipeline_ingest"]
        }
      },
      {
        "set": {
          "field": "full_duration",
          "value": "{{totality_minutes}}:{{totality_seconds}}"
        }
      }
    ]
  },
  "docs": [
    {
      "_source": {
        "name": "Tenkiller",
        "street_address": "OK-100",
        "city": "Vian",
        "state": "Oklahoma",
        "zip_code": "74962",
        "timezone": "CDT",
        "coverage": "100%",
        "eclipse_date": "2024-04-08",
        "totality_minutes": 3,
        "totality_seconds": 54,
        "partial_start_time": "2024-04-08T12:28:11.000Z",
        "max_time": "2024-04-08T13:47:24.000Z",
        "partial_end_time": "2024-04-08T15:06:45.000Z"
      }
    }
  ]
}
```

Here you will get a lot of output, make sure it matches what you expect to see.

Now copy the working pipeline

```json
PUT _ingest/pipeline/totality-ingest
{
  "description" : "pipeline to account ingest",
  "processors": [
      {
        "append": {
          "field": "tags",
          "value": ["pipeline_ingest"]
        }
      },
      {
        "set": {
          "tag": "set full_name",
          "field": "full_name",
          "value": "{{firstname}} {{lastname}}"
        }
      }
    ]
}

//Output
{
  "docs" : [
    {
      "doc" : {
        "_index" : "_index",
        "_id" : "_id",
        "_source" : {
          "coverage" : "100%",
          "street_address" : "OK-100",
          "eclipse_date" : "2024-04-08",
          "full_duration" : "3:54",
          "totality_minutes" : 3,
          "city" : "Vian",
          "timezone" : "CDT",
          "zip_code" : "74962",
          "tags" : [
            "pipeline_ingest"
          ],
          "partial_start_time" : "2024-04-08T12:28:11.000Z",
          "totality_seconds" : 54,
          "name" : "Tenkiller",
          "partial_end_time" : "2024-04-08T15:06:45.000Z",
          "state" : "Oklahoma",
          "max_time" : "2024-04-08T13:47:24.000Z"
        },
        "_ingest" : {
          "timestamp" : "2023-06-23T16:58:30.388650808Z"
        }
      }
    }
  ]
}

```
Then reindex the data with that new pipeline
```json
POST totality-raw/_update_by_query?pipeline=totality-ingest

//output
{
  "took" : 188,
  "timed_out" : false,
  "total" : 187,
  "updated" : 187,
  "deleted" : 0,
  "batches" : 1,
  "version_conflicts" : 0,
  "noops" : 0,
  "retries" : {
    "bulk" : 0,
    "search" : 0
  },
  "throttled_millis" : 0,
  "requests_per_second" : -1.0,
  "throttled_until_millis" : 0,
  "failures" : [ ]
}

```
Checking to verify that the data was transformed properly:
```json
POST totality-raw

// Output 
{
  "took" : 2,
  "timed_out" : false,
  "_shards" : {
    "total" : 1,
    "successful" : 1,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 187,
      "relation" : "eq"
    },
    "max_score" : 1.0,
    "hits" : [
      {
        "_index" : "totality-raw",
        "_id" : "0",
        "_score" : 1.0,
        "_source" : {
          "street_address" : "43 Great Bay Lane",
          "eclipse_date" : "2024-04-08",
          "totality_minutes" : 0,
          "city" : "Laconia",
          "timezone" : "EDT",
          "zip_code" : "03246",
          "coverage_percent" : "97.47%",
          "tags" : [
            "pipeline_ingest"
          ],
          "partial_start_time" : "2024-04-08T14:16:03.000Z",
          "full_name" : " ",
          "totality_seconds" : 0,
          "name" : "Ahern State Park",
          "partial_end_time" : "2024-04-08T16:38:48.000Z",
          "state" : "New Hampshire",
          "max_time" : "2024-04-08T15:29:32.000Z"
        }
      },
```
If you wanted to limit the size and specifics of the request to check if the ingest pipeline worked, you can use the following:

```json
POST totality-raw
{
  "size": 1, 
  "query": { 
    "bool": { 
      "must": [
        { "match": { "gender.keyword":   "F"}}
      ],
      "filter": [ 
        { "range": { "age": { "gte": "39" }}}
      ]
    }
  }
}

```
###  Define a mapping that satisfies a given set of requirements

### Dynamic mapping
> Dynamic mapping allows you to experiment with and explore data when you’re just getting started. Elasticsearch adds new fields automatically, just by indexing a document. You can add fields to the top-level mapping, and to inner object and nested fields.

### Explicit mapping
> Explicit mapping allows you to precisely choose how to define the mapping definition, such as:
> 
> - Which string fields should be treated as full text fields.
> - Which fields contain numbers, dates, or geolocations.
> - The format of date values.
> - Custom rules to control the mapping for dynamically added fields.

:question: 1. Create a new index mapping called `oklahoma` that matches the following requirements:

- state: oklahoma
- line_id: keyword and not aggregateable 
- speech_number: integer
```json
PUT /oklahoma
{
 "settings": {
   "number_of_replicas": 0
 },
 "mappings": {
   "properties": {
        "coverage": {
          "type": "keyword"
        },
        "street_address": {
          "type": "keyword"
        },
        "eclipse_date": {
          "type": "date"
        },
        "start_time": {
          "type": "date"
        },
        "totality_minutes": {
          "type": "integer"
        },
        "city": {
          "type": "keyword"
        },
        "totality_seconds": {
          "type": "integer"
        },
        "name": {
          "type": "keyword"
        },
        "end_time": {
          "type": "date"
        },
        "state": {
          "type": "keyword"
        },
        "max_time": {
          "type": "date"
        },
        "zip_code": {
          "type": "keyword"
        }
  }
 }
}
//output
{
  "acknowledged" : true,
  "shards_acknowledged" : true,
  "index" : "oklahoma"
}
```

2. Using the previous ingested totality-raw index, re-index the data into the new index called oklahoma that only contains the state parks for oklahoma.
<br>Other options including making it so that this new index has state parks with only 100% coverage.
<br>There are ways to do this to ensure that

```json
POST _reindex
{
  "source": { 
    "index": "totality-raw",
    "query": {
      "match": {
        "state": "Oklahoma"
      }
    }
  },
  "dest":   { "index": "oklahoma" }
}

//Output
{
  "took" : 405,
  "timed_out" : false,
  "total" : 39,
  "updated" : 0,
  "created" : 39,
  "deleted" : 0,
  "batches" : 1,
  "version_conflicts" : 0,
  "noops" : 0,
  "retries" : {
    "bulk" : 0,
    "search" : 0
  },
  "throttled_millis" : 0,
  "requests_per_second" : -1.0,
  "throttled_until_millis" : 0,
  "failures" : [ ]
}
```
verify that the data only contains Oklahoma State Parks
### TBD


### Mapping numeric identifiers (need to fix this)
>Not all numeric data should be mapped as a numeric field data type. Elasticsearch optimizes numeric fields, such as integer or long, for range queries. However, keyword fields are better for term and other term-level queries.
>
>Identifiers, such as an ISBN or a product ID, are rarely used in range queries. However, they are often retrieved using term-level queries.
>
>Consider mapping a numeric identifier as a keyword if:
>
> - You don’t plan to search for the identifier data using range queries.
> - Fast retrieval is important. term query searches on keyword fields are often faster than term searches on numeric fields.
> 
> If you’re unsure which to use, you can use a multi-field to map the data as both a keyword and a numeric data type.
https://www.elastic.co/guide/en/elasticsearch/reference/8.1/doc-values.html
> All fields which support doc values have them enabled by default. If you are sure that you __don’t need to sort or aggregate__ on a field, or access the field value from a script, you can disable doc values in order to save disk space


```json

```

</details>
<hr/>


## Define and use a custom analyzer that satisfies a given set of requirements
and
## Define and use multi-fields with different data types and/or analyzers
Alternatives include renaming the states or cities, or something else.

:question: 1. Write a custom analyzer that changes the name of `State Park` to `State Designated Outside Place` in the `name` field, add this to a new index called `funny_name_analyzer`

- Create a new index
- with a mapping on the name field 
- that utilises an analyser 
- to rename the state name to its abbreviation if it matches.

:question: 2. Write a custom analyzer that changes the name of `State Park` to `SP` in the `name` field, add this to a new index called `sp_name_replacer`


<details>
  <summary>View Solution (click to reveal)</summary>


## Test the analyser

```json
POST _analyze
{
  "char_filter": {
      "type": "pattern_replace",
      "pattern": "State Park",
      "replacement": ""
  },
  "text": [
    "Stub Stewart State Park",
    "Very Boring State Park",
    "Super Exciting State Park"
  ]
}

// output
{
  "tokens": [
    {
      "token": "Stub Stewart ",
      "start_offset": 0,
      "end_offset": 23,
      "type": "word",
      "position": 0
    },
    {
      "token": "Very Boring ",
      "start_offset": 24,
      "end_offset": 46,
      "type": "word",
      "position": 101
    },
    {
      "token": "Super Exciting ",
      "start_offset": 47,
      "end_offset": 72,
      "type": "word",
      "position": 202
    }
  ]
}
```

## Put it all together

```json
PUT /ok-custom-analyzer
{
  "mappings": {
    "properties": {
      "speaker": {
        "type": "text",
        "analyzer": "state_park_analyser",
        "fields": {
          "keyword": {
            "type": "keyword",
            "ignore_above": 256
          }
        }
      },
      "line_id": {
        "type": "integer"
      },
      "speech_number": {
        "type": "integer"
      }
    }
  },
  "settings": {
    "number_of_replicas": 0,
    "analysis": {
      "analyzer": {
        "wayward_son_analyser": {
          "type":      "custom", 
          "tokenizer": "standard",
          "char_filter": [
            "rename_filter"
          ]
        }
      },
      "char_filter": {
        "rename_filter": {
          "type": "pattern_replace",
          "pattern": "Oklahoma",
          "replacement": "OK"
        }
      }
    }
  }
}
```
</details>
<hr/>

:question: 2. re-index `totality-all` into `totality-custom-analyzer`

<details>
  <summary>View Solution (click to reveal)</summary>

```json
POST _reindex
{
  "source": { 
    "index": "totality-raw",
    "query": {
      "term": {
        "state.keyword": "Oklahoma"
      }
    }
  },
  "dest":   { "index": "ok-custom-analyzer" }
}
```
</details>
<hr/>

:question: 3. verify by querying the `totality-custom-analyzer` index for the speaker `HAL`, `WAYWARD` and `PRINCE HENRY`

<details>
  <summary>View Solution (click to reveal)</summary>

```json
GET ok-custom-analyzer/_search
{
  "query": {
    "term": {
      "state.keyword": "Ok"
    }
  }
}
```

> NOTE: Oddly you can't see the `HAL` or `WAYWARD` in the returned data, but you can search for it.
> What you get returned is the original data `PRINCE HENRY`

#TBC check why this is.
</details>
<hr/>

# Search Related Activity

### Perform a remote cluster search
Now that we have the remote cluster setup, lets perform a cluster search.

```json
GET /cluster_one:totality-raw/_search
{
  "query": {
    "match": {
      "state": "Oklahoma"
    }
  }
}
```
### Perform a multiple cross cluster search
Example of this
```json
GET /twitter,cluster_one:twitter,cluster_two:twitter/_search
{
  "query": {
    "match": {
      "user": "kimchy"
    }
  }
}
``` 

## Asynchronous Search
-Note there isnt enough data to provide a good use case for this to show its true functionality. But these are examples. 

### Write an asynchronous search to sort by timestamp
```json

POST /totality-raw/_async_search?size=0
{
  "sort": [
    { "totality_minutes": { "order": "asc" } }
  ],
  "aggs": {
    "sale_date": {
      "date_histogram": {
        "field": "totality_minutes",
        "calendar_interval": "1d"
      }
    }
  }
}
```
### Write and execute a search query that is a Boolean combination of multiple queries and filters
Write a query that provides all of the state parks in New Hampshire with a zip code of 03579. Add 
```json

GET totality-raw/_search
{
  "size": 200,
  "query": {
    "bool": {
      "must": [
        {
          "term": {
            "state.keyword": "New Hampshire"
          }
        },
        {
          "term": {
            "zip_code": "03579"
          }
        }
      ]
    }
  }
}   
```
Show the result above but filtering out the state parks with 0 seconds totality time
```json
GET totality-raw/_search
{
  "size": 200,
  "query": {
    "bool": {
      "must": [
        {
          "term": {
            "state.keyword": "New Hampshire"
          }
        },
        {
          "term": {
            "zip_code": "03579"
          }
        }
      ],
      "must_not": [
        {
          "term": {
            "totality_seconds": "0"
          }
        }
      ]
    }
  }
}    
```
### Get Asynchronous Search Details
```json
GET /_async_search/{id}
```

### Get Asynchronous Search Status
```json
GET /_async_search/status/{id}
```

### Delete An Asynchronous Search
```json
DELETE /_async_search/{id}
```

### Write and execute a search that utilizes a runtime field
In this instance, we will create a runtime field that will take the existing fields minutes and seconds, and turn them into a field called seconds_per_site. Therefore, 1 minute and 30 seconds would become 90 seconds for the seconds_per_site field.

Other runtime fields could include, but are not limited to:
- Create a field called state_code that takes the state name and abbreviates it. Such as shortening New Hampshire NH, and VT for Vermont.
- Create a field describing whether the total length of totality is not applicable, long, short, or medium in time.
- How many state parks have the same zipcode (parks_in_zipcode)
- How many state parks are in the same state (parks_in_state)
- How many state parks are in the same city (parks_in_city)

There are 3 ways to get this done: 
1) Create the runtime field when the index is created, which would require uploading new data
2) Using an existing index to create a new runtime field
3) Defining a Runtime Field In A Search Request

### Create the runtime field when the index is created, which would require uploading new data
   
Step 1: Create The Runtime Field
The first step is to create the runtime field, then we have to add data into it.
#### This will create a new index
```json
PUT totality-raw-runtime/
{
  "mappings": {
    "runtime": {
      "total-time-info": {
        "type": "long",
        "script": {
          "source": "emit((doc['totality_minutes'].value * 60) + doc['totality_seconds'].value);"
        }
      }
    },
    "properties": {
      "totality_seconds": {"type": "long"},
      "totality_minutes": {"type": "long"}
    }
  }
}
//Output
{
  "acknowledged" : true,
  "shards_acknowledged" : true,
  "index" : "totality-raw-runtime"
}
```

Then we will add in data into the system for testing. This is not the full list of the imported fields, but rather an example of what it would look like from the dev console. The full file can be found at the example-data folder in the put-runtime-fields.json file. 

```json
POST /totality-raw-runtime/_bulk?refresh
{ "index": {}}
{"name":"Ahern State Park","street_address":"43 Great Bay Lane","city":"Laconia","state":"New Hampshire","zip_code":"03246","timezone":"EDT","coverage_percent":"97.47%","eclipse_date":"2024-04-08","totality_minutes":0,"totality_seconds":0,"partial_start_time":"2024-04-08T14:16:03.000Z","max_time":"2024-04-08T15:29:32.000Z","partial_end_time":"2024-04-08T16:38:48.000Z"}
{ "index": {}}
{"name":"Androscoggin Wayside Park","street_address":"Route 16","city":"Milan","state":"New Hampshire","zip_code":"03588","timezone":"EDT","coverage":"100%","eclipse_date":"2024-04-08","totality_minutes":2,"totality_seconds":5,"partial_start_time":"2024-04-08T14:17:11.000Z","max_time":"2024-04-08T15:30:07.000Z","partial_end_time":"2024-04-08T16:38:57.000Z"}
```
### Using an existing index to create a new runtime field
```json
PUT totality-raw/_mapping
{
  "runtime": {
    "total-time-info": {
      "type": "long",
      "script": {
        "source": """emit((doc['totality_minutes'].value * 60) + doc['totality_seconds'].value)""",
        "params": {
          "totality_seconds": {"type": "long"},
          "totality_minutes": {"type": "long"}
        }
      }
    }
  }
}
//Output
{
  "acknowledged" : true
}
```
<br> Step 2 Perform the search </br>
The search could be:
How many have seconds_per_site above a specific value such as 200.
```json
GET totality-raw-runtime2/_search
{
  "fields": [
    "total-time-info"
  ],
  "size": 2
}
```
### Defining a Runtime Field In A Search Request
GET totality-raw/_search
{
  "runtime_mappings": {
    "total-time-info": {
      "type": "long",
      "script": {
        "source": "emit((doc['totality_minutes'].value * 60) + doc['totality_seconds'].value)"
      }
    }
  },
  "aggs": {
    "total-time-info": {
      "terms": {
        "field": "total-time-info"
      }
    }
  }
}
## Performing Searches

### Write and execute a search query for terms and/or phrases in one or more fields of an index
How many state parks in Vermont are in the path of totality? <br>
How many places have Vermont, Maine, New Hampshire, or Oklahoma as the state? <br>
How many have totality minutes between 3 and 5 minutes? <br>

How many times do the words New Hanpshire appear in the eclipse data? <br>
```json
GET totality-raw/_search
{
  "query": {
    "match": {
      "state": {
        "query": "New Hampshire"
      }
    }
  }
}
```
Output
```json
{
  "took": 1,
  "timed_out": false,
  "_shards": {
    "total": 1,
    "successful": 1,
    "skipped": 0,
    "failed": 0
  },
  "hits": {
    "total": {
      "value": 67,
      "relation": "eq"
    },
    "max_score": 1.0401459,
    ...
```
How many state parks have 100% coverage? (in totality) <br>

```json
GET state_parks_totality_2024/_search
{
  "query": {
    "terms": {
      "state": ["state1", "state2"]
    }
  }
}
```

```json
GET totality-raw/_search
{
  "query": {
    "multi_match" : {
      "query":    "prince henry", 
      "fields": [ "text_entry", "speaker" ],
      "type": "phrase"
    }
  }
}
```
```json
GET totality-raw/_search
{
  "query": {
    "range": {
      "totality_minutes": {
        "gte": 0,
        "lte": 2
      }
    }
  }
}
```
```json
GET totality-raw/_search
{
  "size": 200,
  "query": {
    "bool": {
      "must": [
        {
          "term": {
            "play_name": "Othello"
          }
        },
        {
          "wildcard": {
            "text_entry.keyword": "*Desdemona*"
          }
        }
      ],
      "must_not": [
        {
          "term": {
            "speaker": "OTHELLO"
          }
        }
      ]
    }
  }
}
```
```json
GET shakespeare/_search
{
  "size": 200,
  "query": {
    "bool": {
      "must": [
        {
          "term": {
            "play_name": "Othello"
          }
        },
        {
          "wildcard": {
            "text_entry.keyword": "*Desdemona*"
          }
        }
      ],
      "must_not": [
        {
          "term": {
            "speaker": "OTHELLO"
          }
        }
      ],
      "filter": {
        "term": {
          "text_entry": "sweet"
        }
      }
    }
  }
}
```
- Other searches include but are not limited to:
How many states are in totality (count unique state field)


### Write and execute metric and bucket aggregations
What we will do is use the index aliases and created indexes so far to perform aggregations!

### Metric Aggregations
Some options include but not limited to:
- Average coverage, average time, max time, min time > 0, sum of time for parks that are 100%, sum of total numnber of parks. <br>
The example below shows the maximum amount of minutes.
```json
POST /totality-raw/_search
{
  "aggs": {
    "most_minutes_totality": { "max": { "field": "totality_minutes" } }
  }
}
```
This isnt an exhaustive or exclusive list as there are opportunities for metric aggregations that arent listed below plus there are metric aggregations that cannot apply to this data. Be aware that they exist and understand where the documentation is so if questioned, you know where to find. <br>
Other bucket aggregations applicable to this data include: <br>
- Average totality time
- Maximum totality time
- Stats about totality time
- Sum of totality time
- Min totality time

### Bucket Aggregations
Display number of sites per state <br>
Display number of sites per coverage % <br>
Display the number of sites per minutes of totality <br>
Display number of sites per zip code <br>
Display number of sites per city <br>
Display number of sites per totality minutes <br>

```json
GET totality-raw/_search?filter_path=aggregations
{  
  "aggs": {
    "my-agg-name": {
      "terms": {
        "field": "totality_minutes"
      }
    }
  }
}
```
This isnt an exhaustive or exclusive list as there are opportunities for bucket aggregations that arent listed below plus there are bucket aggregations that cannot apply to this data. Be aware that they exist and understand where the documentation is so if questioned, you know where to find. <br>
Other bucket aggregations applicable to this data include: <br>
- Date Range for totality starting
- Date Range for totality ending
- Date Range for totality maximum
- Range Aggregation For Zip Code
- Range Aggregation For Totality Time
- Filter based upon Zip Code
- Filter based upon totality minutes
- Filter based upon coverage

### Write and execute aggregations that contain sub-aggregations
There are several different sub-aggregations that we can run. These are just examples, and not representative of everything that could be done for sub-aggregations for this data.
- Which state park in totality has the longest total number of minutes for State X?
- Which state park in totality has the shortes total number of minutes for State X?
- Which state park in totality has the shortes total totality time for State X?
- Which state park in totality has the shortest total totality time for State X?
- What is the average totality minutes for State X?
- What is the average totality seconds for State X?
- What is the average totality time for State X for all state parks?
- What is the average totality time for State X for all state parks with 100% totality?
https://www.elastic.co/guide/en/elasticsearch/reference/current/search-aggregations.html#run-sub-aggs
To do any one of the examples above, the high level way to do this would be to:
- Create an aggregation using the state field
- Group that aggregation where the coverage is equal to 100%
- Then finally, 

Basially here, you create each aggregation separately and then combine in the end.

One option is to create a date histogram for the latest totality start time on the day of the eclipse.

```json
GET kibana_sample_data_ecommerce/_search?filter_path=aggregations
{
  "size":0,
  "aggs": {
    "date_hist_y_axis": {
      "date_histogram": {
        "field": "eclipse_date",
        "calendar_interval": "1m",
        "min_doc_count": 1
      }
    }
  }
}
```
Sub-Aggregate by State grouping:
```json
GET kibana_sample_data_ecommerce/_search?filter_path=aggregations
{
  "size": 0,
  "aggs": {
    "cat_keyword": {
      "terms": {
        "field": "state.keyword"
      }
    }
  }
}
```

Putting all together in a way (not combining the exact 2 from above) to show the total count of the totality minutes field for the state parks in Oklahoma:
```json
GET oklahoma/_search?filter_path=aggregations
{
  "size":0,
  "aggs": {
    "date_hist_y_axis": {
      "date_histogram": {
        "field": "eclipse_date",
        "calendar_interval": "1m",
        "min_doc_count": 1
      },
      "aggs": {
        "group_by_category": {
          "terms": {
            "field": "state.keyword",
            "size": 8
          },
          "aggs": {
            "total_totality_minutes": {
              "sum": {
                "field": "totality_minutes"
              }
            }
          }
        }
      }
    }
  }
}
```
## Developing Search Applications
### Highlight the search terms in the response of a query
In the New Hampshire parks, highlight the Name starting the highlight with "#aaa# and ending it with #bbb# <br>
Other options include highlighting 100% coverage state parks, parks with more than a specific amount of time in totality, state parks in a specific zipcode, or state parks in a specific city.
```json
GET totality-raw/_search
{
    "query": {
        "match": {
            "state":  "New Hampshire"
        }
    }, 
    "highlight": {
        "fields":  { 
          "text_entry": {
            "pre_tags": "#aaa#", 
            "post_tags": "#bbb#"
        }
      }
    }
}
```

## Return all of the results of a query by a given requirements
Search the template for all of the state parks in Vermont, sorted in descending order by the number of minutes of totality.
```json
GET totality-raw/_search
{
    "query": {
        "match": {
            "state": "Vermont"
          }
    }, 
    "sort": [
      {
        "coverage.keyword": {
          "order": "asc"
        }
      }
    ]
}
//Output
{
  "took" : 1,
  "timed_out" : false,
  "_shards" : {
    "total" : 1,
    "successful" : 1,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 49,
      "relation" : "eq"
    },
    "max_score" : null,
    "hits" : [
      {
        "_index" : "totality-raw",
        "_id" : "67",
        "_score" : null,
        "_source" : {
          "coverage" : "100%",
          "street_address" : "151 Coon Point Rd",
          "eclipse_date" : "2024-04-08",
          "totality_minutes" : 3,
          "city" : "Alburg",
          "timezone" : "EDT",
          "zip_code" : "05440",
          "tags" : [
            "pipeline_ingest"
          ],
          "partial_start_time" : "2024-04-08T14:14:21.000Z",
          "full_name" : " ",
          "totality_seconds" : 33,
          "name" : "Alburgh Dunes State Park",
          "partial_end_time" : "2024-04-08T16:37:12.000Z",
          "state" : "Vermont",
          "max_time" : "2024-04-08T15:27:43.000Z"
        },
        "sort" : [
          "100%"
        ]
      },
```

### Pagination 
Paginate the Totality results, 20 state parks per page, stating from state park 40 for the state of New Hampshire.
This can also be done for the other states in the dataset and sorted by zipcode or coverage.
```json
GET totality-raw/_search
{
    "size": 2,
    "from": 40,
    "query": {
        "term": {
            "state.keyword": "Vermont"
          }
    }, 
    "sort": [
      {
        "coverage.keyword": {
          "order": "asc"
        }
      }
    ]
}
//Output
{
  "took" : 2,
  "timed_out" : false,
  "_shards" : {
    "total" : 1,
    "successful" : 1,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 49,
      "relation" : "eq"
    },
    "max_score" : null,
    "hits" : [
      {
        "_index" : "totality-raw",
        "_id" : "105",
        "_score" : null,
        "_source" : {
          "coverage" : "98.61%",
          "street_address" : "6800 Woodstock Rd",
          "eclipse_date" : "2024-04-08",
          "totality_minutes" : 0,
          "city" : "Quechee",
          "timezone" : "EDT",
          "zip_code" : "05059",
          "tags" : [
            "pipeline_ingest"
          ],
          "partial_start_time" : "2024-04-08T14:14:47.000Z",
          "full_name" : " ",
          "totality_seconds" : 0,
          "name" : "Quechee State Park",
          "partial_end_time" : "2024-04-08T16:38:03.000Z",
          "state" : "Vermont",
          "max_time" : "2024-04-08T15:28:28.000Z"
        },
        "sort" : [
          "98.61%"
        ]
      },
      {
        "_index" : "totality-raw",
        "_id" : "78",
        "_score" : null,
        "_source" : {
          "coverage" : "98.67%",
          "street_address" : "855 Coolidge State Park Rd",
          "eclipse_date" : "2024-04-08",
          "totality_minutes" : 0,
          "city" : "Plymouth",
          "timezone" : "EDT",
          "zip_code" : "05056",
          "tags" : [
            "pipeline_ingest"
          ],
          "partial_start_time" : "2024-04-08T14:14:20.000Z",
          "full_name" : " ",
          "totality_seconds" : 0,
          "name" : "Coolidge State Park",
          "partial_end_time" : "2024-04-08T16:37:49.000Z",
          "state" : "Vermont",
          "max_time" : "2024-04-08T15:28:07.000Z"
        },
        "sort" : [
          "98.67%"
        ]
      }
    ]
  }
}
```

### Write and execute a query that searches across multiple clusters
In this instance, what we will assume is that each cluster has separate states information on it. Therefore, since there are 11 states in totality, there would be 11 clusters and 11 total indexes.

'''json
GET ny_parks_index,remote_cluster1:nh_parks_index,remote_cluster2:tx_parks_index
{
  "query" : {
    "match_all" : {}
  }
}
'''

### Define and use a search template
A search template is a stored search you can run with different variables.

:question: Create and use a search template that returns the number of a state parks in totality. (100% coverage)
#### Bonus
- Create and use a search template that returns the number of a state parks in totality for a specific state (100% coverage)
- Create and use a search template that returns the number of a state parks in totality for a specific zipcode
- 
:question: Show all state parks that are in the state of `Vermont` that are in totality

<details>
  <summary>View Solution (click to reveal)</summary>

```json
POST _scripts/get_by_state
{
  "script": {
    "lang": "mustache",
    "source": {
      "query": {
        "bool": {
          "must": [
            {
              "term": {
                "coverage.keyword": "{{coverage}}"
              }
            },
            {
              "term": {
                "state.keyword": "{{state}}"
              }
            }
          ]
        }
      }
    }
  }
}
// output

{
  "acknowledged": true
}
```
## pull the template back

:warning: Note that it is not formatted nicely 😤

```json
GET _scripts/get_by_state

// output
{
  "_id" : "get_by_state",
  "found" : true,
  "script" : {
    "lang" : "mustache",
    "source" : """{"query":{"bool":{"must":[{"term":{"coverage.keyword":"{{coverage}}"}},{"term":{"state.keyword":"{{state}}"}}]}}}""",
    "options" : {
      "content_type" : "application/json;charset=utf-8"
    }
  }
}
```

## Test

I suppose you could see this as like a `SQL user-defined function` to be called externally with far less code available to the end-user.  No per-search-template (sql-function-like) security though.  Only read access to the underlying index is required. 🙄

```json
GET totality-raw/_search/template
{
    "id": "get_by_state", 
    "params": {
        "coverage": "100%",
        "state": "Vermont"
    }
}
// output
{
  "took" : 2,
  "timed_out" : false,
  "_shards" : {
    "total" : 1,
    "successful" : 1,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 28,
      "relation" : "eq"
    },
    "max_score" : 2.6436045,
    "hits" : [
      {
        "_index" : "totality-raw",
        "_id" : "67",
        "_score" : 2.6436045,
        "_source" : {
          "coverage" : "100%",
          "street_address" : "151 Coon Point Rd",
          "eclipse_date" : "2024-04-08",
          "totality_minutes" : 3,
          "city" : "Alburg",
          "timezone" : "EDT",
          "zip_code" : "05440",
          "tags" : [
            "pipeline_ingest"
          ],
          "partial_start_time" : "2024-04-08T14:14:21.000Z",
          "full_name" : " ",
          "totality_seconds" : 33,
          "name" : "Alburgh Dunes State Park",
          "partial_end_time" : "2024-04-08T16:37:12.000Z",
          "state" : "Vermont",
          "max_time" : "2024-04-08T15:27:43.000Z"
        }
      },
```
</details>
<hr>

## Slightly off topic, but on the exam!
## Configure an index so that it properly maintains the relationships of nested arrays of objects <br>
:question: 1. Using the below data, create an index with a mapping that allows for relationships to be queried.

```json
PUT totality_r/_doc/_bulk
{"index":{"_index":"totality_r","_id":"0"}}
{"name":"X State Park","relationship":[{"camping":"Yes","neighbor":"Y State Park"}]}
{"index":{"_index":"totality_r","_id":"1"}}
{"name":"Y State Park","relationship":[{"camping":"No","neighbor":"Z State Park"}]}
{"index":{"_index":"totality_r","_id":"2"}}
{"name":"Z State Park","relationship":[{"camping":"Yes","neighbor":"A State Park"}]}
{"index":{"_index":"totality_r","_id":"3"}}
{"name":"A State Park","relationship":[{"camping":"Yes","neighbor":"X State Park"}]}
{"index":{"_index":"totality_r","_id":"4"}}
{"name":"B State Park","relationship":[{"camping":"No","neighbor":"X State Park"}]}
{"index":{"_index":"totality_r","_id":"5"}}
{"name":"C State Park","relationship":[{"camping":"No","neighbor":"Y State Park"}]}
{"index":{"_index":"totality_r","_id":"6"}}
{"name":"D State Park","relationship":[{"camping":"No","neighbor":"Z State Park"}]}
{"index":{"_index":"totality_r","_id":"7"}}
{"name":"Z State Park","relationship":[{"camping":"No","neighbor":"A State Park"}]}
```

<details>
  <summary>View Solution (click to reveal)</summary>

The important part here is to add the `"type": "nested"` so that the data is not flattened when ingested.

So anywhere you have more than one item in the relationship list, it will not be found if you do not use the `nested` term. see docs.

```json
PUT totality_r
{
  "mappings": {
    "properties": {
      "relationship": {
        "type": "nested" 
      }
    }
  }
}

```
</details>
<hr/>

:question: 2. Then query all items in the index that have 'camping' set to 'Yes'

<details>
  <summary>View Solution (click to reveal)</summary>

You need to be using a `nested` query here as well.  Otherwise it will not work.

```json
GET totality_r/_search
{
  "query": {
    "nested": {
      "path": "relationship",
      "query": {
        "bool": {
          "must": [
            { "match": { "relationship.camping": "Yes" }}
          ]
        }
      }
    }
  }
}
// output
{
  "took" : 1,
  "timed_out" : false,
  "_shards" : {
    "total" : 1,
    "successful" : 1,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 3,
      "relation" : "eq"
    },
    "max_score" : 0.8266786,
    "hits" : [
      {
        "_index" : "totality_r5",
        "_id" : "0",
        "_score" : 0.8266786,
        "_source" : {
          "name" : "X State Park",
          "relationship" : [
            {
              "camping" : "Yes",
              "neighbor" : "Y State Park"
            }
          ]
        }
      },
      {
        "_index" : "totality_r5",
        "_id" : "2",
        "_score" : 0.8266786,
        "_source" : {
          "name" : "Z State Park",
          "relationship" : [
            {
              "camping" : "Yes",
              "neighbor" : "A State Park"
            }
          ]
        }
      },
      {
        "_index" : "totality_r5",
        "_id" : "3",
        "_score" : 0.8266786,
        "_source" : {
          "name" : "A State Park",
          "relationship" : [
            {
              "camping" : "Yes",
              "neighbor" : "X State Park"
            }
          ]
        }
      }
    ]
  }
}
```

</details>
<hr/>

:question: 3. Show all state parks that have camping set to Yes AND neighbor set to Y State Park.


<details>
  <summary>View Solution (click to reveal)</summary>
This will show neighors to Y state park as well.

# query

```json
GET totality_r/_search
{
  "query": {
    "nested": {
      "path": "relationship",
      "query": {
        "bool": {
          "must": [
            { "match": { "relationship.camping": "Yes" }},
            { "match": { "relationship.neighbor":  "Y State Park" }} 
          ]
        }
      }
    }
  }
}

// output

{
  "took" : 5,
  "timed_out" : false,
  "_shards" : {
    "total" : 1,
    "successful" : 1,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 1,
      "relation" : "eq"
    },
    "max_score" : 1.1507283,
    "hits" : [
      {
        "_index" : "totality_r222",
        "_id" : "_doc",
        "_score" : 1.1507283,
        "_source" : {
          "name" : "X State Park",
          "relationship" : [
            {
              "camping" : "Yes",
              "neighbor" : "Y State Park"
            }
          ]
        }
      }
    ]
  }
}
```

</details>
<hr/>

### Define an Index Lifecycle Management policy for a time-series index 
<b> This isnt necessarily specific to this but is inportant to setup</b>
Example from Rich Raposa (Elastic Exam video):<br>
  the corresponding index template is called task3<br>
  the data is hot for 3 minutes, then immediately rolls over to warm<br>
  the data is then warm for 5 minutes, then rolls over to cold<br>
  10 minutes after rolling over, the data is deleted<br>

```json
PUT _ilm/policy/task3
{
  "policy": {
    "phases": {
      "hot": {
        "min_age": "0ms",
        "actions": {
          "rollover": {
            "max_primary_shard_size": "50gb",
            "max_age": "3m"
          },
          "set_priority": {
            "priority": 100
          }
        }
      },
      "warm": {
        "min_age": "0m",
        "actions": {
          "set_priority": {
            "priority": 50
          }
        }
      },
      "cold": {
        "min_age": "5m",
        "actions": {
          "set_priority": {
            "priority": 0
          }
        }
      },
      "delete": {
        "min_age": "10m",
        "actions": {
          "delete": {
            "delete_searchable_snapshot": true
          }
        }
      }
    }
  }
}
```

### Define an index template that creates a new data stream 
<b> Create ILM template </b>

As far as i can tell ILM cron runs every 5-10mins.  So doing anything less than this does not work

```json
PUT _ilm/policy/test-ilm
{
  "policy": {
    "phases": {
      "hot": {
        "min_age": "0ms",
        "actions": {
          "rollover": {
            "max_primary_shard_size": "50gb",
            "max_age": "5m"
          },
          "set_priority": {
            "priority": 100
          }
        }
      },
      "warm": {
        "min_age": "10m",
        "actions": {
          "set_priority": {
            "priority": 50
          }
        }
      },
      "cold": {
        "min_age": "15m",
        "actions": {
          "set_priority": {
            "priority": 0
          }
        }
      },
      "delete": {
        "min_age": "30m",
        "actions": {
          "delete": {
            "delete_searchable_snapshot": true
          }
        }
      }
    }
  }
}
```

<b> Create an index template (not a data_stream) </b>

```json
PUT _index_template/test-ilm-tmpl
{
  "template": {
    "settings": {
      "index": {
        "number_of_shards": 1,
        "number_of_replicas" : 0,
        "lifecycle": {
          "name": "test-ilm",
          "rollover_alias": "test-index"
        }
      }
    },
    "mappings": {
      "properties": {
        "@timestamp": {
          "type": "date"
        },
        "field": {
          "type": "text"
        }
      }
    }
  },
  "index_patterns": [
    "test-index-*"
  ],
  "composed_of": []
}
```

<b> Bootstrap the initial index </b>
:bulb: This is very important - do this before data ingest


:bulb: Needs to have the aliases added or it won't work!

```json
PUT test-index-000000
{
  "aliases": {
    "test-index": {
      "is_write_index": true
    }
  }
}
```


<b> Add a doc or two </b>

```json
PUT test-index/_doc/1
{
  "@timestamp" : "2021-10-04T16:26:00.000Z",
  "field":"test data"
}

PUT test-index/_doc/2
{
  "@timestamp" : "2021-10-04T16:58:00.000Z",
  "field":"test data"
}
```

<b> Check index </b>

```json
GET test-index-000000
```

### Check alias

```json
GET test-index
```

### View ILM phase for each index (rinse and repeat here)

At this point you will have indicies being created and rotated
keep requerying this and see that they are.

```json
GET test-index/_ilm/explain?filter_path=*.*.age,*.*.phase
```

### Results

So, importantly what you can see here is that the index is rolled over at 5-ish minutes.

Then, each move to a new phase is from that initial rollover (at 5-ish minutes)

You can also see that no time is exact.  So days/hours are a better time frame than minutes in production. (at least ths is what i saw).

```json
// output 

{
  "indices" : {
    "test-index-000003" : {
      "age" : "39.65m",
      "phase" : "delete"
    },
    "test-index-000004" : {
      "age" : "29.64m",
      "phase" : "cold"
    },
    "test-index-000005" : {
      "age" : "19.65m",
      "phase" : "warm"
    },
    "test-index-000006" : {
      "age" : "9.65m",
      "phase" : "hot"
    },
  }
}
```
--------------------------------------
