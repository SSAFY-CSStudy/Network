# 2. application layer2

## 6. 인터넷 전자 메일

![Untitled](2%20application%20layer2%207b584612d5c84bfd8a45a2e09662200c/Untitled.png)

인터넷 메일 시스템의 상위 레벨 개념

### **Electronic mail**

**User Agents**, 사용자 에이전트

- mail reader
- 사용자가 메시지를 읽고, 응답하고, 전달하고, 저장하고, 구성하게 해줌
- ex) ms outlook, apple mail

**Mail servers**, 메일 서버

- 전자 메일 인프라스트럭처의 중심
- 각 수신자는 메일 서버에 mailbox 보유
    - mailbox : 수신자의 메시지를 유지하고 관리
    - message queue : 보낼 메일 메세지 보관
    - 일반 메시지는 송신자의 사용자 에이전트에서 전달이 시작되고, 송신자의 메일 서버를 거친 후에 수신자의 메일 서버로 전달된다.
    - 전자메일 박스에 있는 메시지를 보려면 메일 서버는 사용자 계정과 비밀 번호를 이용하여  이용자를 인증해야한다.
- 송신자는 메일 서버의 고장에도 대처해야 한다

**SMTP (Simple Mail Transfer Protocol)**
: 인터넷 전자메일을 위한 주요 애플리케이션 계층 프로토콜

- SMTP는 메일을 송신자의 메일 서버로부터 수신자의 메일 서버로 전송→ `TCP의 신뢰적인 데이터 전송 서비스` 이용
- Client와 Server를 갖고 있다
    - Client와 Server 모두가 모든 메일 서버에서 수행
    - 상대 메일로 송신할 때는 client가 되고 수신할 때는 서버가 됨
- client → server로 이메일 보낼 때 : TCP 이용, port 25
- direct transfer : 보내는 서버 → 받는 서버
- SMTP에서는 모든 메일 메시지의 몸체는 단순한 `7-bit ASCII` 
→ 전송 용량이 제한되어 커다란 첨부 파일이나 비디오 파일을 보낼 때 문제
- 전송의 3과정
    1. handshaking (greeting)
    2. transfer of messages (data exchange)
    3. closure
- alice → bob 에게 메시지 보내는 과정
    
    ![Untitled](2%20application%20layer2%207b584612d5c84bfd8a45a2e09662200c/Untitled%201.png)
    
    1. 앨리스는 `전자 메일 사용자 에이전트`를 수행하고, 밥의 전자 메일 주소를 제공하여 메시지를 보내라고 명령한다.
    2. 앨리스의 사용자 에이전트는 메시지를 그녀의 `메일 서버`에게 보내고, 그곳에서 메시지는 `메시지 큐`에 놓인다.
    3. 앨리스의 메일 서버에서 동작하는 SMTP의 클라이언트 측은 메시지 큐에 있는 메시지를 본다.
        
        **밥의 메일 서버에서 수행되고 있는 SMTP 서버에게 `TCP 연결`을 설정
        →** `애플리케이션 핸드 세이킹` 수행
        
    4. 초기 SMTP 핸드셰이킹 이후에 SMTP 클라이언트는 앨리스의 메시지를 TCP 연결로 보낸다. 
    → 25번 포트, TCP 연결, TCP 신뢰적인 데이터 전송 서비스
    5. 밥의 메일 서버 호스트에서 SMTP의 서버 측은 메시지를 수신한다. 밥의 `메일 서버`는 그 메시지를 밥의 `메일 박스`에 놓는다.
    6. 밥은 편한 시간에 그 메시지를 읽기 위해 `사용자 에이전트`를 시동한다.
- conclusion
    - SMTP는 중간 메일 서버 이용 안함
        
        → 메시지를 보낼 때 보내는 데 실패하더라도 중간 메일 서버에 저장되는 것이 아니라 **송신자(보내는 사람)의 메일 서버에** 남아있다
        
- client → server 사이에 메시지 전달 과정
    
    ![Untitled](2%20application%20layer2%207b584612d5c84bfd8a45a2e09662200c/Untitled%202.png)
    
    rfc : 웹 프로토콜 규약, 독자 프로토콜도 있음 찾아보기
    
    이전에 handshaking 했을 가능성 있음(socket에서 수행)
    

