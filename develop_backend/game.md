# 게임 소켓 명세

# Client Emit

---

## 큐잉

- `enQ`
    
    Desc: 게임 참가 희망자가 게임을 시작하기 위해서 상대방을 매칭 받기 위해서 본인을 매칭큐에 `enqueue` 하기 위해 서버쪽으로 `enQ` event를 전송합니다.
    
    At: client의 게임시작 버튼을 누르게 되면 `enQ` event가 발생하게 된다.
    
    To: server 내부의 matching  Queue 서비스
    
    ```tsx
    {
    	isLadder: GameType
    }
    ```
    
- `deQ`
    
    Desc: 게임 참가를 희망했던 유저가 게임이 매칭되기 전에 매칭 큐에서 나오고 싶을때, 게임 취소를 누르게 될때  서버쪽으로 `deQ` event를 전송한다.
    
    At: 이미 게임시작 버튼을 눌렀던 유저가 게임 취소를 누를때
    
    To: server 내부의 matching Queue 서비스
    
    ```tsx
    {
    	isLadder: GameType
    }
    ```
    

## 대기방

- `rule`
    
    Desc: 게임 유저는 대기방에서 앞으로 진행할 게임의 룰을 정하게 된다. 각각의 유저가 정한 룰은 랜덤으로 적용되게 되며 게임 시작 후 어떤 룰이 적용되었는지 알 수 있게 된다.
    
    At: 플레이어가 자신이 원하는 룰을 수정할 때
    
    To: 상대편 유저에게 해당 데이터가 전송되고, 서버에서도 룰을 적용시킴.
    
    ```tsx
    {
    	option1?: GameOption;
    	option2?: GameOption;
    	option3?: GameOption;
    }
    ```
    
- `ready`
    
    Desc: 룰 선택을 완료한 플레이어가 준비가 되었을 경우 event를 발생시킬 수 있다. 또는 2분이 지나게 되면 자동으로 해당 이벤트가 전송된다. 준비 취소의 경우에도 같은 event명을 사용하지만 보내는 데이터를 달리한다.
    
    At: 대기방 입장 후 2분 경과 혹은 ready 버튼을 누를때
    
    To: server 내부의 game 서비스 와 상대편 플레이어
    
    ```tsx
    {
    	isReady: boolean;
    }
    ```
    

## 게임방

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
    

## 관전

1. `watch`
2. `unWatch`

# Server Emit

---

## 큐잉

- `game:match`
    
    Desc: 매칭 큐 서비스에서 매칭큐에 게임 참가 유저 두명이 들어 왔을 경우에 해당 두 유저에게 게임이 매칭 되었다는 이벤트를 전송하게 된다.
    
    At: client의 `enQ` event가 두 번 전송되어 두 유저가 매칭되었을때
    
    To: 게임을 참가를 희망 했던 두 유저.
    
    ```tsx
    {
    	blue: userId,
    	red: userId,
    }
    ```
    

## 대기방

- `rule`
    
    Desc: 게임 유저는 대기방에서 앞으로 진행할 게임의 룰을 정하게 된다. 각각의 유저가 정한 룰은 랜덤으로 적용되게 되며 게임 시작 후 어떤 룰이 적용되었는지 알 수 있게 된다. 서버는 이때 해당 룰을 중계해준다.
    
    At: 플레이어가 자신의 룰을 수정할때 서버로 온 데이터를 다른 플레이어에게 전송합니다.
    
    To: 룰을 변경하지 않은 다른 플레이어에게 해당 데이터를 전송합니다.
    
    ```tsx
    {
    	option1?: GameOption;
    	option2?: GameOption;
    	option3?: GameOption;
    }
    ```
    
- `ready`
    
    Desc: 룰 선택을 완료한 플레이어가 준비가 되었을 경우 event를 발생시킬 수 있다. 또는 2분이 지나게 되면 자동으로 해당 이벤트가 전송된다. 준비 취소의 경우에도 같은 event명을 사용하지만 보내는 데이터를 달리한다. 서버는 이때 해당 이벤트를 중계한다.
    
    At: 플레이어가 ready event를 발생시켰을때 발생합니다.
    
    To: another player
    
    ```tsx
    {
    	isReady: boolean;
    }
    ```
    

## 게임방

- `game:ready`
    
    Desc: 대기방에서 두 플레이어가 레디가 완료되었을때 게임 시작 전에 게임방으로 입장을 알리는 event를 전송해주게 됩니다.
    
    At: 두 플레이어 모두 레디가 됐을 경우에 보내게 됩니다.
    
    To: 두 플레이어에게 보냅니다.
    
    ```tsx
    {
    	metaData: Metadata;
    	ruleData: Ruledata;
    	ingameData: IngameData;
    	spec: Specdata;
    } // GameData
    ```
    
- `game:start`
    
    게임방으로 들어오고 나서 바로 시작되지 않고 몇초간 대기 시간을 주어지게 되는데, 이때 해당 텀을 주기 위해서 해당 event 명으로 data를 통해 ready와 start를 구현합니다.
    
    At: game이 시작 되기 전에 몇초간의 대기 시간을 유지하기 위해서
    
    To: 두 플레이어에게 보냅니다.
    
    ```tsx
    {
    	status: string = "start" | "ready"
    }
    ```
    
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
    

## 재접

1. `player:join`
2. `player:leave`
