## Cloud Transformation / 10083 전소향
User23 
eu-west-3 (파리)

# Site Reliability Engineering(SRE) PreLab 예제 - 음식배달

- **개요**
  - 본 예제는 GitOps기반 SRE과정을 위한 클라우드 플랫폼 환경구축과 마이크로서비스 전 라이프사이클(분석/설계, 구현, DevOps 툴체인(파이프라인)을 통한 배포, 운영/모니터링)을 커버하도록 구성된 예제입니다. 
  - 분석/설계/구현을 포함한 클라우드 플랫폼기반 운영(컨테이너 오케스트레이션, 모니터링, 로깅, etc) 중심으로 예제 프로젝트는 전개됩니다.
  - PreLab 기간동안 주어진 마이크로서비스 리소스를 사용하거나, 새로운 도메인 모델을 선정 후 설계/구현/배포/운영/모니터링을 적용해도 됩니다.

- **PreLab 진행일정**
  - 분석/설계 : (1일차) 
    - 주제 도메인에 대한 분석/설계
    - 분석/설계 모델에 대한 마이크로서비스 구현
    - MSA 아키텍처 구성 및 Cloud Platform 프로비저닝
    - 산출물 : 기능 목록서, 이벤트스토밍 모델, 헥사고날 아키텍처, MSA 아키텍처 구성도 
  
  - 배포/운영/모니터링 구현: (2~3일차) 
    - DevOps Toolchain 구축
    - 마이크로서비스 오케스트레이션 (오토 스케일아웃, 무정지 배포, EFS 스토리지 구성)
    - 서비스 메쉬 구성 및 마이크로서비스에 SideCar Injection
    - 통합 모니터링, 로깅, 메시징 플랫폼 모니터링 적용
    - 산출물 : 파이프라인 구성도, 수행 증적(Microservice 오케스트레이션, 서비스 메시, 모니터링/로깅 구성도)  
 
  - 산출물 리뷰 : (4일차)
    - 마이크로서비스 클라우드 운영환경에 대해 작성된 문서를 통한 개별 리뷰
    - 마지막날 오전(10시)부터 준비된 참가자가 강사, 또는 보조강사와 1:1로 검토 진행
   
  - Phase별 산출물
    - 단계별 산출물은 해당 README.md 화일을 복사해 사용하거나 프리 포멧으로 제출
    - 제출 시, 반드시 소속과 이름을 기재
 
- **체크포인트** 
  - Domain 주제영역 및 시나리오 이해
  - 이벤트스토밍 모델 이해
  - 폴리글랏 프로그래밍 코드(동기호출, 비동기 호출) 이해 
  - MSA 아키텍처 구성
  - Cloud Platform 프로비저닝 
  - DevOps Toolchain 구축 
  - 분산 메시징 플랫폼 구성 
  - SLA 운영 - 오토 스케일아웃(Auto Scale-out) 
  - SLA 운영 - 무정지 배포(Zero downtime Deploy) 
  - Service Mesh 인프라 구축
  - Service Mesh 기반 마이크로서비스 Resilience 적용
  - 마이크로서비스 통합 모니터링
  - 마이크로서비스 통합 로깅
  - 분산 메시징 플랫폼 모니터링