### DNS(Domain Name System)

- Host name : 인터넷 호스트의 식별자
    
    ex) `www.google.com`
    
    - 인터넷에서 호스트 위치에 대한 정보를 제공하지 않음 → IP 주소로 식별 진행
- IP address
    - 4바이트로 구성, 계층구조
- DNS : Host name을 IP address로 변환
    1. DNS 서버들의 계층구조로 구현된 **분산 데이터 베이스**
    2. 애플리케이션 프로토콜 : 호스트가 분산 데이터 베이스로 질의하도록 허락
        1. DNS 서버는 주로 BIND 소프트웨어를 수행하는 UNIX 컴퓨터
        2. DNS 프로토콜은 `UDP`에서 수행되고 `53번 포트` 이용
    3. host aliasing : 별칭 호스트을 가질 수 있다
    4. mail server aliasing
    5. **load distribution( 부하 분산 )**
        
        : 각 서버가 다른 종단 시스템에서 수행되고 다른 IP 주소를 갖는다. 하나의 정식 host name으로 연관되어 있다
        
        → DNS DB가 IP 주소집합을 갖고 있음
        
        - client가 주소 집합으로 매핑하는 호스트 이름에 대한 DNS 질의를 하면, 서버는 IP 주소 집합 전체를 가지고 응답 → 각 응답에서 주소는 순환식으로 전송
        ⇒ DNS의 순환 방식은 트래픽을 분산하는 효과
    
    > **DNS가 중앙 집중 데이터 베이스면 안되는 이유**
    > 
    > - 서버의 고장 : 하나의 네임 서버가 고장 나면, 인터넷 작동 안함
    > - 트래픽 양의 과부하 : 단일 DNS가 모든 질의를 해결해야함
    > - 먼 거리의 중앙 집중 DB : 멀면 멀수록 모든 질의가 느려진다.
    > - 유지보수
    >     - 단일 네임 서버는 모든 인터넷 호스트에 대한 레코드를 유지해야함
    >     - 모든 새로운 호스트를 반영하기 위해 자주 갱신되어야 하고, 사용자에게 호스트를 등록할 수 있도록 허용하는 인증 문제 존재
    > 
    > ⇒ **확장성**이 전혀 없다
    > 

### 분산 계층 데이터 베이스

![Untitled](2%20application%20layer2%207b584612d5c84bfd8a45a2e09662200c/Untitled%203.png)

- **Root DNS Server**
    - 루트 네임 서버는 TLD 서버의 IP 주소들을 제공
    - 1000개 이상의 루트 서버 인스턴스가 세계에 흩어져 있음
    - ICANN에 의해 조정
- **TLD(최상위 레벨 도메인) DNS 서버**
    - `com`, `org`, `net` 같은 상위 레벨 도메인과 `kr`, `uk` 같은 모든 국가의 상위 레벨 도메인에 대한 TLD 서버가 있음
    - Authoritative(책임) DNS 서버에 대한 IP 주소 제공
- **Authoritative(책임) DNS 서버**
    - 인터넷에서 접근하기 쉬운 호스트를 가진 모든 기관은 호스트 이름을 IP 주소로 매핑하는 공개적인 DNS 레코드를 제공해야 함
    → 기관의 책임 DNS 서버는 이 DNS 레코드를 갖고 있음
    - 기관은 직접 자신의 책임 DNS 서버의 구현을 선택할 수 있고, 일부 서비스 제공자의 책임 DNS 서버에 이 레코드를 저장하도록 비용 지불
    

**Local DNS Server**

- 로컬 DNS 서버들의 계층 구조에 엄격하게 속하지는 않지만 DNS 구조의 중심에 존재
- ISP는 로컬 DNS 서버를 갖고, 로컬 DNS 서버로 부터 IP 주소를 호스트에게 제공
- 대체로 호스트에 가까이 있기 때문에 지연이 적음
- 과정

위 그림에서 `cse.nyu.edu`가 `gaia.cs.umass.edu`의 IP 주소를 원한다고 가정해보자.

