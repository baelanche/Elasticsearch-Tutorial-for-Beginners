## [Reference](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices.html)

<br/>

1 인덱스 생성

```
PUT /dept
```

<br/>

2 인덱스 조회

```
GET /dept
```

<br/>

3 인덱스 삭제

```
DELETE /dept
```

<br/>

4 인덱스 생성 - 매핑

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

<br/>

5 인덱스 조회

```
GET /dept
```

<br/>

6 인덱스 조회 - 매핑

```
GET /dept/_mapping
```
