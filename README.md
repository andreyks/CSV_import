# CSV → CREATE TABLE

A minimal Symfony Console CLI tool that reads a CSV file and generates a PostgreSQL-compatible `CREATE TABLE` statement. 

Requires PHP ≥ 8.5 and Composer.

## Install steps

    $ git clone ...
    $ cd folder
    $ composer install

## Usage

    $ php app.php create-table data/employees.csv  

OR

    $ chmod +x app.php 
    $ ./app.php create-table data/employees.csv 


## Notes

The implementation assumes a maximum field length of 255 characters.
Data folder is added to git as example. Avoid to push real customer data into git.

## Roadmap

- Validate if table exists
    CREATE TABLE IF NOT EXISTS table_name
- Improve type detection, add more types
- Detect column length from CSV file values

## Seach integration

Q: How would you extend your existing Symfony application to handle indexing and searching the employee data?
Points to consider:
- What would the document and mapping file look like?
- These CSVs could potentially grow in size to 100k+ rows.
- Should any fields support fuzzy matching?
- How would we apply filters or custom sorting?

A:
- Depending on new application purpose (CLI commands, REST API, Web pages) additional Symfony composer dependencies should be installed - runtime, flex, framework-bundle, yaml, dotenv.
- Install Elasticsearch library elastic/elasticsearch-php, use https://www.elastic.co/docs/reference/elasticsearch/clients/php/getting-started as documentation
- Create Index/Reindex Commands / Controller / Endpoints
-- connection, create/recreate index
-- import CSV in batch. 500 rows per request, use `fgets` for quick position. Apply validate, mapping to each row, logs errors
- Create Search Commands / Controller / Endpoints  
-- sort, default sort field  
-- facets, filter for Grade  
-- range for Age  
-- paginator  
-- there is only one text field, but if other table has multiple text fields, we need to define their weights, ex.: `["title^3", "description"]`  
-- render results (json / html)  
- Mapping example based on CSV from Task 1:
`config/elasticsearch/mapping.json`
```json
{
  "settings": {
    "analysis": {
      "analyzer": {
        "name_analyzer": {
          "type": "custom",
          "tokenizer": "standard",
          "filter": ["lowercase", "asciifolding"]
        }
      }
    }
  },
  "mappings": {
    "dynamic": "strict",
    "properties": {
      "name":   { "type": "text", "analyzer": "name_analyzer",
                  "fields": { "keyword": { "type": "keyword", "ignore_above": 256 } } },
      "age":    { "type": "integer" },
      "grade":  { "type": "keyword" },
      "salary": { "type": "double" }
    }
  }
}
```
Name is available as for sort and key word search (`.keyword` sub-field for exact sort) as for fuzzy search (`name_analyzer` (lowercase + asciifolding)).  
Salary was defined as `double`. It requires changing in CSV import part.

```
"bool.must": [{
    "multi_match": {
        "query": "alice",
        "fields": ["name"],
        "fuzziness": "AUTO"
    }
}]          -> text search with relevance
bool.filter -> term(grade), range(salary), range(age) — only added when option is non-null, filtering is cachable
    { "range": { "salary": { "gte": 60000, "lte": 80000 } } },

sort        -> name.keyword / salary / age / grade
```

Q: "How would you architect keeping the SQL database and ElasticSearch in sync in a production environment when a user updates an employee's salary via a web interface?"

A: When a user updates an employee's salary through a web interface backed by PostgreSQL, ElasticSearch must be kept in sync to avoid serving stale search results. The simplest way is updating index in the same request. The recommended pattern is the dual-write with outbox: the web application writes the salary change to PostgreSQL inside a transaction and simultaneously appends a change event to an `outbox` table in the same transaction. A lightweight background worker (a Symfony Messenger consumer, a queue worker, or a cron-driven CLI command) reads new outbox rows and issues a partial `_update` call to ElasticSearch using the document's existing `_id` (the employee's primary key). This guarantees that the SQL change and the outbox entry are atomic — either both commit or neither does. For higher throughput, the outbox worker can batch multiple updates into a single ES `_bulk` request. If ElasticSearch is temporarily unavailable, unprocessed outbox rows remain in PostgreSQL and are retried, preventing data loss. Index full-resyncs (`php app.php index-employees data/employees.csv --recreate`) serve as a recovery mechanism after prolonged outages or schema changes.


## Coding Standards

To automatically fix your project, or only check against the need of changes, run:

    $ ./vendor/bin/php-cs-fixer fix  
    $ ./vendor/bin/php-cs-fixer check --diff  

See https://cs.symfony.com/ for more information.