1. 자신의 로컬 DNS 서버에 질의를 보낸다. 이때 변환하고 싶은 호스트의 이름을 같이 보낸다.
2. 로컬 DNS 서버는 그 질의 메시지를 루트 DNS 서버에게 전달한다.
3. 루트 DNS 서버는 `edu`를 인식하고, `edu`에 대한 책임을 가진 TLD 서버의 IP 주소 목록을 로컬 DNS 서버에 보낸다.
4. 로컬 DNS 서버는 TLD 서버에 질의를 보낸다.
5. TLD 서버는 `umass.edu`를 인식하고, `dns.umass.edu`로 이름 지어진 책임 DNS 서버의 IP 주소로 응답한다.
6. 로컬 DNS 서버는 작접 책임 DNS 서버로 질의 메시지를 다시 보낸다.
7. 최종 `gaia.cs.umass.edu`의 IP 주소를 응답한다.
8. 호스트에 최종 IP 주소를 응답한다.

![Untitled](2%20application%20layer2%207b584612d5c84bfd8a45a2e09662200c/Untitled%204.png)

→ 총 8번의 DNS 메시지, 

일반적으로 TLD 서버는 위의 예시와 같이 책임 DNS 서버를 알지 않고, 책임 DNS 서버를 아는 중간 DNS 서버를 알고 있다.

즉, 해당 질의 과정 까지 포함되면 전체 10번의 메시지를 보내게 된다.

위 예는 `재귀적 질의`와 `반복적 질의`를 사용한다.

`cse.nyu.edu`로부터 `dns.nyu.edu`로 보내는 질의는 자신을 필요한 매핑을 대신하여 얻도록 `dns.nyu.edu`에 요구하므로 재귀적 질의이고, 나머지는 반복적 질의다.

일반 질의는 전형적으로 반복적 질의를 따른다.

- 재귀적 질의에서는 높은 계층에 있는 DNS server가 책임져야 하는 것들이 많다.
    - puts burden of name resolutions on contacted name server
    - heavy load at upper levels of hierarchy
- 중요한 infra를 지키는 것이 훨씬 낫기 때문에, 중요한 root name server 보단 default name server가 일을 더 하는 것이 좋다.

![Untitled](2%20application%20layer2%207b584612d5c84bfd8a45a2e09662200c/Untitled%205.png)

### **DNS 캐싱**

- DNS 지연 향상과 네트워크의 DNS 메세지 수를 줄이기 위해 사용
    
    → DNS 서버는 DNS 응답을 받았을 때 로컬 메모리에 응답에 대한 정보를 저장할 수 있음
    
    - 호스트 DNS와 IP사이의 매핑과 호스트는 영구적이지 않아서 어떤 기간(`TTL, Time to Live`) 이후에 저장된 정보 제거
        
        `Time to Live`  : 자원 레코드 생존 기간
        
    - TLD 서버들은 로컬 네임서버에 캐시된다. → root name servers는 자주 방문되지 않는다
- cached entries는 out-of-date일 수도 있다
    
    → host name이 IP를 바꾸면, TTL이 만료될 때까지 모를 수도 있다.
    

### **DNS 레코드와 메세지**

- 각 DNS는 하나 이상의 자원 레코드를 가진 메세지로 응답
- **자원 레코드(Resource Records)**
    - Host name을 IP 주소로 매핑하기 위한 자원 레코드 저장
    - 4개의 tuple로 구성 : `(Name, Value, Type, TTL)`
        1. **Type = `A`**
            
            > Address
            > 
            > 
            > Type A 레코드는 표준 호스트 이름의 IP 주소 매핑을 제공한다.
            > 
            - Name : 호스트 이름(hostname)
            - Value : 호스트 이름에 대한 IP 주소
        2. **Type = `NS`**
            
            > Name Server
            > 
            - Name : 도메인(domain)
            - Value : 도메인 내부의 호스트에 대한 IP 주소를 얻을 수 있는 방법을 아는 책임 DNS 서버의 호스트 이름
        3. **Type = `CNAME`**
            
            > Canonical NAME
            > 
            - Name : 정식 호스트 이름의 alias name
            - Value : 별칭 호스트 이름 Name에 대한 정식 호스트 이름
        4. **Type = `MX`**
            
            > Mail eXchange
            > 
            - Value : 별칭 호스트 이름 Name을 갖는 메일 서버의 정식 이름
            - MX 레코드는 메일 서버의 호스트 이름이 간단한 별칭을 갖는 것을 허용한다.
            

