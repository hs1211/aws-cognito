# JWT

---
## 구성

```
+-------------------+
|      Header       |
+-------------------+
|     Payload       |
+-------------------+
|       signed      |
+-------------------+
```

---
## Header
JSON object로서 JWT에서 사용하는 알고리즘이나 어떻게 파싱해야 할지에 대한 부분을 나타낸다.
아래 엘리먼트 중에 `alg` 만 유일하게 필수(required)이다. 나머지는 선택(optional)이다.
- alg: JWT를 signing을 하거나 복호화하는 알고리즘 
- typ: JWT 자체의 MEDIA 타입(거의 사용하지 않음)
- cty: JWT의 Payload 안content type Payload(claim + arbitrary data)  (거의 안씀)
- kid: key id

## Payload
사용자 데이터가 추가되는 부분으로 claim + arbitrary data로 구성된다. 아래는 간단한 예제이다.
```
{
  "sub": "1234567890", 
  "name": "John Doe", 
  "admin": true
}
```

Payload 부분에는 토큰에 담을 정보가 들어있습니다. 여기에 담는 정보의 한 ‘조각’ 을 클레임(claim) 이라고 부르고, 이는 name / value 의 한 쌍으로 이뤄져있습니다. 토큰에는 여러개의 클레임 들을 넣을 수 있습니다.

클레임 의 종류는 다음과 같이 크게 세 분류로 나뉘어져있습니다:
- 등록된 (registered) 클레임,
- 공개 (public) 클레임,
- 비공개 (private) 클레임


### 등록된 (registered) 클레임
등록된 클레임들은 서비스에서 필요한 정보들이 아닌, 토큰에 대한 정보들을 담기위하여 이름이 이미 정해진 클레임들입니다. 등록된 클레임의 사용은 모두 선택적 (optional)이며, 이에 포함된 클레임 이름들은 다음과 같습니다:

- iss: 토큰 발급자 (issuer)
- sub: 토큰 제목 (subject)
- aud: 토큰 대상자 (audience)
- exp: 토큰의 만료시간 (expiraton), 시간은 NumericDate 형식으로 되어있어야 하며 (예: 1480849147370) 언제나 현재 시간보다 이후로 설정되어있어야합니다.
- nbf: iat와 비슷한 개념입니다. 여기에도 NumericDate 형식으로 날짜를 지정한다.
- iat: 토큰이 발급된 시간 (issued at), 이 값을 사용하여 토큰의 age 가 얼마나 되었는지 판단 할 수 있습니다.
- jti: JWT의 고유 식별자로서, 주로 중복적인 처리를 방지하기 위하여 사용됩니다. 일회용 토큰에 사용하면 유용합니다.

### 공개 (public) 클레임
공개 클레임들은 충돌이 방지된 (collision-resistant) 이름을 가지고 있어야 합니다. 충돌을 방지하기 위해서는, 클레임 이름을 URI 형식으로 짓습니다.
```
{
    "https://velopert.com/jwt_claims/is_admin": true
}
```

### 비공개 (private) 클레임
등록된 클레임도아니고, 공개된 클레임들도 아닙니다. 양 측간에 (보통 클라이언트 <->서버) 협의하에 사용되는 클레임 이름들입니다. 공개 클레임과는 달리 이름이 중복되어 충돌이 될 수 있으니 사용할때에 유의해야합니다.
```
{
    "iss": "velopert.com",
    "exp": "1485270000000",
    "https://velopert.com/jwt_claims/is_admin": true,
    "userId": "11028373727102",
    "username": "velopert"
}
```


