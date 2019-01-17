### Step 1: Create a new template
- We are creating a new template called **accent_feature_template** and map its property to our specified index called **accent_feature_index**.
- We are using standard_tokenizer, which splits words into tokens and using ascii folding filter
 which converts alphabetic, numeric, and symbolic Unicode characters which are not in the first 127 ASCII characters (the "Basic Latin" Unicode block) into their ASCII equivalents

```
curl -X PUT \
  http://localhost:9200/_template/accent_feature_template \
  -H 'Content-Type: application/json' \
  -H 'Postman-Token: e57a30ce-8236-4902-b23f-f5ceae149583' \
  -H 'cache-control: no-cache' \
  -d '{
  "template": "accent_feature_index",
  "settings": {
    "number_of_shards": 1,
    "analysis": {
      "analyzer": {
        "folding": {
          "tokenizer": "standard",
          "filter":  [ "lowercase", "asciifolding" ]
        }
      }
    }
  },
  "mappings": {
    "publications":{
    	"properties":{
    		"title":{
    			"type":"text",
    			"analyzer": "folding"
    		}
    	}
    }
    
  }
}'
```
### Step 2: Create a new index
```
curl -X PUT \
  http://localhost:9200/accent_feature_index \
  -H 'Postman-Token: 29026b16-fe42-477a-8401-8f199f763dc0' \
  -H 'cache-control: no-cache'
```

### Step 3: Adding documents to our index
```
curl -X POST \
  http://localhost:9200/accent_feature_index/publications \
  -H 'Content-Type: application/json' \
  -H 'Postman-Token: 2a4ef61c-a9e1-4790-bf61-4f78dcd93551' \
  -H 'cache-control: no-cache' \
  -d '{
	"title":"Ayşe"
}'
```

```
curl -X POST \
  http://localhost:9200/accent_feature_index/publications \
  -H 'Content-Type: application/json' \
  -H 'Postman-Token: 2a4ef61c-a9e1-4790-bf61-4f78dcd93551' \
  -H 'cache-control: no-cache' \
  -d '{
	"title":"Ayse"
}'
```
```
curl -X POST \
  http://localhost:9200/accent_feature_index/publications \
  -H 'Content-Type: application/json' \
  -H 'Postman-Token: 2a4ef61c-a9e1-4790-bf61-4f78dcd93551' \
  -H 'cache-control: no-cache' \
  -d '{
	"title":"aeeiij"
}'
```
```
curl -X POST \
  http://localhost:9200/accent_feature_index/publications \
  -H 'Content-Type: application/json' \
  -H 'Postman-Token: 2a4ef61c-a9e1-4790-bf61-4f78dcd93551' \
  -H 'cache-control: no-cache' \
  -d '{
	"title":"àéëïĳ"
}'
```
```
curl -X POST \
  http://localhost:9200/accent_feature_index/publications \
  -H 'Content-Type: application/json' \
  -H 'Postman-Token: 2a4ef61c-a9e1-4790-bf61-4f78dcd93551' \
  -H 'cache-control: no-cache' \
  -d '{
	"title":"àeeiĳ"
}'
```
### Step 4: Performing search 

```
curl -X GET \
  http://localhost:9200/accent_feature_index/publications/_search \
  -H 'Content-Type: application/json' \
  -H 'Postman-Token: 863e3a26-99a9-4f32-b952-4cb7da4e0049' \
  -H 'cache-control: no-cache' \
  -d '{
	"query":{
		"multi_match" : {
            "query" : "aeeiĳ",
            "fields" : ["title"]
        }
	},
	"_source": true 
	
}'
```
Results for the above curl search request is:
```
{
    "took": 1,
    "timed_out": false,
    "_shards": {
        "total": 1,
        "successful": 1,
        "failed": 0
    },
    "hits": {
        "total": 3,
        "max_score": 0.5389965,
        "hits": [
            {
                "_index": "accent_feature_index",
                "_type": "publications",
                "_id": "AWhbBhXrCBuUsC_Vh7ae",
                "_score": 0.5389965,
                "_source": {
                    "title": "aeeiij"
                }
            },
            {
                "_index": "accent_feature_index",
                "_type": "publications",
                "_id": "AWhbBjIhCBuUsC_Vh7af",
                "_score": 0.5389965,
                "_source": {
                    "title": "àéëïĳ"
                }
            },
            {
                "_index": "accent_feature_index",
                "_type": "publications",
                "_id": "AWhbBlBGCBuUsC_Vh7ag",
                "_score": 0.5389965,
                "_source": {
                    "title": "àeeiĳ"
                }
            }
        ]
    }
}
```