### DNS 메세지

DNS의 요청과 응답 메시지는 모두 같은 포맷을 갖고 있다. 

![Untitled](2%20application%20layer2%207b584612d5c84bfd8a45a2e09662200c/Untitled%206.png)

- Message Header
    
    `처음 12 byte의 헤더 영역` : 여러 필드를 갖고 있다.
    
    - 첫 필드는 질의를 식별하는 16 bit
        
        이 식별자는 질의에 대한 응답 메시지에 복사되어, 클라이언트가 보낸 질의와 수신된 응답 간의 일치를 식별하게 한다.
        
    - `플래그(flag) 필드`에는 여러 개의 플래그가 있다.
        - 1 비트의 `질의/응답 플래그` : 메시지가 질의인지 응답인지 구분
        - 1 비트의 `책임 플래그` : DNS 서버가 질의 이름에 대해 책임 서버일 때 응답 메시지에 설정
        - 1 비트의 `재귀 요구 플래그` : DNS 서버가 레코드를 갖지 않을 때 재귀적 질의를 수행하기를 클라이언트가 원할 때 설정
        - 1 비트로 된 `재귀 가능 필드` : DNS 서버가 재귀 질의를 지원하면 응답에 설
        

### **DNS 데이터베이스에 레코드 삽입**

도메인 네임 `networkutopia.com`을 `등록 기관(DNS registrar)`에 등록 가정

> 등록 기관(DNS registrar)은 도메인 네임의 유일성을 확인하고,
> 
> 
> 그 도메인 네임을 DNS 데이터베이스에 넣고, 그 서비스에 대한 요금을 받는 상업 기관이다.
> 

이전에는 작은 등록기관이 독점했었지만, 이제는 많은 기관이 경쟁하고 ICANN이 이러한 여러 등록기관을 승인해준다.

도메인 네임을 어떤 등록기관에 등록할 때 등록 기관에 주책임 서버와 부책임 서버의 이름과 IP 주소를 등록기관에 제공해야 한다.

- 주책임 서버 : `dns1.networkutopia.com` / 주책임 서버 IP : 212.2.212.1
- 부책임 서버 : `dns2.networkutopia.com` / 부책임 서버 IP : 212.2.212.2

위와 같다고 가정하자.

이 두 책임 DNS 서버 각각에 대해 등록 기관은 `Type NS`와 `Type A` 레코드가 **TLD com 서버에 등록되도록 확인한다.**

특히 주책임 서버의 경우 다음 두 개의 자원 레코드를 DNS 서버에 삽입한다.

```
(networkutopia.com, dns1.networkutopia.com, NS)
(dns1.networkutopia.com, 212.212.212.1, A)
```

또한, `Type A` 레코드와 메일 서버에 대한 `Type MX` 자원 레코드가 **책임 DNS 서버에 등록되는 것을 확인한다.**

이러한 모든 단계가 끝나면 여러 사람들이 웹 사이트를 방문할 수 있고, 전자메일을 보낼 수 있게 된다.

### DDoS 대역폭 플러딩 공격

공격자는 DNS 루트 서버로 다량의 패킷을 보내려는 시도를 하여 다른 DNS 질의들이 응답을 받지 못하게 하려 

## 7. P2P applications

### **클라이언트-서버 구조 분배 시간**

- **서버는 파일 복사본을 N개의 피어 각각에게 전송해야 함**
    
    → 따라서 서버는 `NF 비트` 전송
    
- 즉, 서버가 파일을 분배하는 시간은 적어도 `NF/u(s)`이다.
- d(min)이 가장 낮은 다운로드 속도를 가진 피어의 다운로드 속도를 나타낸다고 하자.
    - 가장 낮은 속도를 가진 피어는 F/d(min)초보다 적은 시간에 파일의 모든 F 비트를 얻을 수 없다.
    - 즉 최소 분배 시간은 `F/d(min)`이다.

즉, 분배 시간은 : `D(cs) ≧ max{ NF/u(s) , F/d(min)} //선형적으로 시간 증가`  

### **P2P 구조 분배 시간**

