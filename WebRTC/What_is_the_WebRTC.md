## WebRTC란?
**WebRTC(Web Real-Time Communications)**란, 웹 애플리케이션과 사이트가 중간자 없이 브라우저 간에 오디오나 영상 미디어를 포착하고 마음대로 스트림 할 뿐 아니라 임의의 데이터 교환할 수 있도록 하는 기술이다.
<U>**<span style="color:red">드라이버나 플러그인 설치 없이</span> 웹브라우저간 P2P 연결을 통해 데이터 교환을 가능하게 하는 기술‼**</U>

WebRTC는 P2P통신에 최적화가 되어있다.~~(이것때문에 n개월간 애를 먹었다.)~~

WebRTC의 주요 API에는 3가지가 있다.(해당 클래스에 의해서 실시간 데이터 교환이 일어난다)
- **MediaStream** - 카메라/마이크 등 데이터 스트림 접근
- **RTCPeerConnection** - 암호화 및 대역폭 관리, 오디오 또는 비디오 연결,Peer들 간의 데이터를 안정적이고 효율적으로 통신하게 처리하는 WebRTC 컴포넌트
- **RTCDataChannel** - json/text 데이터들을 주고받는 채널을 추상화한 API(일반적인 데이터의 P2P통신)

RTCPeerConnection으로 연결하고자 하는 peer의 정보를 주고받아 연결된다.
이 과정을 **Signaling**이라고 한다.

