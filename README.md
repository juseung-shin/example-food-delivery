# 프로젝트명 : 도서대여시스템
# 시나리오
1. 사용자가 원하는 도서를 선택하여 대여 요청을 한다
2. 대여 요청 시 책 상태 "대여 가능" 이면 대여 상태가 "승인" 이다. 
3. 책 상태가 "대여 중" 이면 대여 상태가 "거절" 상태가 된다. 
4. 책 상태가 "대여 불가능" 이면 대여 상태가 "거절" 상태가 된다.  
5. 사용자는 대여한 도서를 반납할 수 있다.
6. 도서가 반납 되면 책 상태 "대여 가능" 대여 상태 "반납"이 된다.
7. 사용자는 시스템에서 자신의 대여 기록 및 상태를 조회할 수 있습니다.

# 이벤트 스토밍
![이벤트 스토밍](https://github.com/user-attachments/assets/e4077709-7df7-4e3d-a1a4-3daad1d1276d)
- Bounded Context : 4개
  - User : 사용자
  - Borrowing : 대여
  - Book : 책
  - Mypage : 마이페이지
# 아키텍처 구성도
![구성도01](https://github.com/user-attachments/assets/1506eeb1-d240-403f-ae1d-58a23e055c63)
- 게이트웨이 : Ingress
- 서비스매쉬 : Istio
- 이벤트 스토어 : Kafka

# 구현
- Saga
  - 각 서비스는 로컬 트랜잭션을 가지고 있으며, 해당 서비스 데이터를 업데이트하며 메시지 또는 이벤트를 발행해서, 다음 단계 트랜잭션을 호출하게 됩니다.
- 사용자가 원하는 도서를 선택하여 대여 요청을 한다.
![대여요청](https://github.com/user-attachments/assets/084ebe54-188a-4c6a-9067-ba3c77c22f07)
- Book 서비스 에서 Sub 한 로그 내용
![Book 서비스 로그](https://github.com/user-attachments/assets/27f827f9-de00-4948-b4db-21dc101b728d)
- 대여 요청 시 책 상태 "대여 가능" 상태여서 "승인" 상태로 Borrowing에 Pub 한다.
![Borring 서비스 로그](https://github.com/user-attachments/assets/d2199c65-59e1-4cd8-bc42-22a1dbe19992)
![대여승인](https://github.com/user-attachments/assets/6c3a14fe-8fc2-4f19-9b24-51f09f811d94)

- Compensation
 - 트랜잭션의 롤백 준 한다.
 - 각 로컬 트랜잭션과 보상 트랜잭션을 이용해 비지니스 데이터 정합성 일치 시킨다.
- 사용자가 책 선택 당시에는 대여 가능 한 책이여서 대여요청
![대여요청](https://github.com/user-attachments/assets/598ccc4e-5e7b-4bea-8dae-7d485ccebcea)
- 책 상태가 대여 가능 하지 못해 거절 Pub 한다.
![대여거절](https://github.com/user-attachments/assets/a2e5c8c9-0395-42a8-91fc-b8a9effac80a) 

- GateWay
- 단일 진입점을 만들어 다른 유형의 클라이언트에게 서로 다른 API조합을 제공할 수도 있고 각 서비스 접근 시 필요한 인증/인가 기능을 한 번에 처리 가능
 ![ingress_gateway](https://github.com/user-attachments/assets/3dcf0451-5132-4a05-bcd6-e67d9461b18e)
 ![ingress_게이트웨이_00](https://github.com/user-attachments/assets/5d3d9d11-e945-44f7-9dcc-b87a13f895be)
 ![ingress_게이트웨이_01](https://github.com/user-attachments/assets/b455904a-a781-4709-a80e-480c8f0feed8)

- CQRS
  - Command and Query Responsibility Segregation   
  - 하나의 저장소에 쓰기 모델과 읽기 모델을 분리하는 방식으로 변화시켜 쓰기 서비스와 조회서비스를 분리해 저장소 처리 성능을 향상  
- 사용자는 시스템에서 자신의 대여 기록 및 상태를 조회할 수 있습니다.
![CQRS_00](https://github.com/user-attachments/assets/27611f70-5dcd-4f1a-8daa-d012b5b3e4e8)
![CQRS_01](https://github.com/user-attachments/assets/016cb86a-75db-4676-82ca-0a8090f4839f)

# 클라우드 배포 
- Azure DevOps를 사용하여 CI/CD를 구성 하였다.
![CI_CD_00](https://github.com/user-attachments/assets/26071158-563f-4012-be2b-f567bc8e4397)
![CI_CD_01](https://github.com/user-attachments/assets/5e999470-f47f-4be5-898b-689de87e8b65)
![CI_CD_02](https://github.com/user-attachments/assets/cc61a5fd-3d3d-4c49-8346-f8d939a25aa2)
![CI_CD_03](https://github.com/user-attachments/assets/65b23715-1e9f-4d69-9c40-27d09c75cb8b)

# 컨테이너 인프라
- HPA
  - Horizontal Pod Autoscaling  
  - HPA는 CPU 사용률 기반으로 디플로이먼트로 실행된 파드의 갯수를 늘리거나 줄여준다.
User CPU 사용률은 50% 이다. 
![hpa_00](https://github.com/user-attachments/assets/fc606570-3de9-414c-8c7f-e052d2fd8cc8)
![hpa_01](https://github.com/user-attachments/assets/cb66ada0-a9ba-446e-a1b5-33b0b8d92eca)
![hpa_02](https://github.com/user-attachments/assets/3c67ae39-93ff-4415-9bd2-5b6e309bd93d)
![hpa_03](https://github.com/user-attachments/assets/fda1c686-a179-4d58-832e-213bb4a80ffb)

- ConfigpMap/Secret
  - ConfigpMap은 컨테이너 이미지로부터 설정 정보를 분리해 주는 리소스 타입
  - Secret은 보안이 중 요한 패스워드나 API 키, 인증서 파일들은 Secret에 저장
![configmap_yml](https://github.com/user-attachments/assets/d31d5dd6-e57d-4995-ad0d-d2f2e7bc0c3d)
![secret](https://github.com/user-attachments/assets/ce60fa6f-2981-4370-9cbe-d9b6564c518f)
![config_secret_01](https://github.com/user-attachments/assets/3d36d2af-e1bb-4f03-aa86-d0b7608ba7b4)
![config_sercret_00](https://github.com/user-attachments/assets/21c69d36-46a0-49d8-a819-40b53dc1cd9e)
![config_sercret_02](https://github.com/user-attachments/assets/c647bd5e-56fd-43fe-b5dd-39f863c08c6c)

- PVC
- Kubernetes에서 영구 스토리지 자원을 요청하는 방식
![pvc_01](https://github.com/user-attachments/assets/cb7a1ea5-1a20-4338-b44d-c8192fb62a83)
![pvc_02](https://github.com/user-attachments/assets/15896bb7-823f-45bd-bb85-d811f284c28f)
![pvc_03](https://github.com/user-attachments/assets/efc4b76c-fa14-4a4c-a219-ab627500632d)

- Liveness
- 문제가 있는 컨테이너를 자동으로 재시작하거나 종료
- 임의로 해당 pod를 프로세스를 죽임
![livenessProbe_00](https://github.com/user-attachments/assets/2cfadee1-82cf-433e-890d-1de7b2f36b9e)

- initialDelaySeconds : 컨테이너가 시작된 후, Probe가작동하기까지딜레이시간
- periodSeconds : Probe를 수행하는주기(빈도)
- timeoutSeconds : Probe가 작동하고최대기다리는시간(타임아웃)
- failureThreshold : Probe가 최종 실패로 간주하는회수

![livenessProbe_01](https://github.com/user-attachments/assets/1e407dcb-e1b6-416b-956d-9d7200085da1)
![livenessProbe_02](https://github.com/user-attachments/assets/230ec3c9-36f9-4dfd-9c22-a487bcee9a23)

- ReadinessProbe
- 설정 없이 요청 상태에서 재배포
![readinessProbe_01](https://github.com/user-attachments/assets/7e6f1ada-e4df-42c3-b01c-9e50802c528e)
- ReadinessProbe 설정
![readinessProbe_02](https://github.com/user-attachments/assets/451a9e35-c924-49f7-9bfb-80d0cf6f1cd3)
- 설정 요청 상태에서 재배포
![readinessProbe_03](https://github.com/user-attachments/assets/fd9eff8a-cbaf-46b8-8c70-b9b0da487c44)

- 서비스 매쉬
- 이스티오를 먼저 설치 한다.
![istio_00](https://github.com/user-attachments/assets/86863cf8-0001-42ce-b162-57938fea8a61)
- user에 사이드카를 설정한다.
![istio_01](https://github.com/user-attachments/assets/aa552f86-0653-4cde-b04f-b8b665bed14b)
- 설정 이후 결과물
![istio_02](https://github.com/user-attachments/assets/acb8dead-70f4-4096-b99f-9697c5105940)

- 통합 모니터링
- loki로 현재 로그를 확인 할수 있다. 
![log](https://github.com/user-attachments/assets/384f45b7-3d42-43bc-8a80-c9486132fc79)
- grafana를 통해 현재 요청에 대한 분석 값을 확인 할 수 있다.
![ob](https://github.com/user-attachments/assets/b62e620f-3eb1-4094-93f5-a28996fc48ec)
