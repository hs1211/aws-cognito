# User Management with AWS Cognito
Amazon Cognito는 아래와 같이 크게 두가지 기능을 제공한다. 각 기능의 상세는 다음과 같다.
- User Pool: User Profile을 관리하는 기능을 제공한다. 가령, 특정 사용자의 이메일 정보와 패스워드를 가지고 있다.
- Federated Identities: 여러번의 로그인을 하나로 통하여 사용할 수 있도록 하는 기능을 제공한다. e.g) google, facebook, github를 통합하는 기능

> Free-tier로서 한달에 50,000 active user 접근을 허용한다.

## Chapters
- Initial Setup
- The Core Functionality
- Last Steps to FUll Fledged

---

### Intial Setup - Cognito


1. User pool을 만들어라
: 만약, Uber같은 시스템이라는 `driver`와 `rider`에 해당하는 두 개의 user pool을 만들어라.

2. Id로 email을 선택하라. password도 필수 요소도 적용된다.

3. Policy를 통하여 password 정책을 지정한다.

4. Verification 방식을 설정해라.
- MFA같은 기능을 추가적으로 연결할 수 있다. 
- email or SMS(초기 설정이 1로 되어 있기 때문에 케이스를 오픈해서 사용가능하면 - 요금이 발생함) 연결가능


5. app 이름과 refresh token 주기를 지정하라.
- app id는 cognito identifer이다. 
- 여러 app에서 동일한 cognito user pool에 접근할 수 있다.
- `Generate client secret` 의미??
 
### Intial Setup - Federated Identities
사용자에게 다양한 로그인 provider를 제공하려고 할 경우(cognito, facebook, gmail, etc) 사용할 수 있는 기능이다.

1. `Authentication providers`메뉴를 선택하라.

2. Cognito를 선택한다면, 
- identity pool, app client id 를 각각 등록해라.

3. Facebook을 선택하다면,
- facebook app id를 등록해라.

4. 설정이 끝나면 role생성화면으로 이동한다.
- authorization role에 대한 권한을 설정한다.
- unauthorization role을 설정한다.

### Backend Authentication
JWT set 을 AWS Cognito로부터 다운로드 받아야 한다. 
https://cognito-idp.{region}.amazonaws.com/{userPoolId}/.well-known/jwks.json

```text
{  
   "keys":[  
      {  
         "alg":"RS256",
         "e":"AQAB",
         "kid":"I7Kw/O0QymLQ8A0pPaXNcv5je7BNYXMCW1HdziUTyrQ=",
         "kty":"RSA",
         "n":"uIqZqU64ytLpQr3J86NMpjxZBRubRzovkQv22oAeHoxO_w4EZuvEeodCV7WxVatHwcVyH0VrkRsqcoigajJO5Xz3s-Ttz_ozhE8wP-BI3DUPOUNtGiKZirNLf9jluScrCUsyyim2UrF4ub-hsxGSt32GFRMfqrkvz0Ral4K4oeIiBNnX8cu_pbSlDgriBLAh8ago41XhqqSFtWwlP-x_KHJc13RBgETj7HOfEm5tr6ibJlMazL3FOoXehfXQw9Yr0752A2hTKAB8reUJXuAwcyTUa8ZEO6IcnhQiaPmIgltxdm-SHdoPqwR_SQxYzZfQzU9uE78ogWT-xP29Gr08Xw",
         "use":"sig"
      },
      {  
         "alg":"RS256",
         "e":"AQAB",
         "kid":"fxyn6hg0ziTNer+mBzqmxqGe38uh4neQPorXo3GAa/s=",
         "kty":"RSA",
         "n":"hMAECS0ALyFaP7OY4ZN5SXqPpkKOdp_RfNAmeCXhK98rmEnD_9Zzqb5oVviZZoqQ5xEZQBRR7a2JOZxL_JZWX7ObteHMSfNZywk8E9FN4XPMJxStZk5JSceKBd5SPYdLzTR58LFMg4OKONA5aJ1sYUu11zq6yMdUBvEJlwBjBrH4lfSkJ_jg4zSeKxsRcM72oAQ_yCnzO5giPoMjyY8VtqCj7NW_7njyQ-bD1WiGaNCkgBxWwYL_13zCxMJxNopa2vHoca0xn9bct-ysS8zIaB3DjNo_8-GGp_HJ4kNW0TczcILtl4mrl81srGzulvuK-mGF0T31IDY-tZWS3IgQYQ",
         "use":"sig"
      }
   ]
}
```

해당 코드는 다음과 같다.
```text
const pems = {}
for(let i = 0; i<jwt_set.keys.length; i++){
 const jwk = {
  kty: jwt_set.keys[i].kty,
  n: jwt_set.keys[i].n,
  e: jwt_set.keys[i].e
 }
 // convert jwk object into PEM
 const pem = jwkToPem(jwk)
 // append PEM to the pems object, with the kid as the identifier
 pems[jwt_set.keys[i].kid] = pem
}
exports.authCheck = function(req, res, next){
 const jwtToken = req.headers.jwt
 ValidateToken(pems, jwtToken)
   .then((data)=>{
    console.log(data)
    next()
   })
   .catch((err)=>{
    console.log(err)
    res.send(err)
   })
}
function ValidateToken(pems, jwtToken){
 const p = new Promise((res, rej)=>{
  const decodedJWT = jwt.decode(jwtToken, {complete: true})
  // reject if its not a valid JWT token
  if(!decodedJWT){
   console.log("Not a valid JWT token")
   rej("Not a valid JWT token")
  }
  // reject if ISS is not matching our userPool Id
  if(decodedJWT.payload.iss != userPool_Id){
   console.log("invalid issuer")
   rej({
    message: "invalid issuer",
    iss: decodedJWT.payload
   })
  }
  // Reject the jwt if it's not an 'Access Token'
  if (decodedJWT.payload.token_use != 'access') {
         console.log("Not an access token")
         rej("Not an access token")
     }
     // Get jwtToken `kid` from header
  const kid = decodedJWT.header.kid
  // check if there is a matching pem, using the `kid` as the identifier
  const pem = pems[kid]
  // if there is no matching pem for this `kid`, reject the token
  if(!pem){
   console.log('Invalid access token')
   rej('Invalid access token')
  }
  console.log("Decoding the JWT with PEM!")
  // verify the signature of the JWT token to ensure its really coming from your User Pool
  jwt.verify(jwtToken, pem, {issuer: userPool_Id}, function(err, payload){
   if(err){
    console.log("Unauthorized signature for this JWT Token")
    rej("Unauthorized signature for this JWT Token")
   }else{
    // if payload exists, then the token is verified!
    res(payload)
   }
  })
 })
 return p
}
```

## Reference
- [Cognito](https://medium.freecodecamp.org/user-management-with-aws-cognito-1-3-initial-setup-a1a692a657b3)
- [AWS WebService](https://medium.freecodecamp.org/the-complete-aws-web-boilerplate-d0ca89d1691f)
