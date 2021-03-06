IP 주소와 주소 체계
========

## 1. IP 주소

- TCP/IP로 통신하는 기기를 식별하기 위한 주소
- 일반적으로 사용되는 IPv4는 32비트로 구성
  - 0과 1로 이루어진 비트가 32개 나열됨
  - 사람이 알아보기 쉽도록 부점 10진 표기법을 이용

> 부점 10진 표기법: 32비트를 8비트씩 10진수로 변환하여 표기

## 2. IP 주소 분류

| 분류 | 설명 |
|-|-|
| 유니캐스트 주소 | one-to-one 통신. 호스트 한 곳이 목적지<br/>컴퓨터나 라우터 등 호스트의 인터페이스에 설정하는 IP 주소 |
| 브로드캐스트 주소 | one-to-all 통신. 같은 네트워크 상의 모든 호스트가 목적지 |
| 멀티캐스트 주소 | one-to-many 통신. 그룹화된 호스트가 목적지 |


## 3. IP 주소 구성

`IP 주소 = 네트워크 ID + 호스트 ID`

- 네트워크 ID로 네트워크를 식별
- 호스트 ID로 해당 네트워크 내부의 호스트(인터페이스)를 식별
- 32비트 중 네트워크 ID와 호스트 ID를 구분하는 지점은 고정되어 있지 않음
  - 클래스풀 / 클래스리스 주소 체계로 구분

## 4. IP 주소 체계

### 가. 클래스풀 주소 체계

| 클래스 | 맨 앞 비트 패턴 | 맨 앞 옥텟의 10진수 표기 | ID 구분 지점 | 네트워크 ID 개수 | 호스트 ID 개수 |
|-|-|-|-|-|-|
| A클래스 | `0` | 1 ~ 126 | 8비트 | 126개 | 약 1600만개 |
| B클래스 | `10` | 128 ~ 191 | 16비트 | 16382개 | 65534개 |
| C클래스 | `110` | 192 ~ 223 | 24비트 | 209만 7150개 | 254개 |

- IP주소를 클래스로 나눔으로서 네트워크 ID와 호스트 ID를 구분하는 방식
- A~E 클래스로 나뉘며, 이 중 호스트 인터페이스에 설정할 수 있는 유니캐스트 IP 주소는 A~C 3종류
  - D 클래스: 멀티캐스트 주소를 정의하는 클래스
  - E 클래스: 실험 용도로 사용되는 클래스
- **클래스 자체를 식별하기 위해 맨 앞부분의 비트 패턴이 규정되어 있음**
- 이용 효율이 나빠 오늘날에는 클래스리스 주소 체계가 주로 사용됨


> 네트워크 ID와 호스트 ID 개수 계산 시 모든 비트가 0이거나 1인 주소는 특수 목적으로 예약되어 있으므로 -2를 하여 제외시킴
> - 호스트 ID의 비트가 모두 0: 네트워크 주소
> - 호스트 ID의 비트가 모두 1: 브로드캐스트 주소

### 나. 클래스리스 주소 체계(CIDR)

`255.255.255.0` = `11111111 11111111 11111111 00000000`

- **서브넷 마스크**를 이용하여 네트워크 ID와 호스트 ID를 구분
  - 비트가 1인 부분이 네트워크 ID
  - 비트가 0인 부분이 호스트 ID
  - 예시의 `255.255.255.0`은 24비트 째에서 네트워크 ID와 호스트 ID가 구분됨
- 서브넷 마스크는 8비트씩 10진수로 변환하여 표기
  - 네트워크 ID를 나타내는 1이 연속해서 나온 다음에 호스트 ID를 나타내는 0이 연속해서 나와야 한다는 규칙이 존재
- 비트 1이 몇 개 존재하는지 나타내는 프리픽스 표기법도 자주 이용됨
  - `255.255.255.0` = `/24`
- 네트워크 ID와 호스트 ID의 구분 위치를 유연하게 설정 가능