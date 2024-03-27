# Transport Layer란?
- 애플리케이션 프로세스 간의 **논리적 통신**
- end  시스템에서 구현 (라우터에서 구현하는 것이 아님) -> end-to-end 통신이 직접 이루어지는 것처럼 보이게 함
- 단위 : **세그먼트**
- 프로토콜 : **TCP**, **UDP**
## vs Network Layer
우편물 배달에서 집과 집간의 배달은 Network Layer이고, 집 내부에서 누구에게 전달할지는 Transport Layer

- Network Layer : **Host** 간의 논리적 통신
- Transport Layer : **Process** 간의 논리적 통신

> 집 내부에서의 전달은 집간의 배달에 관여하지 않음
> 💡 Transport Layer는 네트워크 코어 내에서 메시지가 이동하는 방식에 대한 권한 없음
> 
> 집간의 배달은 집 내부에서의 배달에 관여하지 않음
> 💡 네트워크 코어는 Transport Layer의 정보에 대한 권한 없음
## Protocol
### 1️⃣ **TCP**
- Transmission Control Protocol
- 신뢰적이고, 순서 있는 전달
- Congestion Control : 송신 측이 속도 조절
- Flow Control : 수신 측이 속도 조절
- 3way-4way handshaking -> 연결, 종료 수립 과정 필요
- 헤더에 오류 검출 필드 -> 무결성 검사
### 2️⃣ UDP
- User Datagram Protocol
- 비 신뢰적, 순서 없는 전달
- 혼잡 고려 X
- 헤더에 오류 검출 필드 -> 무결성 검사
- best-effort IP 그 자체
	- IP : Network Layer의 프로토콜로서 비 신뢰적인 서비스 제공
# with 소켓
![](https://i.imgur.com/GB0hJGe.png)
소켓은 UDP, TCP인지에 따라 식별자가 달라짐 -> 패킷을 적절한 소켓에게 보낼 수 있어야 함
### 1️⃣ Multiplexing
- 각 데이터에 헤더 정보를 모아 캡슐화하고 네트워크 계층으로 전달하는 과정
### 2️⃣ Demultiplexing
- Transport Layer에서 세그먼트의 데이터를 올바른 소켓으로 전달하는 작업
- UDP 소켓의 구조
	1) 유일한 식별자
	2) 출발지 포트 번호 필드, 목적지 포트 번호 필드
