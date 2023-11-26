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
> `df` : 쿼리에 필드를 지정하지 않았을 경우 기본값  
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

#### 정렬

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

#### 여러 번의 정렬

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
    }
  }
}
```

#### 범위 검색

```
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
