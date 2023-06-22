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
  PUT totality_2024-raw
  {
    "settings": {
      "number_of_shards": 1,
      "number_of_replicas": 0
    }
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
  "index_patterns": ["totality_2024-*"],
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
Question: Create a template for the state parks that are in totality includes:
name, street address, city, state, state abbreviation, zipcode, coverage %, eclipse date, totality minutes, totality seconds, start time, maximum totality time, and end of the totality time.
```json
PUT _template/totality_2024-tmpl
{
  "index_patterns": ["totality_2024-*"],
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
    "timezone" : { "type": "keyword" },
    "zip_code" :  { "type": "keyword" },    
    "coverage" :  { "type": "keyword" },
    "eclipse_date" :  { "type": "date" },
    "totality_minutes" :  { "type": "integer" },
    "totality_seconds" :  { "type": "integer" },
    "partial_start_time" :  { "type": "date" },
    "totality_start_time" :  { "type": "date" },      
    "max_time" :  { "type": "date" },
    "totality_end_time" :  { "type": "date" },            
    "partial_end_time" :  { "type": "date" }
    }
  }
}
```

### Define and use a dynamic template that satisfies a given set of requirements
Why use a dynamic template

### Lets Upload The Data
If running this in a single node environment, use the file named full-eclipse-data.json for the examples. It makes performing the examples significantly easier. Plus, the examples were designed for that purpose. A cross-cluster implementation will be implemented later. Will require copying the work done to the other clusters. <br>
The file named full-eclipse-data.json has all of the data we're going to use. It is found [here]([https://github.com/mr1716/Elastic-Certified-Engineer-Exam-8.1/blob/main/solar_eclipse_2024.json](https://github.com/mr1716/Elastic-Certified-Engineer-Exam-8.1/blob/main/example-date/full-eclipse-data.json)) <br> 

To find the data broken down by state into each line, check out the file unique-clusters.json [here](https://github.com/mr1716/Elastic-Certified-Engineer-Exam-8.1/blob/main/example-date/unique-clusters.json) <br> The benefit of this is that is broken down by each state each line. DO NOT attempt to upload this as 1 file. It is meant to be used to upload 1 line per cluster and provide the benfits of cross cluster search. <br>

To upload this into elasticsearch, run: <br>
$ curl -u "elastic:Password01" -s -H "Content-Type: application/x-ndjson" -XPUT localhost:9200/totality_info/_bulk --data-binary "@full-eclipse-data.json"; echo

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
```

To verify: <br>
Check that the document count matches using GET totality-all/_count

### part 2

:question: Define an index alias for `totality-raw` called `totality-full`

:question: Apply a filter to only show the state parks where the totality is 100%.
Alternatives include aliases for each state, zipcode, states with totality of X minutes, or Timezones.

1. check that the field you want to filter is a keyword
```json
GET totality-raw/_mapping/field/coverage

// Output 

{
  "totality-raw" : {
    "mappings" : {

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
            "coverage.keyword": "100"
          }
        }
      }
    }
  ]
}
```

3. test

```json
GET totality-full/_count

// Output 

