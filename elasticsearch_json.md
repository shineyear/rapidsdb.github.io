---
layout: default
---

[back](./)

# Elasticsearch json export and import into Rapids DB

1.Create index in elasticsearch

```shell
index.json 

{
  "settings": {
    "number_of_shards": 1
  },
  "mappings": {
    "properties": {
      "field1": { "type": "text" }
    }
  }
}


curl -XDELETE http://localhost:9200/my_index
curl -XPUT http://localhost:9200/my_index -H 'Content-Type: application/json' -d@/index.json
```

2.Insert data into elasticsearch

```shell
data.json


{
  "@timestamp": "2099-11-15T13:12:00",
  "message": "GET /search HTTP/1.1 200 1070000",
  "field1": "kimchy"
}



curl -XPOST http://localhost:9200/my_index/_doc/ -H 'Content-Type: application/json' -d@/data.json
```

3.Export to as json

```shell
curl -X GET 'http://localhost:9200/my_index/_search' | jq -r '.["hits"]["hits"][]' > infile.json


{
  "_index": "my_index",
  "_type": "_doc",
  "_id": "3sMhlXwBOkvgpBzouRFw",
  "_score": 1,
  "_source": {
    "@timestamp": "2099-11-15T13:12:00",
    "message": "GET /search HTTP/1.1 200 1070000",
    "field1": "kimchy"
  }
}
{
  "_index": "my_index",
  "_type": "_doc",
  "_id": "38MhlXwBOkvgpBzo6RFd",
  "_score": 1,
  "_source": {
    "@timestamp": "2099-11-15T13:12:00",
    "message": "GET /search HTTP/1.1 200 1070000",
    "field1": "kimchy"
  }
}
{
  "_index": "my_index",
  "_type": "_doc",
  "_id": "4MMhlXwBOkvgpBzo7REd",
  "_score": 1,
  "_source": {
    "@timestamp": "2099-11-15T13:12:00",
    "message": "GET /search HTTP/1.1 200 1070000",
    "field1": "kimchy"
  }
}
{
  "_index": "my_index",
  "_type": "_doc",
  "_id": "4cMhlXwBOkvgpBzo8BHd",
  "_score": 1,
  "_source": {
    "@timestamp": "2099-11-15T13:12:00",
    "message": "GET /search HTTP/1.1 200 1070000",
    "field1": "kimchy"
  }
}
```

4.Create table in Rapids DB

```sql
create table test ( 
_id varchar(10),
_index varchar(20),
_score int,
field varchar(100),
message varchar(50),
timest datetime(6),
raw json,
KEY(_id), 
KEY(timest)
);
```

5.Load into Rapids DB

```sql
LOAD DATA LOCAL INFILE "infile.json" INTO TABLE test FORMAT JSON (
_id <- _id default NULL,
_index <- _index default NULL,
_score <- _score DEFAULT 0,
@avar <- _source default NULL,
raw <- % default NULL
)
SET 
field = json_extract_string(@avar, 'field1'),
message = json_extract_string(@avar, 'message'),
timest = to_date(json_extract_string(@avar, '@timestamp'), 'YYY-MM-DDTHH:MM:SS');
```