- TCP 소켓의 구조
	1) 유일한 식별자
	2) 출발지 IP, 출발지 포트 번호 필드, 목적지 IP, 목적지 포트 번호 필드
		![](https://i.imgur.com/d3FU8Ta.png)

>- 16비트
>- 0 ~ 65535
>- **잘 알려진 포트 번호** : 0 ~ 1023
#  UDP
## 이용 예시
- 스트리밍 (real-time)
- DNS
- SNMP -> Congestion Control이 어려울 때 네트워크 관리를 하는 응용프로그램
- HTTP/3 (QUIC)
	- HTTP는 기본적으로 신뢰적이기 때문에 application Layer 자체에서 혼잡, 오류 제어 제공
## 구조
![](https://i.imgur.com/rFrNLsC.png)
### 체크섬
- 오류 검출
- 데이터 전송 완료 시, UDP 세그먼트 안의 비트에 대한 변경사항 확인
![](https://i.imgur.com/3CHwGQh.png)
- 2-bit error시 발견할 수 없음

> ⚠️ 각 계층은 서로 관여하지 않는 것이 원칙이지만 오류 검출의 경우 여러 Layer에서 중복으로 나타남
> -> 일종의 **안정 장치**, 모든 Data Link Layer가 오류 제어를 한다는 보장이 없음

> 💡 UDP는 오류 검출 비트가 존재하지만 오류가 검출되어도 아무것도 하지 않음

# TCP
## 신뢰적 데이터 전송
![](https://i.imgur.com/5JfqI2W.png)
TCP 이용 시, Transport Layer에서 신뢰적인 채널이 사용됨
![](https://i.imgur.com/DbdnxsD.png)
실제로 Network Layer의 IP가 비신뢰적인 데이터 전송을 하고, TCP를 이용하는 Transport Layer에서 신뢰성 검사를 함

> 💡 신뢰할 수 없는 계층 위에서 신뢰할 수 있도록 해야함 -> ***rdt***
### 1️⃣ rdt 1.0
- **FSM**
	- Finite-State machine
	- 유한 상태 머신
	- 다른 상태로 전환될 수 있는 송신자, 수신자 간의 모델
![](https://i.imgur.com/Dy4d0un.png)
#### Sender
- `rdt_send(data)`: 상위 계층으로부터 데이터를 받음
- `make_pkt(data)` : 받은 데이터로부터 패킷 생성
- `udt_send(packet)` : 패킷을 채널로 송신
#### Receiver
- `rdt_rcv(packet)` : 하위 계층으로부터 패킷을 받음
- `extract(packet, data)` : 패킷으로부터 데이터 추출
- `deliver_data(data)` :  상위 계층으로 추출한 데이터 전달
#### Result
- 완벽히 신뢰적인 전송 -> 피드백 필요 없음
- Receiver가 Sender의 전송 즉시 받을 수 있다는 가정 -> Congestion, Flow Control 필요 없음
### 2️⃣ rdt 2.0
- 비트 에러가 있는 채널
#### 응답 종류
1. **ACK** 
	- receiver -> sender
	- pkt이 잘 도착함 (***OK***)
2. **NAK**
	- receiver -> sender
	- pkt이 잘 도착하지 않음 (***재요청***)
![](https://i.imgur.com/IrVDAjQ.png)
![](https://i.imgur.com/zwGBuz5.png)
#### Sender
- **Wait for Above** : 상위 계층으로부터 데이터가 전달되기를 기다림
	- 이 후 과정은 rdt 1.0과 동일
- **Wait for ACK or NAK** : Receiver로부터 응답 기다림
	- `rdt_rcv(rcvpkt) && isACK(rcvpkt)` : 가장 최근에 전송된 패킷이 정확하게 수신되었음을 알게 됨
	- `rdt_rcv(rcvpkt) && isNCK(rcvpkt)`: 마지막 패킷을 재전송하고 Receiver로부터 ACK | NCK를 기다림
> 💡 **전송 후 대기** : NAK 이후 다시 응답을 기다리는 과정에서 상위 계층에서 데이터를 보낼 수 없음 (`rdt_send`)
#### Receiver
- `corrupt(rcvpkt)`: 패킷이 수신되었지만 오류 검출
- `notcorrupt(rcvpkt)`:  패킷이 수신되었고 정확하게 오류 없음
### 3️⃣ rdt 2.1
- rdt 2.0에서 ACK, NAK 자체가 손상되는 가능성은 배제함 -> rdt 2.1은 이를 처리할 수 있는 방법을 제시

1. 수신자의 응답을 다시 전달해달라고 하는 방법. 이 또한 오류가 발생할 수 있음
2. 충분한 체크섬 비트 추가
3. Sender가 왜곡된 ACK, NAK을 수신 시 Receiver가 패킷 재송신 -> 중복 패킷이 전송되며 Sender는 이것이 새로운 데이터인지 중복 패킷인지 알 수 없게됨

- 새로운 필드를 추가하고, 필드 안에 순서 번호를 삽입하는 방식
- 재전송인지 여부를 결정할 때 순서 번호를 확인
![](https://i.imgur.com/sJ3lPgd.png)
![](https://i.imgur.com/vnF8fcJ.png)
#### Sender
- 재전송시 같은 순서 번호를 사용하고, 새로운 패킷 송신 시 다른 순서 번호를 사용함
#### Receiver
- 패킷 수신 후, 패킷이 올바르다면 다른 순서 번호로 패킷을 기다리고 올바르지 않다면 같은 순서번호에 대해서 재전송 패킷을 기다림
### 4️⃣ rdt 2.2
- NAK free -> ACK 메시지를 항상 수신하며 순서 번호로 판단
![](https://i.imgur.com/Oid9fC4.png)
![](https://i.imgur.com/7rCXxgm.png)
#### Sender
- 데이터 수신 시, 수신 번호 0 혹은 1로 패킷을 보낸 후, 0번에 대한 응답을 대기함
- 응답이 ACK이며 송신한 수신 번호와 다를 시, 재전송을 하며 같을 시 다른 수신 번호를 이용하여 패킷을 보냄
#### Receiver
- 수신 번호 0 혹은 1에 대한 대기 중, 수신 받은 패킷이 같은 수신 번호를 가지고 있을 시, ACK와 해당 수신 번호를 가지는 패킷을 보냄
- 만약 다른 수신 번호를 가질 시, ACK와 다른 수신 번호를 가지는 패킷을 보냄
### 5️⃣ rdt 3.0
- 오류 + 손실 가능성
- 손실 검측 + 회복 -> **카운트다운 타이머**
	1. 첫 번째 혹은 재전송 패킷이 전송된 시점에 타이머 시작
	2. 타이머 인터럽트에 반응
	3. 타이머 멈춤

> ⚠️ 너무 빠르게 재전송을 할 시 중복 패킷
> ![](https://i.imgur.com/lHENS8x.png)

![](https://i.imgur.com/ZKRWDbQ.png)
- `timeout` :  카운트다운 타이머에 의해 인터럽트 타이머가 발생할 경우, 재전송
## 파이프라인
![](https://i.imgur.com/yzoWKSP.png)
전송 후 대기 방식으로 동작하지만, 여러 패킷을 전송하도록 허용

- 여러 패킷이 전송되기 때문에 순서 번호의 범위가 커져야 함
- 손실 패킷, 오류 패킷에 대해서 회복해야 함
### 1️⃣ GBN
- **Go-Back-N** : N부터 반복
![](https://i.imgur.com/NpUADah.png)
- base + N 패킷은 base 패킷의 응답이 확인되어 `Already ACK'd`가 되기 전에는 사용될 수 없음
![](https://i.imgur.com/0RpBZt7.png)
#### Sender
- `nextseqnum < base + N`
	- 조건 만족 시, 패킷 송신과 동시에 카운트다운 타이머를 동작, 다음 전송 후보 패킷의 인덱스값 증가
	- 조건 위배 시, 데이터 버림
- `getacknum(rcvpkt)` : 누적 ACK로 지금까지 성공한 패킷 중 마지막 전송 패킷의 순서 번호를 가지고 있음
- `timeout` : 인터럽트 타이머 발생 시 송신자는 이전에 전송되었지만 아직 응답이 확인되지 않은 모든 패킷을 보냄
![](https://i.imgur.com/5u4HECk.png)
#### Receiver
- `make_pkt(expectedseqnum, ACK, checksum)`: 성공한 패킷의 누적 수신 번호를 전달함
- 순서가 맞지 않는 패킷은 모두 버림 -> 많은 재전송을 필요로 함
	![](https://i.imgur.com/CbRFFDh.png)
### 2️⃣ SR
- **Selective Repeat** : 응답을 받지 못한 패킷만 재전송
![](https://i.imgur.com/Zpmstx8.png)
- 순서가 맞지 않아도 먼저 도착한 패킷은 가지고 있음
	![](https://i.imgur.com/HsRkK7L.png)
	- 순서에 맞는 패킷이 도착 시, keep해두었던 패킷에 대한 응답을 모두 보냄
![](https://i.imgur.com/jtrZPGc.png)
Receiver의 윈도우는 a, b모두 같지만 a의 경우 두번째 pkt0은 재전송이고, b의 경우 두번째 pkt0은 새 전송임
Receiver의 윈도우는 모두 pkt0을 받아야 할 차례이기 때문에 이를 구분하지 못함