![Untitled](2%20application%20layer2%207b584612d5c84bfd8a45a2e09662200c/Untitled%207.png)

여기서는 각 피어들이 서버가 파일을 분배하는 데 도움을 줄 수 있다.

특히 한 피어가 파일 데이터 일부를 수신할 때, 피어는 그 데이터를 다른 피어들에게 재분배하는 데 자신의 업로드 용량을 이용할 수 있다.

- 분배가 시작되면 서버만이 파일을 갖고 있다. 
→ 하나의 파일은 복사되어야함
    - 이 파일이 피어 커뮤니티에 도달할 수 있도록 하기 위해, 서버는 적어도 한 번 접속 링크로 파일의 각 비트를 보내야 한다.
    - 최소 분배 시간은 적어도 `F/u(s)`다.(서버가 한 번 보낸 비트는 서버가 다시 보낼 필요가 없는데, 이는 **피어들이 그들 사이에서 재분배할 수 있기 때문이다.**)
- 클라이언트-서버 구조와 마찬가지로 다운로드 속도가 가장 낮은 피어는 F/d(min)초보다 적은 시간 안에 파일의 모든 F 비트를 얻을 수 없다.
    
    → 최소 분배시간은 적어도 `F/d(min)`이다.
    
- 마지막으로, **시스템의 전체 업로드 용량**은 전체적으로 서버의 업로드 속도와 각 피어들의 속도를 더한 것이다. 이를 u(total)이라 하자.
    - 시스템은 각 피어들 각각에게 F 비트를 전달해야 한다. 이는 u(total)보다 빠르게 할 수 없다.
    - 따라서 최소 분배 시간은 `NF/u(total)`이다.
- 분배시간 : `D(p2p) ≧ max{ F/u(s) , F/d(min), NF/u(total)`
    
    피어 수 N에 대해 **클라이언트-서버 구조**보다 **P2P 구조**가 더 시간이 적다는 것을 볼 수 있다.
    
    ⇒ P2P 구조를 가진 애플리케이션은 **자가 확장성**을 갖는다.
    

![Untitled](2%20application%20layer2%207b584612d5c84bfd8a45a2e09662200c/Untitled%208.png)

### BitTorrent

특정 파일의 분배로 참여하는 모든 peer의 모임

토렌트에 참여하는 피어들은 **서로에게서 같은 크기의 `청크(chunk)`를 다운로드한다.** (일반적으로 256KB)

- 처음으로 가입하면 그 피어에는 청크가 없지만, 시간이 지나면 점점 많은 청크를 쌓을 수 있다.(accumulate chunks over time from other peers)
- 피어가 청크를 다운로드할 때 또한 청크를 다른 피어들에게 업로드한다.
- 일단 한 피어가 전체 파일을 얻으면 토렌트를 떠나거나, 토렌트에 남아서 다른 피어들로 청크를 계속해서 업로드할 수 있다.

![Untitled](2%20application%20layer2%207b584612d5c84bfd8a45a2e09662200c/Untitled%209.png)

## 8. 비디오 스트리밍과 콘텐트 분배 네트워크

### 인터넷 비디오

- 초당 24개에서 30개의 이미지로 일정한 속도로 표시된다
- 압축되지 않은 디지털 인코딩 이미지는 픽셀 단위로 구성
- 특징
    - 압축될 수 있다 → 비디오 품질과 비트 전송률은 서로 반비례
    - 비트 전송률 : 일반적으로 고화질 동영상을 스트리밍하기 위해 100kbps에서 4Mpbs이상으로 구성
        
        ex) 4K 스트리밍은 10Mbps 이상의 비트 전송률로 예상, 하이엔드 동영상의 경우 트래픽과 스토리지 용량이 엄청나게 필요함을 의미
        
    - 연속 재생을 위해, 네트워크는 압축된 비디오의 전송률 이상의 스트리밍 애플리케이션에 대한 평균 처리량을 제공해야함
        
        → 압축을 사용하여 동일한 비디오를 여러 버전의 품질로 만들 수 있음
        

### HTTP 스트리밍 및 DASH

**HTTP 스트리밍에서 비디오**

