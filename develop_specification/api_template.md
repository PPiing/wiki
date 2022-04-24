### 요청

- 헤더
- 쿠키

| Attr Name | Type | Description |
| --------- | ---- | ----------- |
| .         | .    | .           |

<hr>

### 응답

- 정상 응답
```
{
	data1: {
		'':'',
	}
	data2: {
		'':'',
	}
}, status=200
```

- 비정상 응답
```
{
	rsp_code: STRING,
	rsp_error: STRING,
	data1: {
		'':'',
	}
	data2: {
		'':'',
	}
}, status=4XX
```