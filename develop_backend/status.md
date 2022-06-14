## 소켓 API 명세

### 서버의 소켓 송신

- event : status:update
    - desc : 현재 바뀐 자신의 상태를 유저시퀀스와 함께 FE로 보내줍니다.
    - at
        - online - 첫 소켓 연결시, 게임 종료 후
        - gaming - 게임 시작 시
        - offline - 소켓의 연결이 끊어졌을 때
    - to : 나와 친구 관계인 유저들에게
    - 예시 data

```tsx
{
	"userSeq" : 1
	"status" : USST10
}
```

---

### 서버의 소켓 수신

- event : handleConnection ( 처음 소켓이 연결되었을 때)
    - desc : 클라이언트와 처음 소켓이 연결되었을 때
    - at : 로그인 후
    - from : 클라이언트
    - 예시 data

```tsx
{
	"userSeq" : 1
}
```

- event : game:start
    - desc : 클라이언트가 게임을 시작할 때
    - at : 게임의 규칙을 정한 후 ready를 누르는 순간
    - from : 클라이언트
    - 예시 data

```tsx
{
	"userSeq" : 1
}
```

- event : game:finish
    - desc : 클라이언트가 게임을 시작할 때
    - at : 게임이 끝난 후 리다이렉트된 직 후
    - from : 클라이언트
    - 예시 data

```tsx
{
	"userSeq" : 1
}
```