{
  "count" : ??,
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
      "value" : ??
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
  "count" : 1000
}
```

reindex into `totality-full`

:bulb: do the term query first, then once you are happy with the output, convert it into a `_reindex`

```json
POST _reindex
{
  "source": { "index": "totality-state-parks-raw",
    "query": {
      "term": {
        "coverage.keyword": "100%"
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
  "count" : ??
}
```

Check again (fix this later)
```json
GET /totality-state-parks/_search?filter_path=*.*.*.coverage

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

Get two example docs
```json
GET /accounts-raw/_search?q=gender:F&size=2
```

Note down those ids and get the balances
```json
GET /accounts-raw/_doc/_mget?filter_path=*.*.balance
{
    "ids" : ["13", "25"]
}

//Output

{
  "docs" : [
    {
      "_source" : {
        "balance" : 32838
      }
    },
    {
      "_source" : {
        "balance" : 40540
      }
    }
  ]
}
```

Update the accounts - take note of the number of `updated` docs
```json
POST accounts-2021/_update_by_query
{
  "script": {
    "source": "ctx._source.balance=ctx._source.balance*1.25",
    "lang": "painless"
  },
  "query": {
    "term": {
      "gender": "F"
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
GET /accounts-2021/_doc/_mget?filter_path=*.*.balance
{
    "ids" : ["13", "25"]
}

// Output

{
  "docs" : [
    {
      "_source" : {
        "balance" : 41047.5
      }
    },
    {
      "_source" : {
        "balance" : 50675.0
      }
    }
  ]
}
```
:question: Give all state parks with 100% coverage in `totality-raw` a 20% decrease on their total time to return to their normal values
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
          "tag": "set full_duration",
          "field": "full_duration",
          "value": "{{minutes}}:{{seconds}}"
        }
      },
      {
        "script": {
          "tag": "39s and over female bonus",
          "if": """
            if (ctx.age >= 39) { 
              if (ctx.gender=="F") { 
                return true 
              }
            } 
            return false
          """,
          "lang": "painless",
          "source": """
            ctx.balance = ctx.balance*1.05
          """
        }
      }
    ]
  },
  "docs": [
    {
      "_source": {
        "account_number": 10000,
        "balance": 1000000,
        "firstname": "George",
        "lastname": "Cross",
        "age": 92,
        "gender": "M",
        "address": "1 Dog Lane",
        "employer": "Wheatens",
        "email": "george@wheatens.com",
        "city": "London",
        "state": "UK"
      }
    },
    {
      "_source": {
        "account_number": 10001,
        "balance": 1000001,
        "firstname": "Millie",
        "lastname": "Cross",
        "age": 84,
        "gender": "F",
        "address": "1 Dog Lane",
        "employer": "Wheatens",
        "email": "millie@wheatens.com",
        "city": "London",
        "state": "UK"
      }
    }
  ]
}
```

Here you will get a lot of output, make sure it matches what you expect to see.

Now copy the working pipeline

```json
PUT _ingest/pipeline/accounts-ingest
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
      },
      {
        "script": {
          "tag": "39s and over female bonus",
          "if": """
            if (ctx.age >= 39) { 
              if (ctx.gender=="F") { 
                return true 
              }
            } 
            return false
          """,
          "lang": "painless",
          "source": """
            ctx.balance = ctx.balance*1.05
          """
        }
      }
    ]
}
```
Then reindex the data with that new pipeline
```json
POST totality-raw/_update_by_query?pipeline=totality-ingest
```
Checking to verify that the data was transformed properly:
```json
POST totality-raw/_search?filter_path=*.*._id
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

// Output 

{
  "hits" : {
    "hits" : [
      {
        "_id" : "25"
      }
    ]
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
    "speaker": {"type": "keyword"},
    "line_id": {
      "type": "keyword",
      "doc_values": false
    },
    "speech_number": {"type": "integer"}
  }
 }
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
      "term": {
        "state": "Oklahoma"
      }
    }
  },
  "dest":   { "index": "oklahoma" }
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
PUT /totality_numeric_id
{
 "settings": {
   "number_of_replicas": 0
 },
 "mappings": {
   "properties": {
    "state": {"type": "keyword"},
    "line_id": {
      "type": "keyword",
      "doc_values": false
    },
    "speech_number": {"type": "integer"}
  }
 }
}
```

</details>
<hr/>


## Define and use a custom analyzer that satisfies a given set of requirements
and
## Define and use multi-fields with different data types and/or analyzers
Alternatives include renaming the states or cities, or something else.

:question: 1. Write a custom analyzer that changes the name of `State Park` to `State Designated Outside Place` in the `name` field, add this to a new index called `funny_name_analyzer`

- Create a new index, 
- with a mapping on the name field 
- that utilises an analyser 
- to rename the st name if it matches.

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
  "tokens" : [
    {
      "token" : "PRINCE WILLIAM",
      "start_offset" : 0,
      "end_offset" : 14,
      "type" : "word",
      "position" : 0
    },
    {
      "token" : "WAYWARD PRINCE HAL",
      "start_offset" : 15,
      "end_offset" : 27,
      "type" : "word",
      "position" : 101
    },
    {
      "token" : "PRINEC HARRY",
      "start_offset" : 28,
      "end_offset" : 40,
      "type" : "word",
      "position" : 202
    }
  ]
}

```