- HTTP 서버 내의 특정 URL을 갖는 일반적인 파일로 저장
1. client는 server에게 TCP 연결을 설립하고 해당 URL에 대한 HTTP GET 요청을 발생시킨다
2. server는 기본 네트워크 프로토콜 및 트래픽이 허용되는 대로 HTTP 응답 메세지 내에서 비디오 파일을 전송
3. 애플리케이션 버퍼에 전송된 바이트 저장
4. 버퍼의 바이트 수가 미리 정해진 임계값을 초과하면 재생을 시작한다
    - 버퍼에서 주기적으로 비디오 프레임을 가져와 프레임을 압축 해제한 다음 사용자의 화면에 표시
- **문제점**
    
    가용 대역폭이 달라도 똑같이 인코딩된 비디오를 전송 받는 다는 문제가 있다 → HTTP 기반 스트리밍 인 `DASH` 개발
    

**Dash(Dynamic Adaptive Streaming over HTTP)**

- 비디오는 여러가지 버전으로 인코딩 되며, 각 버전은 비트율과 품질 수준이 서로 다르다
- client는 동적으로 서로 다른 버전의 비디오를 몇 초 분량의 길이를 갖는 비디오 조각 단위로 요청
    - **가용 대역폭이 충분할 때** : 높은 비트율의 비디오 버전을 요청
    - **가용 대역폭이 적을 때 :** 낮은 비트율의 비디오 버전을 요청
    
    ⇒ client는 자신의 상황에 알맞은 비디오 버전을 요청
    
- 각 비디오 버전은 HTTP 서버에 서로 다른 URL을 가지고 저장된다
    - HTTP 서버는 비트율에 따른 각 버전의 URL을 제공하는 manifest 파일을 갖고 있다
    - client는 이 manifests 파일을 제공 받고, 매번 원하는 버전의 비디오 조각 단위를 선택하여 HTTP GET 요청 메시지에 URL과 byte-range를 지정하여 요청
    
    ⇒ Dash는 client가 서로 다른 품질 수준을 자유롭게 변화시킬 수 있도록 허용
    

### CDN(Contents Distribution Network)

- 다수의 지점에 분산된 서버들을 운영하며, 비디오 및 다른 형태의 웹 콘텐츠 데이터의 복사본을 분산 서버에 저장
- **철학**
    - **Enter Deep**
        
        : 서버 클러스터를 세계 곳곳의 접속 네트워크에 구축함으로써 ISP의 접속 네트워크로 깊이 들어가는 것
        
        → 최대한 서버를 사용자 근처에 위치시켜 링크 및 라우터를 거치는 횟수를 줄여 지연시간 및 처리율을 개선하는 것
        
    - **Bring Home**
        
        : 좀 더 적은 수의 핵심 지점에 큰 규모의 서버 클러스터를 구축하여 ISP를 Home으로 가져오는 개념
        
        → 접속 ISP에 연결하는 대신, 일반적으로 CDN들은 그들의 클러스터를 `IXP(인터넷 교환 지점)`에 배치
        
        - Enter Deep보다 처리률이 낮고 delay가 더 걸릴 수 있음
        - 유지 보수하기 편하며 비용이 적게 듬
- 동작
    1. 사용자가 URL을 지정하여 비디오를 요청
    2. CDN은 그 요청을 가로채(`DNS redirection`) client에게 가장 적당한 CDN 클러스터를 선택
    3. client의 요청을 해당 클러스터의 서버로 연결
    
    ![Untitled](2%20application%20layer2%207b584612d5c84bfd8a45a2e09662200c/Untitled%2010.png)
    
    1. 사용자가 URL 입력
    2. 사용자의 호스트는 URL의 host name에 대한 질의를 Local DNS로 보낸다
    3. Local DNS는 CDN server의 책임(authoritative) DNS로 질의 전달책임 DNS 서버는 해당 질의를 CDN 서버로 연결하기 위해 CDN 서버의 책임 DNS 서버의 IP를 전달
    4. Local DNS는 CDN 서버의 책임 DNS로 질의를 보내고, CDN 콘텐츠 서버의 IP주소를 로컬 DNS서버로 응답
    → client가 콘텐트를 전송받게 될 서버가 결절
    5. Local DNS 서버는 사용자 호스트에게 CDN 서버의 IP 주소를 알려준다
    6. client는 호스트가 알게된 IP 주소로 HTTP 혹은 DASH 프로토콜을 통해 비디오를 받아온다.
        
        

