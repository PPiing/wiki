# 알람 API 명세

## HTTP API 명세

- 백앤드 모듈의 /docs 엔드포인트에 접근하면 Swagger로 작성된 명세를 확인할 수 있습니다.

## 소켓 API 명세

### 서버의 소켓 송신

서버에서 소켓 데이터를 송신할 때에는 다음 인터페이스를 준수합니다. 인터페이스의 필드들은 이벤트에 맞게 데이터를 송부해 줍니다.

```tsx
interface ISocketSend {
  senderSeq?: number; // 송신자 ID
  alarmCode: AlarmCode; // 알람 코드 Enum
  message?: string; // 알람 메시지
}
```
알람 코드 Enum은 다음과 같습니다.
```tsx
enum AlarmCode {
  ALAM10 = 'ALAM10', // 업적 달성
  ALAM11 = 'ALAM11', // 레벨 달성
  ALAM12 = 'ALAM12', // 방 초대
  ALAM20 = 'ALAM20', // 친구 요청
  ALAM21 = 'ALAM21', // 게임 요청
}
```

알람 코드

- event : `alarm:normal`

  - desc : 서버에서 생성되는 알림 메시지를 클라이언트에 보내고자 할 때 호출됨

  - at : 접속 중인 클라이언트가 알람을 받을 때

  - to : 알림 메시지를 받는 클라이언트

  - 예시 data

    ```json
    {
      "alarmCode": "ALAM12"
      "message": "방에 초대되었습니다"
    }
    ```

- event : `alarm:confirm`

  - desc : 게임 초대나 친구 요청처럼 발신자가 있고 수락/거절이 필요한 알람이 올 때 호출됨

  - at : 게임 초대나 친구 요청을 받을 때

  - to : 수락/거절이 필요한 요청을 받은 클라이언트

  - 예시 data

    ```json
    {
      "senderSeq": 2, // 메시지를 전송한 사람
      "alarmCode": "ALAM20" // 알람 코드가 친구 요청이므로 2번이 친구 요청을 보냄
    }
    ```