## Put it all together
- mappings -> `speaker` -> `"analyzer": "wayward_son_analyser"`
- settings -> analysis -> `"wayward_son_analyser"` -> `"char_filter": ["rename_filter"]`
- `"rename_filter"` -> `"pattern_replace"`

```json
PUT /henry4_hal
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
          "pattern": "PRINCE HENRY",
          "replacement": "WAYWARD PRINCE HAL"
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
    "index": "totality-all",
    "query": {
      "term": {
        "play_name": "Henry IV"
      }
    }
  },
  "dest":   { "index": "totality-custom-analyzer" }
}
```
</details>
<hr/>

:question: 3. verify by querying the `henry4_hal` index for the speaker `HAL`, `WAYWARD` and `PRINCE HENRY`

<details>
  <summary>View Solution (click to reveal)</summary>

```json
GET henry4_hal/_search
{
  "query": {
    "term": {
      "speaker": "HAL"
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

POST /{insert index here}/_async_search?size=0
{
  "sort": [
    { "@timestamp": { "order": "asc" } }
  ],
  "aggs": {
    "sale_date": {
      "date_histogram": {
        "field": "@timestamp",
        "calendar_interval": "1d"
      }
    }
  }
}
```

### Get Asynchronous Search Details
```json
GET /_async_search/{id}=
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

Step 2 Perform the search
The search could be:
How many have seconds_per_site above a specific value such as 200.

Bonus Options:
Search for state parks where the value (parks_in_city, parks_in_zipcode, or parks_in_state) are above 2.

## Performing Searches

### Write and execute a search query for terms and/or phrases in one or more fields of an index

How many times do the words New Hanpshire appear in the eclipse data? <br>
How many times do the words New and Hampshire appear in the eclipse data? <br>
How many state parks have 100% coverage? (in totality) <br>
How many state parks in Vermont are in the path of totality? <br>
How many places have Vermont, Maine, New Hampshire, or Oklahoma as the state? <br>
How many have totality minutes between 3 and 5 minutes? <br>


```json
GET shakespeare/_search
{
  "query": {
    "match": {
      "text_entry": {
        "query": "brothers blood",
        "operator": "and"
      }
    }
  }
}
```
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
GET shakespeare/_search
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
GET shakespeare/_search
{
  "query": {
    "range": {
      "speech_number": {
        "gte": 400,
        "lte": 402
      }
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
- Average coverage, average time, max time, min time > 0, sum of time for parks that are 100%, sum of total numnber of parks.

```json
GET {solar eclipse}/_search?filter_path=aggregations
{
  "size": 0,
  "query": {
    "match": {
      "geoip.country_iso_code": "US"
    }
  },
  "aggs": {
    "cart_stats": {
      "extended_stats": {
        "field": "taxful_total_price"
      }
    }
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
Display number of sites per zip code <br>
Display number of sites per city <br>
Display number of sites per totality minutes <br>

```json
GET kibana_sample_data_ecommerce/_search?filter_path=aggregations
{
  "size":0,
  "aggs": {
    "date_hist": {
      "date_histogram": {
        "field": "order_date",
        "calendar_interval": "1d",
        "min_doc_count": 1
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
There are several different sub-aggregations that we can run.
- Longest state park totality time?
- 
Display total totality minutes broken down by state
- The date_histogram is the Y-axis (time)
- Then you need to group by l
- Then Sum the sales (X-axis)

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

Putting all together:
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
      },
      "aggs": {
        "group_by_category": {
          "terms": {
            "field": "state.keyword",
            "size": 8
          },
          "aggs": {
            "total_sales_price": {
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
In the New Hampshire parks, highlight the Name starting the highlight with "#aaa# and ending it with #bbb#
```json
GET shakespeare/_search
{
    "query": {
        "match": {
            "text_entry":  "New Hampshire"
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
GET shakespeare/_search
{
    "query": {
        "term": {
            "state": "Vermont"
          }
    }, 
    "sort": [
      {
        "totality_minutes": {
          "order": "desc"
        }
      }
    ]
}
```

### Pagination 
Paginate the Totality results, 20 state parks per page, stating from state park 40 for the state of New Hampshire.
This can also be done for the other states in the dataset and sorted by zipcode or coverage.
```json
GET totality-all/_search
{
    "size": 20,
    "from": 40,
    "query": {
        "term": {
            "state": "New Hampshire"
          }
    }, 
    "sort": [
      {
        "coverage": {
          "order": "asc"
        }
      }
    ]
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
POST _scripts/get_lines
{
  "script": {
    "lang": "mustache",
    "source": {
      "query": {
        "bool": {
          "must": [
            {
              "term": {
                "play_name": "{{play_name}}"
              }
            },
            {
              "term": {
                "speaker": "{{ speaker }}"
              }
            }
          ]
        }
      }
    }
  }
}
```

## pull the template back

:warning: Note that it is not formatted nicely 😤

```json
GET _scripts/get_lines

// output

{
  "_id" : "get_lines",
  "found" : true,
  "script" : {
    "lang" : "mustache",
    "source" : """{"query":{"bool":{"must":[{"term":{"play_name":"{{play_name}}"}},{"term":{"speaker":"{{ speaker }}"}}]}}}""",
    "options" : {
      "content_type" : "application/json; charset=UTF-8"
    }
  }
}
```

## Test

I suppose you could see this as like a `SQL user-defined function` to be called externally with far less code available to the end-user.  No per-search-template (sql-function-like) security though.  Only read access to the underlying index is required. 🙄

```json
GET shakespeare/_search/template?filter_path=hits.hits.*.text_entry
{
    "id": "get_lines", 
    "params": {
        "play_name": "Cymbeline",
        "speaker" : "Attendant"
    }
}

// output

{
  "hits" : {
    "hits" : [
      {
        "_source" : {
          "text_entry" : "Please you, sir,"
        }
      },
      {
        "_source" : {
          "text_entry" : "Her chambers are all lockd; and theres no answer"
        }
      },
      {
        "_source" : {
          "text_entry" : "That will be given to the loudest noise we make."
        }
      }
    ]
  }
}
```
</details>
<hr>

## Slightly off topic, but on the exam!
## Configure an index so that it properly maintains the relationships of nested arrays of objects <br>
:question: 1. Using the below data, create an index with a mapping that allows for relationships to be queried.

```json
PUT totality_r/_doc/_bulk
{"index":{"_index":"totality_r","_id":"0"}}
{"name":"zipcode","relationship":[{"name":"statepark","type":"zipcode"}]}
{"index":{"_index":"henry4_r","_id":"1"}}
{"name":"FALSTAFF","relationship":[{"name":"PRINCE HENRY","type":"friend"}]}
{"index":{"_index":"henry4_r","_id":"2"}}
{"name":"HOTSPUR","relationship":[{"name":"PRINCE HENRY","type":"foe"}]}
{"index":{"_index":"henry4_r","_id":"3"}}
{"name":"WESTMORELAND","relationship":[{"name":"KING HENRY IV","type":"friend"}]}
{"index":{"_index":"henry4_r","_id":"4"}}
{"name":"WESTMORELAND","relationship":[{"name":"KING HENRY IV","type":"friend"}]}
{"index":{"_index":"henry4_r","_id":"5"}}
{"name":"EARL OF WORCESTER","relationship":[{"name":"KING HENRY IV","type":"foe"}]}
{"index":{"_index":"henry4_r","_id":"6"}}
{"name":"GLENDOWER","relationship":[{"name":"KING HENRY IV","type":"foe"}]}
{"index":{"_index":"henry4_r","_id":"7"}}
{"name":"EARL OF DOUGLAS","relationship":[{"name":"KING HENRY IV","type":"foe"}]}
{"index":{"_index":"henry4_r","_id":"8"}}
{"name":"MORTIMER","relationship":[{"name":"KING HENRY IV","type":"foe"}]}
{"index":{"_index":"henry4_r","_id":"9"}}
{"name":"VERNON","relationship":[{"name":"EARL OF WORCESTER","type":"friend"}]}
{"index":{"_index":"henry4_r","_id":"10"}}
{"name":"NORTHUMBERLAND","relationship":[{"name":"HOTSPUR","type":"father"}, {"name":"KING HENRY IV","type":"foe"}]}
{"index":{"_index":"henry4_r","_id":"11"}}
{"name":"SIR WALTER BLUNT","relationship":[{"name":"KING HENRY IV","type":"friend"}]}
{"index":{"_index":"henry4_r","_id":"12"}}
{"name":"GADSHILL","relationship":[{"name":"PRINCE HENRY","type":"friend"}]}
{"index":{"_index":"henry4_r","_id":"13"}}
{"name":"ARCHBISHOP OF YORK","relationship":[{"name":"KING HENRY IV","type":"foe"}]}
{"index":{"_index":"henry4_r","_id":"14"}}
{"name":"POINS","relationship":[{"name":"FALSTAFF","type":"friend"}]}
{"index":{"_index":"henry4_r","_id":"15"}}
{"name":"BARDOLPH","relationship":[{"name":"FALSTAFF","type":"friend"}]}
{"index":{"_index":"henry4_r","_id":"16"}}
{"name":"PETO","relationship":[{"name":"FALSTAFF","type":"friend"}]}
{"index":{"_index":"henry4_r","_id":"17"}}
{"name":"PRINCE HENRY","relationship":[{"name":"FALSTAFF","type":"friend"}, {"name":"PETO","type":"friend"},{"name":"BARDOLPH","type":"friend"},{"name":"POINS","type":"friend"},{"name":"GADSHILL","type":"friend"}]}
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

:question: 2. Then query all items in the index that are the `first` of `STATE`

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
            { "match": { "relationship.name": "STATE" }},
            { "match": { "relationship.type":  "first" }} 
          ]
        }
      }
    }
  }
}
// output

