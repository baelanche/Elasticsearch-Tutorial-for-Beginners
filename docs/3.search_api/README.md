## [Reference](https://www.elastic.co/guide/en/elasticsearch/reference/current/search.html)

<br/>

### 1 - URI Search

```json
POST dept_search/_search?q=studentName:stu01
```

<br/>

`q` : 검색을 수행할 쿼리  
`df` : 쿼리에 필드를 지정하지 않았을 경우 기본값  
`sort` : 검색 결과의 정렬 필드  
`from` : 검색을 시작할 문서의 위치  
`size` : 검색 결과 개수
