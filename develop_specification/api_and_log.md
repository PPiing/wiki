# API 및 로그 명세

## API 명세
- 요청 정보에 대한 명세는 헤더와 같은 공통적인 부분을 통일합니다.
- 응답에 대한 명세는 다음과 같은 기본적인 형태를 갖습니다.
```
에러 및 응답코드 / 메세지
```
- [Kakao Developers : request code - request](https://developers.kakao.com/docs/latest/ko/kakaologin/rest-api#request-code-request)
- [Kakao Developers : request code - response ](https://developers.kakao.com/docs/latest/ko/kakaologin/rest-api#request-code-response)
- 기본 형태
- 정해야 할 사항
  - version 정보 기입할 것인지
  - 요청 및 응답 형태 결정하기
### request
```
공통
header
'Authroization': Bearer + ACCESS_TOKEN (인증이 필요한 경우)
'Content-Type': application/json
 
GET
[query]
params: {
	'키': '자료형'
}

POST
[body]
data: {
	'키': '자료형'
}
```

### response
```
exports.getResponseForm = async (code, status) => {
    const rspForm = {
        status: status,
        data: {
            rsp_code: code,
        },
    };
    try {
        const ourErr = await `에러를 기입한 DB 모델`.findOne({
            where: { err_status: status },
        });
        rspForm.status = `에러를 기입한 DB 모델`.errorStatus;
        rspForm.data.rsp_code = `에러를 기입한 DB 모델`.errorCode;
        rspForm.data.msg = `에러를 기입한 DB 모델`.errorMsg;
    } catch (err) {
        console.error('[getResponseForm.js] error 관련 DB 접근 실패', err);
        rspForm.status = 500;
        rspForm.data.rsp_code = TSS000;
        rspForm.data.msg = '서버에서 error 관련 DB에 접근하지 못했습니다.';
    }
    return (rspForm);
};

res = getResponsForm(TS200, 200)
res.data = {
	access_token = ''
}
return res.json()
```
```
{
	rsp_code: 우리가 제공하는 자체 API 코드
	rsp_msg: 우리가 제공하는 자체 API 메세지
	rsp_data: 상황에 따라서 다른 명칭의 obj 형태 데이터
}, status=rsp_status
```

### Error DB 생성
- DB를 사용하는 이유: 중복될 수 있는 부분에 대한 수정시, hard 코딩을 최대한 막고, 수정사항이 생긴다면 수정이 용이하도록 설정하기 위함
- 내부 API를 사용하며 발생하는 에러 코드를 정하고, 해당 에러 코드에 대한 메세지를 매핑합니다.
- TSS(트랜센던스 서버): 내부 API 사용시 BE→FE로 에러를 던질 때 사용
FE에서 API 사용시 문제가 생긴다면, 어떤 문제인지 catch 할 수 있게 됩니다.
- TSC(트랜센던스 클라이언트): API 사용 결과를 FE→Client로 에러를 던질 때 사용
Client에서 서비스 이용시 문제가 생긴다면, 어떤 문제인지 catch 할 수 있게 됩니다.

| 내부 에러코드 | 에러 메세지                    | HTTP status 코드 | 부가설명                                                      |
| ------------- | ------------------------------ | ---------------- | ------------------------------------------------------------- |
| errorCode     | errorMsg                       | errorStatus      | errorComment                                                  |
| TSS001        | 42에 존재하지 않는 계정        | 401              | OAuth 사용시, 42 서울에 없는 계정으로 로그인 한 경우          |
| TSC001        | 42에 존재하지 않는 계정입니다. | 401              | 42 계정이 없는 상태로 로그인하는 클라이언트에게 보내는 메세지 |

[API template](api_template.md)

## Log
- 한 유저가 API를 요청하고부터 응답을 받을 때까지의 과정을 기록합니다. (→ user_seq를 과정마다 기록)
- API 요청과 응답 사이에서 사용한 함수, DB에 어떤 쿼리를 보냈는지를 기록합니다.

### 정해야 할 것
- Log file을 나눌 것인지

### API 요청 로그
- API 요청 시간
- 요청자의 브라우저 정보 (url 및 ip,  user-agent) → 기기에 대한 정보 기록을 위함
- 요청자의 userSeq
```javascript
const genApiReqLog = (req) => {
    const ret = [
        `[API_REQ] ${req.method} ${req.route.path} ${getTimeStamp()}`,
        ` - user_seq[user_ip]: ${req.user.user_seq}[${req._remoteAddress}]`,
        ` - user_agent: ${req.headers['user-agent']}`,
    ]
    console.log(ret.join('\n'));
};
```

### 함수 로그
```javascript
const genFunctionLog = (req, funcName, flag) => {
    let ret;
    if (flag) {
        ret = [
            `[FN_${funcName}] [LOG] ${getTimeStamp()}`,
            ` - user_seq[user_ip]: ${req.user.user_seq}[${req._remoteAddress}]`,
        ]
    } else {
        ret = [
            `[FN_${funcName}] [ERR] ${getTimeStamp()}`,
            ` - user_seq[user_ip]: ${req.user.user_seq}[${req._remoteAddress}]`,
        ]
    }
    return (ret.join('\n'));
}
```

### DB 로그
- 테이블 이름
- 쿼리 방식 (CRUD)
```javascript
const genDatabaseLog = (req, tableName, method, flag) => {
    let ret;
    if (flag) {
        ret = [
            `[DB_${tableName}] [LOG] [${method}] ${getTimeStamp()}`,
            ` - user_seq[user_ip]: ${req.user.user_seq}[${req._remoteAddress}]`,
        ]
    } else {
        ret = [
            `[DB_${tableName}] [ERR] [${method}] ${getTimeStamp()}`,
            ` - user_seq[user_ip]: ${req.user.user_seq}[${req._remoteAddress}]`,
        ]
    }
    return (ret.join('\n'));
}
```

### API 응답 로그
```javascript
const genApiResLog = (res, resForm) => {
    // eslint-disable-next-line no-underscore-dangle
    const ret = [
        `[API_RES] ${res.req.method} ${res.req.route.path} ${getTimeStamp()}`,
        ` - rsp_code[status_code]: ${resForm.data.rsp_code}[${res.statusCode}], rsp_msg[status_msg]: ${resForm.data.msg}[${res.statusMessage}]`,
        ` - user_seq[user_ip]: ${res.req.user.user_seq}[${res.req._remoteAddress}]`,
    ]
    console.log(ret.join('\n'));
};
```

### 참고
```javascript
const getTimeStamp = () => {
    const date = new Date(new Date() + 3240 * 10000).toISOString().split('T')[0]
    const time = new Date().toTimeString().split(' ')[0];
    return `<${date} ${time}>`;
}
```