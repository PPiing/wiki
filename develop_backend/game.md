# 게임 소켓 명세

# Client Emit

<details>
<summary> ## 큐잉 </summary>

- `enQ`
    
    Desc: 게임 참가 희망자가 게임을 시작하기 위해서 상대방을 매칭 받기 위해서 본인을 매칭큐에 `enqueue` 하기 위해 서버쪽으로 `enQ` event를 전송합니다.
    
    At: client의 게임시작 버튼을 누르게 되면 `enQ` event가 발생하게 된다.
    
    To: server 내부의 matching  Queue 서비스
    
    ```tsx
    ruleData: {
        matchScore: number,
        ballSpeed: number,
        paddleSize: number,
        isRankGame: boolean,
    },
    ```
    
- `deQ`
    
    Desc: 게임 참가를 희망했던 유저가 게임이 매칭되기 전에 매칭 큐에서 나오고 싶을때, 게임 취소를 누르게 될때  서버쪽으로 `deQ` event를 전송한다.
    
    At: 이미 게임시작 버튼을 눌렀던 유저가 게임 취소를 누를때
    
    To: server 내부의 matching Queue 서비스
    
    ```tsx
    ruleData: {
        matchScore: number,
        ballSpeed: number,
        paddleSize: number,
        isRankGame: boolean,
    },
    ```
</details>
  

<details>
    <summary> ## 게임방 </summary>

- `game:paddle`
    
    Desc: 게임이 시작 되면 플레이어는 자신의 패들을 조작할 수 있다. 정해진 키를 누르게 되면 해당 방향 정하는 event가 한번 발생한다. 이 후 해당 버튼을 떼게 되면 패들 움직임을 정지 시키는 event가 발생한다. 패들의 방향은 data로 전송되게 된다.
    
    At: 플레이어가 정해진 키를 누를 경우나 뗄 경우에 발생한다.
    
    To: server내부의 game simulation 서비스
    
    ```tsx
    {
    	direction: PaddleDirective; // 0(stop) , 1(up) , -1(down)
    }
    /*
        export const enum PaddleDirective {
          UP = 1,
          STOP = 0,
          DOWN = -1,
        }
    */
    ```

</details>

# Server Emit

---

<details>
    <summary> ## 큐잉 </summary>

- `game:ready`
    
    Desc: 매칭 큐 서비스에서 매칭큐에 게임 참가 유저 두명이 들어 왔을 경우에 해당 두 유저에게 게임이 매칭 되었다는 이벤트를 전송하게 된다.
    
    At: client의 `enQ` event가 두 번 전송되어 두 유저가 매칭되었을때
    
    To: 게임을 참가를 희망 했던 두 유저.
    
    Desc: 대기방에서 두 플레이어가 매칭되었을때 시작 전에 게임방으로 입장을 알리는 event를 전송해주게 됩니다.
    
    
    ```tsx
    {
        ruleData: {
            matchScore: number,
            ballSpeed: number,
            paddleSize: number,
            isRankGame: boolean,
        },
        blueUser: string, // name
        redUser: string, // name
    } // GameData
    ```
</details>

<details>
    <summary> ## 게임방 </summary>


- `game:start`
    
    게임방으로 들어오고 나서 바로 시작되지 않고 몇초간 대기 시간을 주어지게 되는데, 이때 해당 텀을 주기 위해서 game:ready후에 일정 시간 후 game:start를 발송합니다.
    
    At: game이 시작되었을때
    
    To: 두 플레이어에게 보냅니다.
 
    
- `game:render`
    
    Desc: game이 진행되는 동안은 모든 데이터는 서버에서 계산하게 된다. 해당 프레임마다 공의 위치나 패들의 위치는 서버에서 랜더되고 각각의 클라이언트에게 전송됩니다.
    
    At: each Frame render
    
    To: 두 플레이어(클라이언트)
    
    ```tsx
    {
    	ball: { x, y };
    	paddleBlue: { x, y };
    	paddleRed: { x, y };
    }
    ```
    
- `game:score`
    
    Desc: 한쪽이 득점을 하게 되면 점수 데이터를 보내주게 된다.
    
    At: 득점시에 서버에서 클라이언트로 보내준다.
    
    What: score data를 보내준다.
    
    ```tsx
    {
    	scoreBlue: number;
    	scoreRed: number;
    }
    ```
    
- `game:end`
    
    Desc: 한쪽이 게임 룰에서 정해진 목표점수를 달성하게 되면 game이 종료되고 game:end 데이터를 보내준다.
    
    At: 게임 목표점수 달성시에 게임이 종료되고 결과를 보내준다.
    
    What: 두 클라이언트에게 게임 결과를 보내주게 된다.
    
    ```tsx
    {
    	metaData: Metadata;
    	inGameData: InGameData;
    }
    ```
</details>

> ## 2022.07.24. 최신화

## 관전
- `game:watch`
    Desc: 게임 관전 신청
    At: 진행중인 게임 룸넘버를 알고 있고 게임을 관전하고 싶을때
    What: 해당게임의 진행상황을 이벤트로 전달받게 된다.
    ```ts
    {
        roomId: string;
    }
    ```
- `game:unwatch`
    Desc: 게임 관전 중단
    At: 진행중인 게임 룸넘버를 알고 있고 게임을 관전을 그만하고 
    What: 해당게임의 이벤트 전달을 중단한다.
    ```ts
    {
        roomId: string;
    }
    ```


## 재접 (*구현중)

1. `player:join`
2. `player:leave`