![](https://images.velog.io/images/ong_hh/post/bddd6c81-437e-4702-9892-e5d57b139e81/image.png)


Caller가 Signaling서버를 통해 자신의 SessionDescription을 보내면 Callee도 마찬가지로 Signaling서버를 통해 자신의 SessionDescription을 보낸다. 그 외에도 ICECandidate를 Signaling서버를 통해 주고 받으며 peer간 연결을 완료하고 Caller와 Callee간에 Media데이터를 주고 받는다.

**즉,** 두 명의 유저가 스트림을 주고 받는 것이고. 연결 요청을 한 자가 Caller, 연결을 받는 자가 Callee이다. 이 두 사람이 통신을 하기 위한 중간 역할을 해 주는 서버가 SessionDescription이다. 

## WebRTC의 P2P사용 시의 한계점
앞서 말했듯이 WebRTC는 P2P에 최적화 되어 있다.
하지만 Peer들 간의 단순 연결 또한 쉽지 않다. **왜?** 를 위해서 네트워크를 공부해보자..~~(네트워크를 배운지 n년이 지나서 개념이 어려웠다)~~

P2P 연결은 ip:port를 open 하거나 listen 함으로서 동작한다. 따라서 사전에 상호 간 연결 설정을 교환하고 동의해야 하는데, IP주소를 알고 있고 연결할 준비가 되었다고 해서 연결에 성공하는 것이 아니다. 공유기와 같은 라우터 장비 내부에는 NAT이라는 기술이 설정되어 있다.

NAT/방화벽 환경의 클라이언트는 내부망에서 사설 IP를 사용하다가, 외부망과의 인터넷 통신을 위해 NAT장비(라우터 혹은 방화벽)를 통해 공인 IP를 할당받아 인터넷 연결을 하는 구조를 가지고 있다. ([NAT개념정리](https://velog.io/@ong_hh/Network-NAT%EB%9E%80)) 이 구조는 외부에 있는 공인 IP로 구성된 호스트에 요청과 응답을 받을 수는 있지만, NAT는 일반적으로 요청하지 않은 패킷은 차단하는 특성을 가지고 있다. 

## NAT/방화벽을 통과하는 WebRTC기술
WebRTC는 NAT 통과 기법을 위해 다음의 네트워크 표준을 이용한다.

### ICE(Interactive Connnectivity Establishment)
브라우저가 peer를 통한 연결이 가능하도록 해주는 프레임 워크이다.

>- **peer간 단순 연결 시 작동하지 않는 이유들**
  - 연결을 시도하는 방화벽을 통과해야 함
  - 단말에 Public IP가 없다면 유일한 주소값을 할당해야 한다.
  - 라우터가 peer간의 직접 연결을 허용하지 않을 때 데이터를 릴레이해야 하는 경우

ICE는 위의 작업들을 수행하기 위해 STUN 서버를 이용하여 외부 주소를 획득하고 그것이 실패한다면 TURN 중계 서버를 통해 트래픽을 라우팅한다.

* STUN 서버는 외부 네트워크 주소를 얻는데 사용
* TURN 서버들은 직접(P2P) 연결이 실패할 경우 트래픽을 중계하는데 사용

### STUN(Session Traversal Utilities for NAT)
NAT들은 사설 로컬 네트워크에서 디바이스에 IP 주소를 제공하지만 이 주소는 외부에서 사용될 수 없다. 공용 주소가 없이는 WebRTC 피어(Peer)들은 통신할 수 있는 방법이 없다. 이 문제를 해결하기 위해서 WebRTC는 STUN을 사용.

STUN 서버들은 공용 인터넷에서 동작하며 아래와 같은 단순한 한가지 작업을 수행한다.
(NAT 뒤에서 동작한느 어플리케이션으로부터) 전달된 요청의 IP:port 주소를 확인하고 그 주소를 응답으로 되돌려 보낸다.

이 절차는 WebRTC 피어(Peer)가 그 자신에 대해 공용에서 액세스 가능한 주소를 획득할 수 있도록 한 뒤 직접 연결을 설정하기 위한 시그널링 메커니즘을 통해 또다른 피어(Peer)로 전송한다

![](https://images.velog.io/images/ong_hh/post/ffe352f9-2da1-4dfd-9ded-01b71957dc11/image.png)

### TURN(Traversal Using Relays around NAT)
TURN은 시그널링 데이터가 아니라 피어(Peer)들 사이의 오디오/비디오/데이터 스트리밍 릴레이를 위해 사용

TURN 서버들은 공용 주소들을 가지고 있으므로 설령 피어(Peer)들이 방화벽이나 프록시들 뒤에 존재하더라도 피어(Peer)들이 접속할 수 있다.

![](https://images.velog.io/images/ong_hh/post/fb676255-ec95-425f-8173-b5e11492ecb9/image.png)


## 정리
봐도봐도 헷갈렸는데 [millo](https://millo-l.github.io/WebRTC-%EA%B5%AC%ED%98%84%ED%95%98%EA%B8%B0-1-1-P2P/)님의 정리글을 보고 이해가 잘 되어서 가져왔다.

**
1. 우선 각각의 peer들은 STUN 서버에 자신의 Public Address와 접근 가능한지 여부를 전달 받는다.<br>
2. Peer1(Caller)이 createOffer를 통해 먼저 자신의 SessionDescription을 생성하고 Signaling Server를 통해 Peer2에게 전달한다.<br>
3. Peer2가 Peer1의 SessionDescription을 전달 받고 이에 대한 답으로 createAnswer을 통해 자신의 SessionDescription을 생성하고 Signaling Server를 통해 Peer1에 전달한다.<br>
4. Peer1과 Peer2 모두 자신의 SessionDescription을 생성한 후부터 자신의 ICECandidate 정보를 생성하기 시작하고 이를 각각 서로에게 전달한다.<br>
5. 서로의 MediaStream을 peer간 통신으로 주고 받는다.<br>
6. 만약 Peer1과 Peer2 둘 중 Symmetric NAT을 가진 Peer가 있는 경우 TURN 서버를 사용해 data relay로 연결을 진행해야 한다.<br>
**


**참고**
https://m.blog.naver.com/sehyunfa/221678622407
https://lovejaco.github.io/posts/webrtc-connectivity-and-nat-traversal/
https://millo-l.github.io/WebRTC-%EC%9D%B4%EB%A1%A0-%EC%A0%95%EB%A6%AC%ED%95%98%EA%B8%B0/
https://www.html5rocks.com/ko/tutorials/webrtc/infrastructure/