## 9. Socket programming

클라이언트, 서버 프로세스가 socket으로 부터 읽고 쓰기를 통해 서로 통신

### 전송 서비스

**UDP** : unreliable datagram
송신 프로세스가 데이터의 패킷을 소켓 문 밖으로 밀어내기 전에, 먼저 패킷에 목적지 주소를 붙여 넣어야함

- handshaking을 하지 않는다
- data를 잃어버리거난 받지 못할 수도 있음

Client/ Server의 interaction : UDP

![Untitled](2%20application%20layer2%207b584612d5c84bfd8a45a2e09662200c/Untitled%2011.png)

1. 클라이언트는 키보드로부터 한 줄의 문자를 읽고 그 데이터를 서버로 보낸다.
2. 서버는 그 데이터를 수신하고 문자를 대문자로 변환한다.
3. 서버는 수정된 데이터를 클라이언트에게 보낸다.
4. 클라이언트는 수정된 데이터를 수신하고 그 줄을 화면에 나타낸다

- **Example**
    - Client
        
        ```python
        # socket module이다. 이 module을 통해 소켓을 생성할 수 있다.
        from socket import *
        
        #서버의 IP 혹은 서버의 호스트 이름을 할당한다.
        serverName = ’hostname’
        
        # 목적지 port 번호를 나타낸다.
        serverPort = 12000
        
        # 클라이언트 소켓을 생성한다. AF_INET은 IPv4를 사용하고 있음을 나타내고, SOCK_DGRAM은 UDP 소켓임을 의미한다.
        clientSocket = socket(AF_INET, SOCK_DGRAM)
        
        # 보낼 메시지를 입력 받는다.
        message = Input(’Input lowercase sentence:’)
        
        # 소켓으로 바이트 형태를 보내기 위해 먼저 encode()를 통해 바이트 타입으로 변환한다.
        # sendTo() 메서드는 목적지 주소를 메시지에 붙이고 그 패킷을 프로세스 소켓인 clientSocket으로 보낸다.
        # 클라이언트 주소도 같이 보내지는데 이는 자동으로 수행된다.
        clientSocket.sendto(message.encode(),(serverName, serverPort))
        
        # 패킷 데이터는 modifiedMessage에 저장되고, 패킷의 출발지 주소(IP, port)는 serverAddress에 할당된다.
        # recvfrom() 메서드는 2048의 버퍼 크기로 받아들인다. 
        modifiedMessage, serverAddress = clientSocket.recvfrom(2048)
        
        # 출력
        print(modifiedMessage.decode())
        
        # 소켓 닫기
        clientSocket.close()
        ```
        
    
    - Server
        
        ```python
        from socket import *
        
        # 포트 번호
        serverPort = 12000
        
        # UDP 소켓 생성
        serverSocket = socket(AF_INET, SOCK_DGRAM)
        
        # 12000 포트 번호를 소켓에 할당한다. 이를 통해 서버 IP 주소의 12000 포트로 패킷을 보내면 해당 소켓으로 패킷이 전달된다.
        serverSocket.bind((’’, serverPort))
        
        print(”The server is ready to receive”)
        
        while True:
            # 패킷이 서버에 도착하면 데이터는 메세지에 할당되고 패킷의 출발지 주소는 clientAddress에 저장된다.
            # 해당 주소로 서버는 응답을 어디에 보내야할지 알 수 있다.
            message, clientAddress = serverSocket.recvfrom(2048)
            
            # 바이트 데이터를 decode()하고 대문자로 변환한다.
            modifiedMessage = message.decode().upper()
            
            # 클라이언트 주소를 대문자로 변환된 메시지에 붙이고, 그 결과로 만들어진 패킷을 서버에 보낸다.
            # 서버의 주소도 같이 보내지는데 이는 자동으로 수행된다.
            serverSocket.sendto(modifiedMessage.encode(), clientAddress) 
        ```
        

**TCP** : reliable, byte stream-oriented

![Untitled](2%20application%20layer2%207b584612d5c84bfd8a45a2e09662200c/Untitled%2012.png)