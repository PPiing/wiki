# 채팅 모듈 구성 및 명세
## 추가 설치한 모듈

```bash
$> npm install cache-manager @nestjs/schedule @nestjs/event-emitter
$> npm install -D @types/cache-manager @types/cron
```

캐시, 스케줄러, 이벤트 모듈을 추가합니다.

## 컨트롤러와 게이트웨이

채팅 프로젝트의 구성 요소는 다음과 같습니다.

- 컨트롤러 : HTTP Request/Respone 처리
- 게이트웨이 : Socket 송신/수신 처리
- 서비스 : 비즈니스 로직 전반
- 리포지토리 : RDBMS와 상호작용

그 밖에 추가로 사용하는 nest.js의 모듈(기능)은 다음과 같습니다. 아래 모듈들은 Spring Framework에도 존재하므로 nest.js의 기능보다 전반적인 개념이나 기타 사용 예를 참조하시려면 nest.js 외에도 Spring Framework 문서를 참조하시면 도움이 될 겁니다.

- cache : 채팅을 DB에 바로 저장하지 않고 캐시할 때 사용합니다.
- schedule : 특정 작업을 특정 주기 혹은 시간에 실행할 때 사용합니다.
- event : HTTP Request에 의해 특정 소켓 메시지를 클라이언트로 송부해야 할 때 컨트롤러와 게이트웨이 사이를 연결하기 위해 사용합니다.

## 채팅 API 명세

## 컨트롤러 구성 및 명세

컨트롤러는 HTTP API 요청 및 응답을 담당합니다. 현재 HTTP API는 다음 기능을 수행합니다.

- …
- POST /api/chatrooms/new
    - 새로운 채팅방을 만듬
    - 요청
        
        ```json
        {
          "chatType": string;
          "chatName": string;
          "password": string;
          "isDirected": boolean;
        }
        ```
        
    - 응답
        
        ```json
        201 response
        {
          chatSeq: string;
          chatName: string;
        }

        40x response
        TBD
        ```
        
- PUT /api/chatrooms/join/{roomId}
    - 채팅방에 들어감
    - 요청
        
        ```json
        {
          "chatSeq": number; // 들어가고자 하는 방 ID
        }
        ```
        
    - 응답
        
        ```json
        TBD
        ```
        
- DELETE /api/chatrooms/leave/{roomId}
    - 채팅방에서 떠남
    - 응답
        
        ```json
        204 response
        ```
        
- GET /api/chatrooms/message/{roomId}/{msgID}/{count}
    - 이전 채팅들을 {count} 만큼 가져옴. 이전 채팅의 기준인 {msgID}가 -1이라면 가장 최신의 채팅을 가져옴
    - 응답
        
        ```json
        TBD
        ```
        

## 게이트웨이 구성 및 명세

게이트웨이는 소켓 (socket.io) 통신을 담당합니다. 소켓 데이터의 송신 및 수신을 담당합니다.

### 서버의 소켓 송신

- event : join room
    - desc : 방에 조인하고자 할 때 호출됨
    - at : HTTP로 클라이언트(들) 을 방에 조인 요청할 때
    - to : 요청을 보낸 클라이언트를 포함한 룸 멤버들
    - data
        
        ```json
        TBD
        ```
        
- event : new room
    - desc : 새 방이 생성될 때 방 정보를 보냄
    - at : HTTP 방 생성 요청
    - to : 전부 / 룸 멤버들
    - data
        
        ```json
        TBD
        ```
        
- event : user leave
    - desc : 유저가 방을 나갈 때 (내보낼 때) 정보를 보냄
    - at : HTTP 요청으로 유저가 방을 나갈 때 (강퇴당할 때)
    - to : 나가는 (강퇴당하는) 클라이언트를 포함한 룸 멤버들
    - data
        
        ```json
        TBD
        ```
        
- event : rooms
    - desc : 채팅 소켓 연결이 성공적으로 성사될 때 클라이언트에게 본인이 소속된 방의 리스트를 보냄
    - at : 클라이언트 채팅 소켓 연결
    - to : 연결 요청한 클라이언트
    - data
        
        ```json
        TBD
        ```
        
- event : chat
    - desc : 클라이언트가 서버로 채팅을 전송할 때 해당 채팅을 보낸 클라이언트를 포함하여 방 유저들에게 보냄
    - at : 클라이언트가 서버로 채팅을 보낸 이후
    - to : 본인 포함 클라이언트가 속한 룸
    - data
        
        ```json
        TBD
        ```
        

### 서버의 소켓 수신

- event : chat
    - desc : 클라이언트가 메시지를 보낼 때 데이터를 처리함
    - at : 클라이언트가 메시지를 보낼 때
    - from : 클라이언트
    - data
        
        ```json
        TBD
        ```