### VPC

- 사용자가 정의한 가상의 네트워크 환경 (논리적 격리)
- VPC 내의 모든 인스턴스(EC2) 들은 사설 IP가 부여됨
- 개별 인스턴스에 공인 IP 할당 가능 (public IP/Elastic IP)
- 네트워크 요청 제어가능 (보안그룹, Network ACL)

**VCP 내 Subnet 구성**

- VPC 내의 IP 주소 공간을 논리적으로 분할
- 왜?
    - Subnet간 트래픽 격리 → 서브넷 단위로 네트워크 정책, 보안그룹 및 라우팅을 구성할 수 있다.
    - 보안 → 서브넷 간에 보안을 격리시키고, 필요에 따라 트래픽을 허용하거나 차단
    - 가용영역 활용 → 서비스 장애 발생에 대응

**Subnet을 어떻게 분할할까?**

CIDR 표기 방법을 사용해 네트워크 주소 지정

- 형식 : [IP주소]/[비트 수] 단위
    - 10.0.0.0/24 → 호스트 주소 10.0.0.0 ~ 10.0.0.255


Route Table

- Subnet 레벨에서 트래픽이 어떻게 전달되는지를 결정하는데 사용
- VPC는 하나 이상의 Route Table을 가짐

**방화벽**

- NACL
    - stateless
    - 서브넷 단위 보안규칙 적용
- Security Group (방화벽)
    - stateful
    - 서버단위 보안규칙 적용

AWS 보안 그룹은 기본이 DENY ALL

외부에서 접속은 어떻게 할까?

**보안그룹**

- 연결된 리소스에 도달하고 나갈 수 있는 트래픽을 제거
- 보안 그룹은 Stateful 방화벽
    - 들어오는 트래픽 규칙이 허용되었다면, 나가는 트래픽에 대해서도 허용


**Stateful / Stateless 방화벽**

- 방화벽이 동작하는 방식을 의미
- Inbound Traffic에 대한 응답을 어떻게 처리할 것인지 나타냄

**Stateless**

- 허용된 Inbound Traffic의 응답(Response)에 대한 Port까지도 명시적으로 허용해야만 통신이 가능한 방식
- NACL

**Stateful**

- Inbound Traffic이 허용된 경우, 이에 상응하는 응답(Response)에 해당하는 Port는 자동으로 함께 허용(Allow)하는 방식