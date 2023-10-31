### id 지정 없이 문서 생성

```
POST /dept/_doc/
{
  "deptName": "software",
  "id": 202312345,
  "studentName": "gildong"
}
```

response :

```
{
  "_index": "dept",
  "_id": <_id>,
  "_version": 1,
  "result": "created",
  "_shards": {
    "total": 2,
    "successful": 1,
    "failed": 0
  },
  "_seq_no": 2,
  "_primary_term": 2
}
```

id 가 랜덤으로 생성된다.

```
GET /dept/_doc/<_id>
```

조회 할 때는 랜덤으로 부여된 id 를 통해 조회한다.

### id 지정하여 문서 생성

```
POST /dept/_doc/1
{
  "deptName": "software",
  "id": 202312345,
  "studentName": "gildong"
}
```

```
GET /dept/_doc/1
```

지정한 id 로 조회 가능하다.

### PUT

```
PUT /dept/_doc/1
{
  "id": 202311111
}
```

response :

```
{
  "_index": "dept",
  "_id": "1",
  "_version": 2,
  "result": "updated",
  "_shards": {
    "total": 2,
    "successful": 1,
    "failed": 0
  },
  "_seq_no": 4,
  "_primary_term": 2
}
```

_version 값이 1 증가하였다.

### 

```
POST /dept/_doc/1
{
  "deptName": "software",
  "id": 202312345,
  "studentName": "gildong"
}
```

위에서 썼던 api 를 다시 요청해보자.

```
{
  "_index": "dept",
  "_id": "1",
  "_version": 3,
  "result": "updated",
  "_shards": {
    "total": 2,
    "successful": 1,
    "failed": 0
  },
  "_seq_no": 5,
  "_primary_term": 2
}
```

POST 요청을 했는데 _version 값이 올라간 것을 확인할 수 있다.
elasticsearch 에서는 일반적으로 id 값이 이미 존재하는 경우에는 update 작업을 하고, id 값이 없을 경우에는 create 작업을 한다.

#### op_type 사용

https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-index_.html

```
PUT /dept/_doc/1?op_type=create
{
  "deptName": "software",
  "id": 202312345,
  "studentName": "chulsu"
}
```

op_type 을 지정해주면 result 값을 create 만 나올 수 있게 강제한다.

```
{
  "error": {
    "root_cause": [
      {
        "type": "version_conflict_engine_exception",
        "reason": "[1]: version conflict, document already exists (current version [4])",
        "index_uuid": "Q0iX5rh4RAum7TfTLZ59dw",
        "shard": "0",
        "index": "dept"
      }
    ],
    "type": "version_conflict_engine_exception",
    "reason": "[1]: version conflict, document already exists (current version [4])",
    "index_uuid": "Q0iX5rh4RAum7TfTLZ59dw",
    "shard": "0",
    "index": "dept"
  },
  "status": 409
}
```

이미 존재하는 문서에 대해 자동으로 update 하는 것을 방지한다.

### 존재하지 않는 mapping 선언

```
POST /dept/_doc/2
{
  "deptName": "software",
  "id": 202311333,
  "studentName": "hosu",
  "gender": "male"
}
```

index 생성할 때 지정하지 않았던 gender 매핑을 추가했다.

response :

```
{
  "_index": "dept",
  "_id": "2",
  "_version": 1,
  "result": "created",
  "_shards": {
    "total": 2,
    "successful": 1,
    "failed": 0
  },
  "_seq_no": 7,
  "_primary_term": 2
}
```

요청이 성공했다.

```
GET /dept/_mapping
```

인덱스 정보를 조회 해보자.

```
{
  "dept": {
    "mappings": {
      "properties": {
        "deptName": {
          "type": "keyword"
        },
        "gender": {
          "type": "text",
          "fields": {
            "keyword": {
              "type": "keyword",
              "ignore_above": 256
            }
          }
        },
        "id": {
          "type": "integer"
        },
        "studentName": {
          "type": "text"
        }
      }
    }
  }
}
```

gender 가 자동으로 추가된 것을 확인할 수 있다.

### 동적 매핑 해제

매핑이 자동으로 확장되는 것을 막고자 한다면 아래와 같이 진행한다.

```
DELETE /dept
```

기존 인덱스를 먼저 삭제한다.

```
PUT /dept
{
  "mappings": {
    "dynamic": "strict",
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

`"dynamic": "stirct"` 속성을 추가해주었다.

```
POST /dept/_doc/1
{
  "deptName": "software",
  "id": 202311111,
  "studentName": "gildong",
  "gender": "male"
}
```

위 요청을 하였을 때 요청이 실패되는 것을 확인할 수 있다.

### Delete API

```
DELETE /dept/_doc/1
```

문법은 다른 메소드와 동일하다.

#### Delete by query

```
POST /dept/_delete_by_query
{
  "query": {
    "match": {
      "studentName": "gildong"
    }
  }
}
```

json 형식의 쿼리를 통하여 문서의 내용을 찾아서 삭제할 수 있다.

### Update API

```
POST /dept/_doc/1
{
  "deptName": "software",
  "id": 202312345,
  "studentName": "gildong"
}
```

먼저 위 문서를 다시 생성해준다.

```
POST /dept/_update/1
{
  "script" : {
    "source": "ctx._source.studentName = params.newName",
    "lang": "painless",
    "params" : {
      "newName" : "minsu"
    }
  }
}
```

여기서는 source, params 만 살펴보면 되는데
source 는 RDBMS 에서 where 조건과 비슷한 역할을 하고, params 는 where 조건에 대입될 값과 유사하다.

### Bulk API

https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-bulk.html

```
POST _bulk
{ "index" : { "_index" : "dept", "_id" : "1" } }
{ "studentName" : "gildong" }
{ "delete" : { "_index" : "dept", "_id" : "2" } }
{ "create" : { "_index" : "dept", "_id" : "3" } }
{ "studentName" : "chulsu" }
{ "update" : {"_id" : "1", "_index" : "dept"} }
{ "doc" : {"deptName" : "English"} }
```

```
GET /dept/_doc/1
GET /dept/_doc/2
GET /dept/_doc/3
```
