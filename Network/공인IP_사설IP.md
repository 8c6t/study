공인 IP와 사설 IP
========

## 1. 공인 IP

- 인터넷상에서 중복되지 않는 IP 주소
- 인터넷 통신을 위해서는 공인 IP가 필수

### 공인 IP 할당/관리

1. ICANN의 하부 조직인 IR(Internet Registry)에서 실질적으로 공인 IP를 할당/관리
2. 전 세계 지역을 5개의 RIR(Regional Internet Registry)로 나누어 담당
3. RIR이 국가별 NIR(National Internet Registry)로 나뉘어 담당
4. NIR이 국가 안에서 LIR(Local Internet Registry)로 나뉨. 일반적으로 ISP가 LIR에 해당
5. 각 LIR(ISP)이 엔드 유저에게 공인 IP 주소를 할당
  

## 2. 사설 IP

- 공인 IP 고갈에 따른 대첵으로 생겨난 IP
- 외부와 연결되지 않는 네트워크에서 이용
- 공인 IP로서 이용되지 않는 범위의 IP 주소를 설정해서 사용

### 사설 IP 주소의 범위

- 10.0.0.0 ~ 10.255.255.255 (10.0.0.0/8)
- 172.16.0.0 ~ 172.31.255.255 (172.16.0.0/12)
- 192.168.0.0 ~ 192.168.255.255 (192.168.0.0/16)

## 3. NAT

- Network Address Translation
- 사설 IP와 공인 IP 주소를 상호 변환하는 기술
- 사설 IP와 공인 IP 주소의 경계에 위치하는 라우터나 방화벽 상에서 이루어짐


### NAT 동작 방식

1. 클라이언트에서 서버로 IP 패킷을 전송
2. 라우터가 패킷 발신자 IP 주소를 클라이언트의 사설 IP 주소에서 공인 IP 주소로 변환해서 전송
3. 라우터가 변환 정보를 NAT 테이블에 기록
4. 서버에서 공인 IP 주소로 응답 패킷을 보냄
5. 라우터가 NAT 테이블을 참조하여 응답 패킷의 목적지 IP 주소를 원래의 사설 IP 주소로 변환
6. 클라이언트에서 패킷을 돌려받음

