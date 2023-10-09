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

### Update

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
