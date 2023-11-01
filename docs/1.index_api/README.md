## [Reference](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices.html)

<br/>

1 인덱스 생성

```json
PUT /dept
```

<br/>

2 인덱스 조회

```json
GET /dept
```

<br/>

3 인덱스 삭제

```json
DELETE /dept
```

<br/>

4 인덱스 생성 - 매핑

```json
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
      "studentName": {
        "type": "keyword"
      }
    }
  }
}
```

<br/>

5 인덱스 조회

```json
GET /dept
```

<br/>

6 매핑 수정

```json
PUT /dept/_mapping
{
  "properties": {
    "studentName": {
      "type": "text"
    }
  }
}
```

<br/>

7 인덱스 조회 - 매핑

```json
GET /dept/_mapping
```
