### id 지정 없이 문서 생성

```
POST dept/_doc/
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
  "_id": <id>,
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
GET dept/_doc/<id>
```

조회 할 때는 랜덤으로 부여된 id 를 통해 조회한다.

### id 지정하여 문서 생성

```
POST dept/_doc/1
{
  "deptName": "software",
  "id": 202312345,
  "studentName": "gildong"
}
```

```
GET dept/_doc/1
```

지정한 id 로 조회 가능하다.

### Update

```
PUT dept/_doc/1
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
POST dept/_doc/1
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
PUT dept/_doc/1?op_type=create
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
