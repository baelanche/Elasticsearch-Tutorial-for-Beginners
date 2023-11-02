## [Reference](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs.html)

<br/>

### 1 - id 지정 없이 문서 생성

```json
POST /dept/_doc/
{
  "deptName": "software",
  "id": 202312345,
  "studentName": "gildong"
}
```

Response:

```json
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

```json
GET /dept/_doc/<_id>
```

조회 할 때는 랜덤으로 부여된 id 를 통해 조회한다.

<br/>

### 2 - id 지정하여 문서 생성

```json
POST /dept/_doc/1
{
  "deptName": "software",
  "id": 202312345,
  "studentName": "gildong"
}
```

```json
GET /dept/_doc/1
```

지정한 id 로 조회 가능하다.

<br/>

### 3 - 문서 생성/수정

```json
PUT /dept/_doc/1
{
  "id": 202311111
}
```

Response:

```json
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

<br/>

```json
POST /dept/_doc/1
{
  "deptName": "software",
  "id": 202312345,
  "studentName": "gildong"
}
```

POST 메소드를 호출해보자.

```json
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

_version 값이 올라간 것을 확인할 수 있다.  
elasticsearch 에서는 일반적으로 id 값이 이미 존재하는 경우에는 update 작업을 하고, id 값이 없을 경우에는 create 작업을 하기 때문이다.

<br/>

### 3.2 - [op_type](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-index_.html)

```json
PUT /dept/_doc/1?op_type=create
{
  "deptName": "software",
  "id": 202312345,
  "studentName": "chulsu"
}
```

op_type=create 를 통해 이미 존재하는 문서에 대한 update 를 방지할 수 있다.

Response:

```json
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

### 4 - 존재하지 않는 mapping 선언

```json
POST /dept/_doc/2
{
  "deptName": "software",
  "id": 202311333,
  "studentName": "hosu",
  "gender": "male"
}
```

index 생성할 때 기존에 없던 gender 매핑을 추가했다.

Response :

```json
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

```json
GET /dept/_mapping
```

인덱스 정보를 조회 해보자.

```json
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

gender 매핑이 자동으로 추가된 것을 확인할 수 있다.

<br/>

### 4.2 - 동적 매핑 해제

```json
PUT /dept-static
{
  "mappings": {
    "dynamic": "strict",
    "properties": {
      "deptName": {
        "type": "keyword"
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
```

`"dynamic": "stirct"` 속성을 추가한다.

```json
POST /dept-static/_doc/1
{
  "deptName": "software",
  "id": 202311111,
  "studentName": "gildong",
  "gender": "male"
}
```

위 요청을 하였을 때 요청이 실패하는 것을 확인할 수 있다.

<br/>

### 5 - 문서 삭제

```json
DELETE /dept/_doc/1
```

<br/>

### 5.2 - 문서 삭제(쿼리)

```json
POST /dept/_delete_by_query
{
  "query": {
    "match": {
      "studentName": "gildong"
    }
  }
}
```

json 형식의 쿼리를 통하여 문서의 내용을 비교하여 삭제할 수 있다.

<br/>

### 6 - 문서 수정(쿼리)

```json
POST /dept/_doc/1
{
  "deptName": "software",
  "id": 202312345,
  "studentName": "gildong"
}
```

문서를 다시 생성해준다.

```json
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

<br/>

## [Bulk API](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-bulk.html)

한번에 여러번의 요청을 수행할 수 있다.  
요청 도중에 오류가 발생해도 롤백과 같은 방법을 취할 수 없다.

```json
POST _bulk
{ "index" : { "_index" : "dept", "_id" : "1" } }
{ "studentName" : "gildong" }
{ "delete" : { "_index" : "dept", "_id" : "2" } }
{ "create" : { "_index" : "dept", "_id" : "3" } }
{ "studentName" : "chulsu" }
{ "update" : {"_id" : "1", "_index" : "dept"} }
{ "doc" : {"deptName" : "English"} }
```

```json
GET /dept/_doc/1
GET /dept/_doc/2
GET /dept/_doc/3
```
