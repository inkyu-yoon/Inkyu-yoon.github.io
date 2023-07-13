---
layout: post
title: "· 3 Way Handshake와 4 Way Handshake는 무엇인가요?"
nav_order: 6
parent : Network
grand_parent: 📚Learned
permalink: docs/Learned/Network/tcp-handshake
---

# 3 Way Handshake와 4 Way Handshake는 무엇인가요?
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---


{: .important-title}
> 핵심
>
> 3-way handshake의 경우 연결을 수립하는 과정에서 진행되고 4-way handshake의 경우 연결을 해제하는 과정에서 진행됩니다.
> 
> 3-way handshake는 클라이언트가 SYN 패킷을 서버에 전송에 연결을 요청하면 서버는 ACK와 SYN 패킷으로 응답합니다.
> 
> 그리고 클라이언트가 연결 확정을 위한 ACK 패킷을 보내면서 마무리됩니다.
> 
> 이 과정이 3번에 걸쳐 이루어져 3-way handshake라 불립니다.
>
> 
> 4-way handshake는 연결을 해제할때 진행됩니다.
> 
> 연결 해제를 위해 FIN 패킷을 전송합니다.
> 
> FIN 패킷을 받은 쪽은 ACK 패킷으로 우선 응답합니다.
> 
> 남은 작업을 마치고 FIN 패킷을 연결 해제를 요청했던 쪽으로 전송합니다.
> 
> 연결 해제 요청을 했던 쪽은 ACK를 응답함으로서 마무리됩니다.



## **3-way handshake란?**

TCP/IP 프로토콜을 이용해서 통신을 하는 응용프로그램이 데이터를 전송하기 전에 먼저 정확한 전송을 보장하기 위해 상대방 컴퓨터와 사전에 세션을 수립하는 과정이다.  
 
<div align = "center">

<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230628160110371.png" alt="image-20230628160110371" style="zoom:80%;" />

</div>

### Client State

1. **CLOSED** : 데이터를 주고 받기 전 연결이 안된 기본 상태를 의미한다.  

2. **SYN-SENT** : 클라이언트 측에서 SYN을 보냈다는 상태, 서버측의 응답을 기다리고 있는 상태를 의미한다.  

3. **LISTEN** : CLOSED 상태에 있던 서버가 데이터를 받을 준비가 되어 대기하는 상태를 의미한다.  

4. **SYN-RECEIVED** : 클라이언트 측에서 보낸 SYN을 서버 측에서 잘 받았다는 상태를 의미한다.  

5. **ESTABLISHED** : 연결이 성공적으로 되었다는 상태를 의미한다. 클라이언트 측에선 서버의 응답을 받으면 해당 상태가 되고, 서버 측에선 응답을 보내고 다시 클라이언트 측에서 데이터를 보내면 해당 상태가 된다.  



### 3-way handshake 과정

1️⃣  **Client →  Server : SYN**

클라이언트는 서버와 커넥션을 연결하기 위해 `SYN` 을 보낸다.  

> PORT 상태
>
> - Client : `CLOSED → SYN_SENT`
> - Server : `LISTEN`



2️⃣  **Server → Client : ACK+SYN**

서버가 `SYN` 을 받고, 클라이언트로 받았다는 신호인 `ACK` 와 `SYN` 패킷을 보낸다.  

> PORT 상태
>
> - Client : `CLOSED`
> - Server : `SYN_RECEIVE`



3️⃣  **Client →  Server : ACK**

클라이언트는 서버의 응답은 ACK와 SYN 패킷을 받고, ACK를 서버로 보낸다.  

마지막으로 접속 요청 프로세스가 수락 확인을 보내 연결을 맺는다.  

이때, 전송할 데이터가 있으면 이 단계에서 데이터를 전송할 수 있다.  

> PORT 상태
>
> - Client : `ESTABLISED`
> - Server : `SYN_Receive → ESTABLISED`



## **4-way handshake란?**

4-Way Handshake은 연결을 해제 (Connecntion Termination)하는 과정이다.


