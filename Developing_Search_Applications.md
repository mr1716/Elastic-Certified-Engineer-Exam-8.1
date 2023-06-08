# Developing Search Applications

# Highlight the search terms in the response of a query
https://www.elastic.co/guide/en/elasticsearch/reference/8.1/highlighting.html

:question: In the spoken lines of the play, highlight the word `Hamlet` starting the highlight with `"#aaa#` and ending it with `#bbb#`

<details>
  <summary>View Solution (click to reveal)</summary>

```json
GET shakespeare/_search
{
    "query": {
        "match": {
            "text_entry":  "Hamlet"
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
</details>
<hr>

# Sort the results of a query by a given set of requirements <br>
https://www.elastic.co/guide/en/elasticsearch/reference/8.1/sort-search-results.html <br>
:question: Return all of `Othellos` lines in reverse order.

<details>
  <summary>View Solution (click to reveal)</summary>

```json
GET shakespeare/_search
{
    "query": {
        "term": {
            "speaker": "OTHELLO"
          }
    }, 
    "sort": [
      {
        "speech_number": {
          "order": "desc"
        }
      }
    ]
}
```
</details>
<hr>

# Implement pagination of the results of a search query

https://www.elastic.co/guide/en/elasticsearch/reference/8.1/search-request-from-size.html
https://www.elastic.co/guide/en/elasticsearch/reference/8.1/paginate-search-results.html

:question: Paginate the `Othello` play, `20` speech lines per page, stating from line `40`.

:question: What is the first line on this page?

<details>
  <summary>View Solution (click to reveal)</summary>


```json
GET shakespeare/_search
{
    "size": 20,
    "from": 40,
    "query": {
        "term": {
            "play_name": "Othello"
          }
    }, 
    "sort": [
      {
        "speech_number": {
          "order": "asc"
        }
      }
    ]
}

// Output

      {
        "_source" : {
          "text_entry" : "Will you think so?"
        }
      }
```

</details>
<hr>




# Define and use index aliases

## part 1

:question: Define an index alias for `accounts-raw` called `accounts-all`

<details>
  <summary>View Solution (click to reveal)</summary>

```json
POST /_aliases
{
  "actions": [
    {
      "add": {
        "index": "accounts-raw",
        "alias": "accounts-all"
      }
    }
  ]
}
```

Check that the document count matches

```json
GET accounts-all/_count
```
</details>
<hr>

## part 2

:question: Define an index alias for `accounts-raw` called `accounts-male`

:question: Apply a filter to only show the male account owners.

<details>
  <summary>View Solution (click to reveal)</summary>

https://www.elastic.co/guide/en/elasticsearch/reference/8.1/indices-aliases.html

1. check that the feild you want to filter is a keyword

```json
GET accounts-raw/_mapping/field/gender

// Output 

{
  "accounts-raw" : {
    "mappings" : {
      "gender" : {
        "full_name" : "gender",
        "mapping" : {
          "gender" : {
            "type" : "text",
            "fields" : {
              "keyword" : {
                "type" : "keyword",
                "ignore_above" : 256
              }
            }
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
        "index": "accounts-raw",
        "alias": "accounts-male",
        "filter": {
          "term": {
            "gender.keyword": "M"
          }
        }
      }
    }
  ]
}
```

3. test

```json
GET accounts-male/_count

// Output 

{
  "count" : 507,
  "_shards" : {
    "total" : 1,
    "successful" : 1,
    "skipped" : 0,
    "failed" : 0
  }
}
```

4. :question: BONUS: Run a query to do the same on `accounts-raw` index

Extra bonus: only print the total hits

```json
POST accounts-raw/_search?filter_path=hits.total.value
{
  "query": {
    "match": {
      "gender": "M"
    }
  }
}

// Output 

{
  "hits" : {
    "total" : {
      "value" : 507
    }
  }
}
```
</details>
<hr>

# Define and use a search template

https://www.elastic.co/guide/en/elasticsearch/reference/8.1/search-template.html

:question: Create and use a search template that returns the lines of a person in a play.

:question: Show all lines that belong to `Attendant` in the play `Cymbeline`

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
