# elastci_study
구성해놓은 kibana를 이용하여 elastic을 활용할 수 있는 방법을 터득하기 위해서 Dev Tools에서 작업한 내역을 남김.

엘라스틱에서의 단일 데이터 단위 -> 도큐먼트(document)
도큐먼트를 모아놓은 집합 -> 인덱스(Index)
데이터를 저장하는 단위인 인덱스 -> 인디시즈(Indices)
엘라스틱에 데이터를 저장하는 행위 -> 색인
```
인덱스
|
|-> 샤드 (Shard) 라는 단위로 분리 / 클러스터 구성 시 각 노드에 분산되어 저장. // 싱글노드 구성 시 프라이머리 샤드만 존재하고, 복제본은 생성하지 않음
|--> 엘라스틱 버전 별 디폴트 샤드 ( v7.0부터 디폴트 1개 // v6.x 이하 버전 5개로 구성)
|---> 처음 생성된 샤드 -> 프라이머리 샤드 (Primary Shard)
|---> 복제본 -> 리플리카(Replica)

# 삭제
DELETE books

# 조회
GET books

# books 인덱스 셋팅 수정 (Shards 값은 변경이 안됨)
PUT books/_settings
{
    "number_of_replicas": 1
}

# books 인덱스 생성
PUT /books
{
    "settings": {
        "number_of_shards": 5,
        "number_of_replicas": 1
    }
}

# 노드 정보 확인
GET _cat/nodes?v&h=ip,name,node.role

# 인덱스 정보 확인
GET _cat/indices?v&h=health,status,index,docs.count,pri,rep

yellow open   books                                                                       0   5   1

# 샤드 정보 확인
GET _cat/shards/books?v

index shard prirep state      docs store dataset ip          node
books 0     p      STARTED       0  249b    249b 10.24.1.236 elasticsearch-es-default-0
books 0     r      UNASSIGNED                                
books 1     p      STARTED       0  249b    249b 10.24.1.236 elasticsearch-es-default-0
books 1     r      UNASSIGNED                                
books 2     p      STARTED       0  249b    249b 10.24.1.236 elasticsearch-es-default-0
books 2     r      UNASSIGNED                                
books 3     p      STARTED       0  249b    249b 10.24.1.236 elasticsearch-es-default-0
books 3     r      UNASSIGNED                                
books 4     p      STARTED       0  249b    249b 10.24.1.236 elasticsearch-es-default-0
books 4     r      UNASSIGNED           

prirep r UNASSIGNED 이 뜨는 이유? -> 싱글노드여서 레플리카를 설정해도 복제본이 생기지 않기 때문.
인덱스 정보 확인 시 green이 아닌 yellow가 뜨는게 위와 같은 이유이다.

elastic 버전 업그레이드에 따라 filebeat.input이 살짝 달라졌다. 기존엔 type: container가 가능했지만 이제는 error log를 뱉어냈음. log에서 알려주는 elastic 공홈을 보고 type: filestream으로 변경 -> 해결 완료.
elastic 기능을 쓰려고 elastic에 대해서 스터디 진행 중.

# 클러스터 셋팅
GET _cluster/settings

# 모니터링 on -> off ( null 값을 넣으면 default 셋팅이 됨)
PUT _cluster/settings
{
  "persistent": {
    "xpack": {
      "monitoring": {
        "collection": {
          "enabled": false
        }
      }
    }
  }
}
#! [xpack.monitoring.collection.enabled] setting was deprecated in Elasticsearch and will be removed in a future release. See the deprecation documentation for the next major version.
위 설정은 추후 major 버전에서는 삭제될 예정.



---
# 도큐먼트 CURDS : 입력, 조회, 수정, 삭제, 검색

## 입력
PUT my_index/_doc/1
{
  "name": "Jeff Kim",
  "message" : "Hello Elasticsearch"
}

### 이전과 다른 내용을 PUT 으로 삽입하면 이전 정보가 없어지고, 새로운 PUT 정보만 오버라이트됨.
PUT my_index/_doc/1
{
  "age": 40
}

## 조회
GET my_index/_doc/1
GET my_index/_source/1

## 삭제
DELETE my_index/_doc/1
DELETE my_index

## 수정
### POST는 document id가 자동으로 생성되어 겹치는 문제를 해결할 수 있음.
POST my_index/_doc
{
  "name" : "Jeff Kim 2",
  "message" : "Hello Kibana"
}
### POST 내용 조회
GET my_index/_doc/xLSYCJwB9ZZKxExiCnhV


## Update
POST my_index/_update/1
{
  "doc":{
    "age": 28
  }
}

POST my_index/_update/xLSYCJwB9ZZKxExiCnhV
{
  "doc":{
    "age": 31
  }
}

---
# Bulk API
여러 명령을 배치로 수행하기 위해서 사용하며 index, create ,update, delete 동작 가능하고 delete를 제외하고는 명령문과 데이터문을 한 줄씩 순서대로 입력해야함
줄바꿈 허용하지 않음.


---
# 검색
# 검색
GET my_index/_search?q=age:28 AND name:Jeff
GET my_index/_search?q=message:kibana AND name:Jeff

GET my_index/_search
{
  "query": {
    "match": {
      "message": "kibana"
    }
  }
}


```
