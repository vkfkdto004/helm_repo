# helm_repo

```
# repo download example
helm repo add helm_repo https://vkfkdto004.github.io/helm_repo/
helm repo list
helm repo update
helm search repo mynginx
helm install nginx helm_repo/mynginx
```


<br>
<br>

<img width="1337" height="760" alt="image" src="https://github.com/user-attachments/assets/da1ce63d-72be-43c1-aeea-3ea32db5baca" /> <br>
<img width="1597" height="897" alt="k8s 프로젝트 구성도" src="https://github.com/user-attachments/assets/3171d56c-2323-4b5a-bab5-40864a7eb9b5" />



<Project agenda>
1. k8s 환경에서 CI/CD, observability 환경 구성  <br>
2. 복잡한 yaml을 관리하기 위해서 helm로 패키징하여 배포 <br>
3. kustomize를 이용하여 다양한 환경에서도 yaml을 체계적으로 관리 <br>
<br>
CI - Jenkins <br>
CD - helm + argoCD <br>
observability - EFK Stack (추후 Logstash 추가) 
language - python (+faskapi) <br>

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
v0.5 (2025.12.31) - logstash helm chart uninstall 후 filebeat yaml 배포 완료 <br>

 
### 1차 observability 구성 완료_2025.12.31
 
================================================================================ <br>

v1.1 (2026.01.02) - jenkins helm 배포 및 github, dockerhub 레포 생성. <br>
- ci_build (https://github.com/vkfkdto004/ci_build.git)
- kimwooseop/ci_build (https://hub.docker.com/repository/docker/kimwooseop/ci_build/general)
```
https://github.com/vkfkdto004/ci_build.git 링크는 이미지 빌드를 위한 레포지토리 이다.
https://hub.docker.com/repository/docker/kimwooseop/ci_build/general 링크는 jenkins에서 이미지 빌드 후 푸쉬하기 위한 레포지토리이다.

대략적인 구조
개발 -> ci_build 레포 push -> webhook -> jenkins -> 이미지 빌드 -> dockerhub 레포 push 
```
v1.2 (2026.01.03~04) - jenkins helm 수정 후 재업로드, ci_build에 nginx 이미지 빌드 자동화까지 완료 (자세한 설명은 ci_build 레포지토리에서 설명)<br>
v1.3 (2026.01.04) - 사소한 에러나, 서버 자체에 문제가 많아서 k8s 클러스터 CP1+WORKER2 재구성 후 jenkins 환경까지 재배포 완료


---

v2.1 (2026.01.07)
환경 자체 바꿔서 하다보니 VM 자체도 너무 부하가 많이 걸리고, 패키지 받는데도 오래 걸리다보니 다시 한번 k8s 환경을 어떻게 해야 내가 가지고 있는 리소스 가지고 최적화를 할 수 있을지 고민했다.
집 노트북 환경까지 살짝 수정한 다음 아래 그림과 같이 k8s 인프라를 수정하기로 했다. 

<img width="1270" height="711" alt="image" src="https://github.com/user-attachments/assets/b92e0b81-1e35-40bb-8a9e-c9de62b21e8c" />



1. Control-plane 노드를 2대로 구성하고 HAProxy를 통해 kube-apiserver 요청을 분산시켜 control-plane 레벨(TCP L4 Layer)의 고가용성을 확보하였다. (노트북 리소스 상 worker node 없이, taint 제거 실시)
2.  CNI는 eBPF 기반의 cilium을 채택, Envoy 기반 Gateway API를 통해 ingress 및 L7 로드밸런싱을 처리하도록 구성하였다.
3. 외부 etcd 노드를 포함한 3-member etcd cluster를 구성하여 quorum을 확보하고, etcd 장애 시에도 etcd 가용성을 유지하도록 설계하였다.
4. NFS 서버와 nfs-provisioner를 구성하여, 동적 PV 프로비저닝 환경을 구성하였고, 다양한 서비스에 대한 상태 저장이 필요한 워크로드에 대한 테스트와 중앙 집중화된 데이터 저장 환경을 구성하였다. (nfs-provisioner는 StorageClass를 제공하고, PVC 요청 시 PV를 동적으로 생성한다.)





