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
            type: "whatsapp",
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
    "contacts.type": ["whatsapp", "phone"],
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

## Get API
- get data with metadata `GET /<index>/_doc/<id>`
- get data without metadata `GET /<index>/_source/<id>`
- return 404 if not exists

## Check exists API
- `HEAD /<index>/_doc/<id>` return 200 or 404

## Multiget API
- get from all index `POST /_mget`
- get from specific index `POST /<index>/_mget`

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