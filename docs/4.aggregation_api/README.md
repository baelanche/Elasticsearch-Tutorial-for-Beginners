## [Reference](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-aggregations.html)

<br/>

### 1 - 종류

1. 매트릭 집계
2. 버킷 집계
3. 파이프라인 집계

<br/>

형식:

```
GET <_index>/_search
{
  "query": {

  },
  "aggs": {

  }
}
```

query 를 통해 문서를 필터링하고, aggs 키워드를 통해 쿼리 결과를 집계한다.

<br/>

### 1.2 - 매트릭 집계

쿼리 결과에 대해서 합, 곱, 평균 등의 산술 연산을 수행한다.

#### sum

```
GET <index>/_search?size=0
{
  "aggs": {
    "<aggs_name>": {
      "sum": {
        "field": "<field>"
      }
    }
  }
}
```

<br/>

### 1.3 - 버킷 집계

쿼리 결과 집합을 특정 기준으로 나누고 연산을 수행한다.
