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
    - [SLA 준수 - 오토스케일 아웃](#Auto-ScaleOut)
    - [SLA 준수 - 무정지 재배포](#무정지-재배포)
    - [Service Mesh 인프라구축](#Service-Mesh)
    - [마이크로서비스 통합 Monitoring]
    - [마이크로서비스 통합 Logging]
    - [이벤트 스트리밍 플랫폼 Monitoring]
  


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

## gateway change to LB type
gateway buildspec-kubectl.yml에 service type:LoadBalancer 를 추가해준다. 
```
  apiVersion: v1
  kind: Service
  metadata:
    name: $_PROJECT_NAME
    labels:
      app: $_PROJECT_NAME
  spec:
    ports:
      - port: 8080
        targetPort: 8080
    selector:
      app: $_PROJECT_NAME
    type: LoadBalancer  
```

gateway application.yml에 svc 이름 변경 
```
spring:
  profiles: docker
  cloud:
    gateway:
      routes:
        - id: order
          uri: http://user23-order:8080
          predicates:
            - Path=/orders/** 
        - id: delivery
          uri: http://user23-delivery:8080
          predicates:
            - Path=/deliveries/** 
        - id: product
          uri: http://user23-products:8080
          predicates:
            - Path=/inventories/** 
        - id: frontend
          uri: http://frontend:8080
          predicates:
            - Path=/**
```

![image](https://user-images.githubusercontent.com/14817202/175184331-8e3f4f15-c7e4-4428-9a59-273c812dd135.png)


# Test Commands
```
#kafka monitoring
kubectl -n kafka exec -ti my-kafka-0 -- /usr/bin/kafka-console-consumer --bootstrap-server my-kafka:9092 --topic mall --from-beginning
```
- 상품 등록 
```
http POST http://a80fef8e98315402cba6962c3c1cb624-1068765691.eu-west-3.elb.amazonaws.com:8080/inventories productId=1001 productName=TV stock=100
```
![image](https://user-images.githubusercontent.com/14817202/175190625-0399f616-c2a6-4cab-b3b4-50e3dd1054a2.png)

- 주문 생성 
```
http POST http://a80fef8e98315402cba6962c3c1cb624-1068765691.eu-west-3.elb.amazonaws.com:8080/orders productId=1001 productName=TV qty=5 customerId=100

```
![image](https://user-images.githubusercontent.com/14817202/175190737-930515fd-b722-4b2d-94f0-808d061478d2.png)

![image](https://user-images.githubusercontent.com/14817202/175190895-fe5a130f-4d81-49c4-b596-749c062e19ea.png)


- 주문 취소 
```
http DELETE http://a80fef8e98315402cba6962c3c1cb624-1068765691.eu-west-3.elb.amazonaws.com:8080/orders/4
```
![image](https://user-images.githubusercontent.com/14817202/175190968-5cd28c50-b04d-4d46-aafc-6ae21ccdc33b.png)

![image](https://user-images.githubusercontent.com/14817202/175191003-0916af01-a5b0-49e0-8f23-1e34946258c8.png)


# Auto ScaleOut

1. order 서비스의 buildspec-kubectl.yml 파일 수정 
```
  readinessProbe:
    httpGet:
      path: /actuator/health
      port: 8080
    initialDelaySeconds: 10
    timeoutSeconds: 2
    periodSeconds: 5
    failureThreshold: 10
  livenessProbe:
    httpGet:
      path: /actuator/health
      port: 8080
    initialDelaySeconds: 120
    timeoutSeconds: 2
    periodSeconds: 5
    failureThreshold: 5
  resources:
    limits:
      cpu: 500m
    requests:
      cpu: 200m
```

2. Load Generator(siege) 생성 
```
kubectl apply -f - <<EOF
apiVersion: v1
kind: Pod
metadata:
  name: siege
spec:
  containers:
  - name: siege
    image: apexacme/siege-nginx
EOF
```

3. siege 작동 확인
```
kubectl exec -it siege -- /bin/bash
siege -c1 -t2S -v http://user23-order:8080/orders
exit
```
![image](https://user-images.githubusercontent.com/14817202/175209455-ea3ebfdc-b312-4520-b0b9-5ec86f7d6d1c.png)

4. HPA 설정 
```
kubectl autoscale deploy user23-order --min=1 --max=10 --cpu-percent=15
```

5. order서비스에 워크로드 30초 발생 
- 동시 접속자 수 30
- 반복횟수 10 
- 30초동안 
```
kubectl exec -it siege -- /bin/bash
siege -c30 -t30S -r10 --content-type "application/json" 'http://user23-order:8080/orders POST {"productId": "1001", "productName": "TV", "qty": "5", "customerId": "100"}'
```
![image](https://user-images.githubusercontent.com/14817202/175210231-c9ee2aff-c4b4-4880-9b40-f3ad99c5d436.png)

6. scale out 확인
![image](https://user-images.githubusercontent.com/14817202/175210403-520144c8-1537-4c31-aea1-f811c126bac0.png)


# 무정지 재배포
1. 워크로드 모니터링 
```
siege -c100 -t120S -r10 --content-type "application/json" 'http://a80fef8e98315402cba6962c3c1cb624-1068765691.eu-west-3.elb.amazonaws.com:8080/orders POST {"item": "chicken"}'
```
![image](https://user-images.githubusercontent.com/14817202/175213588-3ef5970b-29c5-4992-8b42-e9cc2ebdd1f9.png)

2. siege 부하가 걸린 상태에서 order서비스를 재배포, Readiness Probe설정으로 인해 Availability 보장 
```
siege -c1 -t300S -v --content-type "application/json" 'http://a80fef8e98315402cba6962c3c1cb624-1068765691.eu-west-3.elb.amazonaws.com:8080/orders POST {"item": "chicken"}'
```
![image](https://user-images.githubusercontent.com/14817202/175222985-079af3d1-c4ef-4c16-9d9d-aa8f35248cd3.png)

# Service Mesh 



