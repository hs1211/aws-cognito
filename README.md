# API Gateway Authentication Method

Amazon API Gateway에서 Authorization을 위해서 아래와 같이 두 가지 방식을 제공한다.
- Cognito 
- Lambda Authorizer

---

## Cognito

1. Create a new user pool
- 1) Review defaults

2. General Settings > 'Add an app client'
- 1) Check ADMIN_NO_SRP_AUTH option

3. Signup user and Confirm
- 1) Create user and confirm
```bash
# Create a user
$ aws cognito-idp sign-up -region AWS_REGION -client-id CLIENT_ID \
    -username USERNAME -password PASSWORD -user-attributes Name=email,Value=EMAIL
    
# Confirm sign up
$ aws cognito-idp admin-confirm-sign-up -region AWS_REGION -user-pool-id USER_POOL \
    -username USERNAME
```
4. Wiring with API Gateway
- 1) Click 'Authorizers' menu
- 2) enter `User Pool Name`
- 3) enter `Field Name` in Token Source e.g) Authorization
- 4) Move the API and set up Authorization as a `Authorizer Name`

5. Get Access token
- 1) Invoke User Pool to get access token
```bash
$ aws cognito-idp admin-initiate-auth -region AWS_REGION -cli-input-json file://input.json

# Response
{
    "AuthenticationResult": {
        "ExpiresIn": 3600,
        "IdToken": "ID_TOKEN",
        "RefreshToken": "REFRESH_TOKEN",
        "TokenType": "Bearer",
        "AccessToken": "ACCESS_TOKEN"
    },
    "ChallengeParameters": {}
}
```

---

## Lambda Authorizer
아래 그림과 같이 람다 함수를 필터를 사용하여 해당 리소스에 접근하는 방식으로 구현한다.
```text
# Client(Access Token) --> API Gateway  ---> AWS Resource
                               |
                              \_/
                        Lambda Authorizer
```

- 아래는 Lambda 내부의 구현으로 input은 event 파라미터로 부터 추출하고, 결과로서는 policy를 리턴하는 방식으로 구현한다.

```javascript
const TOKEN = process.env.TOKEN;

const generatePolicy = (effect, methodArn) => {
    return {
        'policyDocument': {
            'Version': '2012-10-17',
            'Statement': [
                {
                    'Sid': '1',
                    'Action': 'execute-api:Invoke',
                    'Effect': effect,
                    'Resource': methodArn
                }
            ]
        }
    }
}

exports.handler = async (event, context) => {
    if(event.authorizationToken == TOKEN){
        return generatePolicy('ALLOW', event.methodArn)
    }
    return generatePolicy('DENY', event.methodArn)
}
view raw
```


## Reference
- [API Gateway + DynamoDB + Authorizer](https://hackernoon.com/full-guide-to-building-a-serverless-api-with-zero-code-c4f7871998f5)
