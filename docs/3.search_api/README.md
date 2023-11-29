## [Reference](https://www.elastic.co/guide/en/elasticsearch/reference/current/search.html)

<br/>

### 1 - URI Search

```
POST dept_search/_search?q=studentName:stu01
```

```
"hits": [
      {
        ...

        "_score": ..., // 문서의 점수

        ...
      }
    ]
```

> 자주 쓰는 명령어  
> `q` : 검색을 수행할 쿼리  
> `analyze_wildcard` : 와일드카드의 활성화 여부 지정(default: false)  
> `sort` : 검색 결과의 정렬 필드  
> `from` : 검색을 시작할 문서의 위치  
> `size` : 검색 결과 개수

<br/>

```
POST dept_search/_search?q=studentName:* AND id:202300001&from=0&size=10
```
<br/>

### 2 - Request Body Search

```json
POST dept_search/_search
{
  "query": {
    "query_string": {
      "default_field": "studentName",
      "query": "studentName:stu01"
    }
  }
}
```

Query DSL 이라 불리는 문법으로 json 형태의 쿼리를 작성한다.

<br/>

### 3 - Query DSL

#### term

```json
POST dept_search/_search
{
  "query": {
    "term": {
      "studentName": "stu01"
    }
  }
}
```

> term : 정확히 일치하는 용어를 찾는다. (형태소 분석을 하지 않음)

#### sort

```json
POST dept_search/_search
{
  "query": {
    "term": {
      "deptName": "software"
    }
  },
  "sort": {
    "id": {
      "order": "desc"
    }
  }
}
```

#### multi sort

```json
POST dept_search/_search
{
  "query": {
    "term": {
      "deptName": "software"
    }
  },
  "sort": {
    "score": {
      "order": "desc"
    },
    "id": {
      "order": "desc"
    }
  }
}
```

#### range

```json
POST dept_search/_search
{
  "query": {
    "range": {
      "id": {
        "gte": "202300000",
        "lte": "202300005"
      }
    }
  }
}
```

> lt : <  
> gt : >  
> lte : <=  
> gte : >=

#### match_all

```json
POST dept_search/_search
{
  "query": {
    "match_all": {}
  }
}
```

```json
POST dept_search/_search
```

모든 문서 검색

#### match

```json
POST dept_search/_search
{
  "query": {
    "match": {
      "deptName": "English"
    }
  }
}
```

형태소 분석을 통하여 용어를 구분한 후 검색

#### multi match

```json
POST dept_search/_search
{
  "query": {
    "multi_match": {
      "query": "software",
      "fields": ["deptName", "studentName"]
    }
  }
}
```

#### multi index search

```json
POST dept,dept_search/_search
{
  "query": {
    "term": {
      "deptName": "English"
    }
  }
}
```

```json
POST dept,dept_search/_search
{
  "query": {
    "match": {
      "deptName": "English"
    }
  }
}
```

#### bool

```json
POST dept_search/_search
{
  "query":{
    "bool":{
      "must":[
        {
          "match":{
            "studentName":"stu01"
          }
        },
        {
          "term":{
            "deptName":"software"
          }
        }
      ],
      "must_not": [
        {
          "term":{
            "deptName": "korean"
          }
        }
      ]
    }
  }
}
```

> must : 조건이 참일 경우 검색(=)  
> must_not : 조건이 거짓일 경우 검색(!=)  
> should : 하나 이상을 만족할 경우 검색(OR)  
> filter : 조건을 포함할 경우 검색(IN), 점수 계산 하지 않음

#### wildcard

```json
POST dept_search/_search
{
  "query": {
    "wildcard": {
      "studentName": "stu0?"
    }
  }
}
```

```json
POST dept_search/_search
{
  "query": {
    "wildcard": {
      "deptName": "soft*"
    }
  }
}
```

> 형태소 분석을 하지 않음  
> `?` : 한 글자에 대한 와일드카드  
> `*` : 문자 길이와 상관없이 검색

#### prefix

```json
POST dept_search/_search
{
  "query": {
    "prefix": {
      "deptName": "soft"
    }
  }
}
```

접두어 검색

#### exists

```json
POST dept/_search
{
  "query": {
    "exists": {
      "field": "id"
    }
  }
}
```

```json
POST dept/_search
{
  "query": {
    "exists": {
      "field": "deptName"
    }
  }
}
```

필드가 존재하는 문서를 검색한다

#### count

```
POST dept_search/_count?deptName:software
```

#### multi search

```json
GET dept_search/_msearch
{"index": "dept" }
{"query" : {"match_all" : {}}}
{"index": "dept_search"}
{"query" : {"match" : { "deptName": "software"}}}
```

#### highlight

```json
POST dept_search/_search
{
  "query": {
    "match": {
      "deptName": {
        "query": "software"
      }
    }
  },
  "highlight": {
    "fields": {
      "deptName": {}
    }
  }
}
```

