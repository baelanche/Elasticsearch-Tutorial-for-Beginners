1

```
PUT /dept
```

2

```
GET /dept
```

3

```
DELETE /dept
```

4

```
PUT /dept
{
  "mappings": {
    "properties": {
      "deptName":{
        "type":"keyword"
        
      },
      "id":{
        "type":"integer"
      },
      "studentName":{
        "type":"text"
      }
    }
  }
}
```

5

```
GET /dept
```

6

```
GET /dept/_mapping
```