<div align = "center">

<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20230628160739875.png" alt="image-20230628160739875" style="zoom:80%;" />

</div>

### Client State

1. **FIN-WAIT** : 클라이언트 측에서 서버에 연결을 해제하겠다고 신호를 보낸 상태를 의미한다.

2. **CLOSE-WAIT** : 서버 측에서 클라이언트로 부터 연결을 해제하겠다 신호를 받은 상태이며, 아직 전송이 끝날 때까지 대기하는 상태이다. 이러한 대기하는 상태는 전송이 끝나지 않은 데이터의 손실을 예방해준다.

3. **LAST-ACK** : 서버 측에서 전송이 끝나고 클라이언트 측으로 전송이 완전히 끝났다는 신호를 보낸 상태이다.

4. **TIME-WAIT** : 클라이언트 측에서 서버 측으로부터 전송이 끝났다는 신호를 받은 상태이며, 완전히 연결을 해제할 준비하는 상태이다. CLOSE-WAIT과 마찬가지로 전송이 끝나지 않은 데이터의 손실을 예방해준다.

5. **CLOSED** : 연결이 해제된 상태로 클라이언트 측과 서버 측의 기본 상태가 된다.



### 4-way handshake 과정

1️⃣  **Client → Server : FIN**

서버와 클라이언트가 연결된 상태에서 클라이언트가 close()를 호출하여 접속을 끊으려 한다.  

클라이언트는 서버에게 연결을 종료한다는 `FIN` 플래그를 보낸다.  

이때 `FIN` 패킷에는 실질적으로 ACK도 포함되어있다.  

> PORT 상태
>
> - Client : `ESTABLISHED → FIN_WAIT`
> - Server : `ESTABLISHED`

2️⃣  **Server → Client : ACK**

서버는 `FIN` 을 받고, 확인했다는 `ACK`를 클라이언트에게 보내고 자신의 통신이 끝날때까지 기다린다. (이상태가 TIME_WAIT 상태)

서버는 클라이언트에게 응답을 보내고 `CLOSE_WAIT` 상태에 들어간다.  

**남은 데이터가 있다면 마저 전송을 마친 후에 close( )를 호출하기 위해서이다.**  

클라이언트에서는 서버에서 `ACK` 를 받은 후에 서버가 남은 데이터 처리를 끝내고 `FIN` 패킷을 보낼 때까지 기다리게 된다. (`FIN_WAIT_2`)

> PORT 상태
>
> - Client : `FIN_WAIT`
> - Server : `ESTABLISHED → CLOSE_WAIT`


3️⃣  **Server → Client : FIN**

데이터를 모두 보냈다면, 서버는 연결이 종료에 합의 한다는 의미로 `FIN` 패킷을 클라이언트에게 보낸 후에,    

승인 번호를 보내줄 때까지 기다리는 `LAST_ACK` 상태로 들어간다.  

> PORT 상태
>
> - Client : `FIN_WAIT`
> - Server : `CLOSE_WAIT → LAST_ACK`


4️⃣  **Client → Server : ACK**

클라이언트는 `FIN` 을 받고, 확인했다는 `ACK`를 서버에게 보낸다.  

아직 서버로부터 받지 못한 데이터가 있을 수 있으므로 `TIME_WAIT`을 통해 기다린다. (실질적인 종료과정 `CLOSED`에 들어가게 된다.)  

이때 `TIME_WAIT` 상태는 **의도치 않은 에러로 인해 연결이 데드락으로 빠지는 것을 방지**한다.  

만약, 에러로 인해 종료가 지연되다가 타임이 초과되면 `CLOSED`가 된다.  

> PORT 상태
>
> - Client : `TIME_WAIT → CLOSED`
> - Server : `CLOSED`



>  TIME_WAIT이란? : Client가 Server로 부터 FIN을 수신하더라도 일정시간(디폴트 240초) 동안 세션을 남겨놓고 잉여 패킷을 기다리는 과정

