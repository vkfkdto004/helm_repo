# helm_repo

```
# repo download example
helm repo add helm_repo https://github.com/vkfkdto004/helm_repo
helm repo list
helm repo update
helm search repo mynginx
helm install nginx helm_repo/mynginx
```

다운 가능한 chart list
- mynginx
- myelasticsearch-8.5.1
- mylogstash-8.5.1


<br>
<br>

<img width="1337" height="760" alt="image" src="https://github.com/user-attachments/assets/da1ce63d-72be-43c1-aeea-3ea32db5baca" /> <br>
<img width="1597" height="897" alt="k8s 프로젝트 구성도" src="https://github.com/user-attachments/assets/3171d56c-2323-4b5a-bab5-40864a7eb9b5" />



<agenda>
k8s 환경에서 CI/CD, Monitoring 환경 구성 예정 <br>
복잡한 yaml을 관리하기 위해서 helm로 패키징하여 자동화 <br>
kustomize를 이용하여 다양한 환경에서도 yaml을 체계적으로 관리 <br>
CI - Jenkins 예정 <br>
CD - helm + argoCD <br>
Monitoring - Prometheus + Grafana <br>
EelasticSearch <br>
language - python (faskapi) <br>

<br>
<br>


v0.1 (2025.12.21) - 프로젝트 구성도 작성, github helm repo 구성 및 mynginx 테스트 chart 업로드 <br>
v0.2 (2025.12.28) - elasticsearch chart values.yaml 수정 후 업로드 <br>
v0.3 (2025.12.29) - elasticsearch chart values.yaml 싱글노드 재구성 후 정상 동작 확인 + logstash 와 연동 정상 동작 확인<br>
v0.4 (2025.12.30) - kibana yaml 설치 후 k8s 클러스터 대시보드 구성을 위해서 metricbeat + kube-state-metric yaml 배포 완료. <br>
```
현재 초기 테스트 배포 버전이라 보완해야할 점이 많음.
ELK 스택의 구조 -- FileBeat(로그수집) -> Logstash(필터링) -> Elasticsearch(데이터 저장) -> Kibana(대시보드)
로그 수집이 우선되야 하므로, logstash는 우선 접어두고, filebeat 구성하여 로그수집 진행
```
v0.5 (2025.12.31) - logstash helm chart uninstall 후 filebeat yaml 배포 진행 (https://github.com/elastic/beats/tree/main/deploy/kubernetes/filebeat) 참고





