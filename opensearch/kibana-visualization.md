# Kibana로 데이터 Visualization 하기

Kibana는 ElasticSearch를 백엔드로 사용해 데이터 visualization과 analysis를 할 수 있게 도와주는 프론트엔드 web application입니다. 이번 워크샵에서는 Kibana의 대표적 visualization 도구들을 사용하지만 Kibana는 아주 다양한 분석툴들을 제공하고 있습니다.

## 데이터 Visualization

### JSON Generator로 데이터 보내기

1\. Random Log Generator로 초당 **10 record**를 **5분**동안 보냅니다.

<img src="../.gitbook/assets/file.drawing (82).svg" alt="" class="gitbook-drawing">

### Index Patterns

2\. OpenSearch의 Stack Management -> Index pattern -> Create new pattern에 접속해 새로운 Index Pattern을 생성합니다.

<img src="../.gitbook/assets/file.drawing (29).svg" alt="" class="gitbook-drawing">

3\. 이전에 정했던 _eksapp-log-\*_ 를 입력합고 **Next Step**을 클릭합니다.

<img src="../.gitbook/assets/file.drawing (6).svg" alt="" class="gitbook-drawing">

4\. Time field에 _timestamp_를 입력하고 **Create index pattern**을 클릭해 index pattern을 생성합니다.

<img src="../.gitbook/assets/file.drawing (14).svg" alt="" class="gitbook-drawing">

### Discover

5\. OpenSearch Dashboard에서 **Discover** 창으로 이동합니다. 발생한 로그가 정상적으로 전송 되는지 확인할 수 있습니다.

<img src="../.gitbook/assets/file.drawing (99).svg" alt="" class="gitbook-drawing">

### Visualize (Pie)

이번엔 샘플 로그 `status` 의 결과(_OK, WARN, FAIL_)를 분석합니다.

6\. OpenSearch Dashboard에서 **Discover** 창으로 이동해서 **Create new visualization**을 클릭합니다.

<img src="../.gitbook/assets/file.drawing (40).svg" alt="" class="gitbook-drawing">

7\. **Pie**를 선택합니다.

<img src="../.gitbook/assets/file.drawing (41).svg" alt="" class="gitbook-drawing">

8\. 이전에 정한 index를 클릭합니다.

<img src="../.gitbook/assets/file.drawing (106).svg" alt="" class="gitbook-drawing">

9\. 다음과 같이 요구사항을 선택하고 **Update**를 클릭합니다.

* Aggregation: **Count**
* Buckets: **Split slices**
* Aggregation: **Terms**
* Field: **status.keyword**

<img src="../.gitbook/assets/file.drawing (94).svg" alt="" class="gitbook-drawing">

10\. 보시다시 분석된 데이터를 이해하기 쉽게 시각화 할 수 있습니다.

<img src="../.gitbook/assets/file.drawing (92).svg" alt="" class="gitbook-drawing">

### Visualize (Area)

11\. 이번엔 Area를 선택합니다.

<img src="../.gitbook/assets/file.drawing (72).svg" alt="" class="gitbook-drawing">

12\. 다음과 같이 Bucket 내용을 설정합니다.

* Aggregation: **Date Histogram**
* Field: **timestamp**
* **Split Series**
* Sub aggregation: **Terms**
* Field: **status.keyword**
* Order by: Metric: **Count**

<img src="../.gitbook/assets/file.drawing (30).svg" alt="" class="gitbook-drawing">

13\. 마지막으로 쿼리를 `status:OK|WARN|FAIL` 로 정하면 다음과 같이 status를 결과별로 확인할 수 있습니다.

<img src="../.gitbook/assets/file.drawing (79).svg" alt="" class="gitbook-drawing">

