## [Reference](https://www.elastic.co/guide/en/elasticsearch/reference/current/search.html)

<br/>

### 1 - URI Search

```json
POST dept_search/_search?q=studentName:stu01
```

> 자주 쓰는 명령어  
> `q` : 검색을 수행할 쿼리  
> `df` : 쿼리에 필드를 지정하지 않았을 경우 기본값  
> `sort` : 검색 결과의 정렬 필드  
> `from` : 검색을 시작할 문서의 위치  
> `size` : 검색 결과 개수

<br/>

```json
POST dept_search/_serarch?q=studentName:* AND id:202300001&from=0&size=10
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