{
  "hits" : {
    "hits" : [
      {
        "_source" : {
          "name" : "Texas"
        }
      }
    ]
  }
}
```

</details>
<hr/>

:question: 3. Show all state parks in the specific and neighboring zip codes.


<details>
  <summary>View Solution (click to reveal)</summary>

There should be four.  This is where the nesting comes into play as `FALSTAFF` himself decribes `PRINCE HENRY` as his only friend. But other people describe `FALSTAFF` as their friend.

```
PRINCE HENRY -> FALSTAFF
FALSTAFF -> PRINCE HENRY
POINS -> FALSTAFF
BARDOLPH -> FALSTAFF
PETO -> FALSTAFF
```

# query

```json
GET henry4_r/_search?filter_path=*.*.*.name
{
  "query": {
    "nested": {
      "path": "relationship",
      "query": {
        "bool": {
          "must": [
            { "match": { "relationship.name": "FALSTAFF" }},
            { "match": { "relationship.type":  "friend" }} 
          ]
        }
      }
    }
  }
}

// output

{
  "hits" : {
    "hits" : [
      {
        "_source" : {
          "name" : "POINS"
        }
      },
      {
        "_source" : {
          "name" : "BARDOLPH"
        }
      },
      {
        "_source" : {
          "name" : "PETO"
        }
      },
      {
        "_source" : {
          "name" : "PRINCE HENRY"
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
