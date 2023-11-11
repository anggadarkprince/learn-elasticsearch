# Elasticsearch
## Install elasticsearch
- https://www.elastic.co/downloads/elasticsearch
- edit `config/elasticsearch.yml` if necessary
- `./bin/elasticsearch`
- open `http://localhost:9200`

## Lucene document
**elastic.json**
```json
{
    "_id": "324",
    "name": "Angga Ari"
    "gender": "male",
    emails: [
        "angga.aw92@mail.com",
        "angga123@mail",
    ],
    "location": {
        city: "Surabaya",
        country: "Indonesia",
    },
    "contacts: [
        {
            type: "whatsapp primary",
            value: "2853423"
        },
        {
            type: "phone",
            value: "2853423"
        }
    ]
}
```
**lucene.doc**
```json
{
    "_id": ["324"],
    "name": ["Angga Ari"]
    "gender": ["male"],
    "location": ["Surabaya"]
    "emails": ["angga.aw92@mail.com", "angga123@mail"],
    "location.city": ["Surabaya"],
    "location.country": ["Indonesia"],
    "contacts.type": ["whatsapp", "primary", "phone"],
    "contacts.value: ["2853423"] // only single because other value is same
}
```

## Index
- similar to table in relational database
- elasticsearch doesn't have database separation
- usually we add prefix app name to distnguist between app in same elasticsearch server
- should be lowercase, allow use - + and _ (not in the front of name), max 255 bytes

### Create index customer, product, orders
PUT http://localhost:9200/customers
PUT http://localhost:9200/products
PUT http://localhost:9200/orders

### Get all orders
GET http://localhost:9200/_cat/indices?v

### Delete index
DELETE http://localhost:9200/customers

## Dynamic mapping
- not store null value
- true/false as boolean
- double as float
- long as long
- array depends on the first element type
- string can be parsed as date, float, long, text depends on it can be detected as known type or not
- string date yyyy/MM/dd HH:mm:ss by default will be parsed as date (auto parsing can be disabled: `date_detection` to false), format date can be configured: `dynamic_date_formats` (multiple format can be detected)

# Elasticsearch API
## Create API
- store data into index `POST / PUT /<index>/_create/<id>`
{
    field: value
}
- will return conflict response when id is exist, see Index API

## Get API
- get data with metadata `GET /<index>/_doc/<id>`
- get data without metadata `GET /<index>/_source/<id>`
- return 404 if not exists

## Check exists API
- `HEAD /<index>/_doc/<id>` return 200 or 404

## Multiget API
- get from all index `POST /_mget`
{
    "docs": [
        {
            "_id": 1,
            "_index": "orders"
        },
        ...
    ]
}
- get from specific index `POST /<index>/_mget`
{
    "ids": ["1", "2", ...]
}

## Search API
- `POST /_search`
- `POST /<index>/_search`

## Pagination API
- using query param `from`, default 0
- `size` as total each page, default 10

## Sorting API
- `<field>:<direction>`
- direction can be `asc` or `desc`
- for multi sort using coma (,) as separator: `field1:asc,field2:desc`

## Index API for create and update
- Create if not exist and update if exist (replace)
- `POST /<index>/_doc/<id>`
{
    field: value
}

## Update API for edit some field of document (not replaced)
- `POST /<index>/_update/<id>`
{
    "doc": {
        field: value
    }
}

## Delete API to delete document
- `DELETE /<index>/_doc/<id>`

## Bulk API
- `POST /_bulk`
- `POST /<index>/_bulk`

## Alias
- alias for index, usefull when we have multiple version of index, client mention the index rather than direct index name
- `POST /_aliases`
{
    "actions": [
        {"add": {"alias": "customer", "index": "customers"}},
        {"remove": {"alias": "customer", "index": "customers"}}
    ]
}

## Reindex API
- Copy data index to new index
- `POST /_reindex`
{
    "source": {"index": "orders"},
    "dest": {"index": "order_v2"}
}

## Source Field
- Elasticsearch store data into lucine document but add original json in `_source` key
- Size of document will be doubled (lucine doc + original json data)
- Filter field in parameter, search or get (_doc, _source) API
- `_search?_source_includes`: include fields of document
- `_doc/<id>?_source_excludes`: exclude fields of document