- **PreLab 참고자료**
  - [핵심요소 기술 교재](https://drive.google.com/drive/folders/1atL7qAz4zPLcMiQvW-3PvrU1qmggdziD) (Ctrl + 클릭)
  - [Mini Shopping mall 이벤트스토밍 모델](https://labs.msaez.io/#/storming/C7pO0ZuWtXXxIKenocD9EMPYrxw2/98f0cfeee84ff3ded1a1b00f9cd38ac3) (Ctrl + 클릭) 
  - Microservice Code 리파지토리
    - [Gateway] : https://github.com/acmexii/gateway
    - [Order] : https://github.com/acmexii/order
    - [Delivery] : https://github.com/acmexii/delivery
    - [Product] : https://github.com/acmexii/product
  - 마이크로서비스 Test Commands (Local)
    - 상품등록 : http POST http://localhost:8083/inventories productId=1001 productName=TV stock=100 
    - 주문생성 : http POST http://localhost:8081/orders productId=1001 productName=TV qty=5 customerId=100
    - 주문취소 : http DELETE http://localhost:8081/orders/1
    - Kafka 모니터링 : /usr/local/kafka/bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic mall --from-beginning
  - 마이크로서비스 Test Commands (Cloud)
    - 상품등록 : http POST http://gateway:8080/inventories productId=1001 productName=TV stock=100 
    - 주문생성 : http POST http://gateway:8080/orders productId=1001 productName=TV qty=5 customerId=100
    - 주문취소 : http DELETE http://gateway:8080/orders/1
    - Kafka 모니터링 : kubectl -n kafka exec -ti my-kafka-0 -- /usr/bin/kafka-console-consumer --bootstrap-server my-kafka:9092 --topic mall --from-beginning

    
# Table of contents

- [예제 - 음식배달](#---)
  - [설계~구현](#---)
    - [이벤트 스토밍](#이벤트-스토밍)
    - [MSA Architecture](#MSA-Architecture)
  - [Cloud Services Provisioning](#---)
    - [EKS 클러스터 구성](#EKS)
  - [배포]
    - [파이프라인 생성](#파이프라인-생성)
    - [분산 메시지 플랫폼 구성](#kafka)
  - [운영]
    - [동기식 호출/서킷 브레이킹/장애격리]
    - [SLA 준수 - 오토스케일 아웃]
    - [SLA 준수 - 무정지 재배포]
    - [Service Mesh 인프라구축]
    - [마이크로서비스 통합 Monitoring]
    - [마이크로서비스 통합 Logging]
    - [이벤트 스트리밍 플랫폼 Monitoring]
  - [신규 개발 조직의 추가]


# **이벤트 스토밍**
![image](https://user-images.githubusercontent.com/14817202/174930344-c21742eb-d812-42c4-8622-971163c3f47c.png)

# **MSA Architecture**

# EKS
1. IAM 보안 자격 증명 설정 
```
aws configure
AWS Access Key ID [None]:
AWS Secret Access Key [None]:
Default region name [None]: 
Default output format [None]: 
```


2. EKS cluster create 
```
eksctl create cluster --name user23-eks --version 1.21 --nodegroup-name standard-workers --node-type t3.medium --nodes 4 --nodes-min 1 --nodes-max 4
```

3. ECR create 
```
aws ecr create-repository \
    --repository-name user23-ecr \
    --image-scanning-configuration scanOnPush=true \
    --region eu-west-3
```

4. docker login to ECR 
```
docker login --username AWS -p $(aws ecr get-login-password --region eu-west-3) 271153858532.dkr.ecr.eu-west-3.amazonaws.com/
```

5. Metric-Server install 
```
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
```

# 파이프라인 생성 
- gateway
- order
- product
- delivery

![image](https://user-images.githubusercontent.com/14817202/174956261-7c0ad40e-0c33-4841-8274-832d81a731ed.png)


# Kafka
```
helm repo add incubator https://charts.helm.sh/incubator
helm repo update
kubectl create namespace kafka
helm install my-kafka --namespace kafka incubator/kafka

kubectl get svc my-kafka -n kafka
```

![image](https://user-images.githubusercontent.com/14817202/174965058-90a52bda-0fd0-4773-bbb9-91263a349cbc.png)

# Test Commands
```
kubectl port-forward deploy/user23-gateway 8080:8080
kubectl port-forward deploy/user23-products 8083:8080
kubectl port-forward deploy/user23-order 8081:8080
kubectl port-forward deploy/user23-delivery 8082:8080

#kafka monitoring
kubectl -n kafka exec -ti my-kafka-0 -- /usr/bin/kafka-console-consumer --bootstrap-server my-kafka:9092 --topic mall --from-beginning
```
- 상품 등록 
```
http POST http://localhost:8083/inventories productId=1001 productName=TV stock=100
```
![image](https://user-images.githubusercontent.com/14817202/174968646-a7767300-7ac7-49d7-8c46-1c288adf35fa.png)

- 주문 생성 
```
http POST http://localhost:8081/orders productId=1001 productName=TV qty=5 customerId=100
```
![image](https://user-images.githubusercontent.com/14817202/174968864-99d15c43-0a6d-4bf8-9476-f804e25d1b10.png)

![image](https://user-images.githubusercontent.com/14817202/174969126-c7c5c3d5-c0d5-4add-959a-af016b50d0e8.png)


- 주문 취소 
```
http DELETE http://localhost:8081/orders/1
```
![image](https://user-images.githubusercontent.com/14817202/174969533-f58c20e1-60e8-42dd-94ff-8a4024198e10.png)

![image](https://user-images.githubusercontent.com/14817202/174969610-ff851577-5ec7-49ee-8516-022e9b163782.png)

