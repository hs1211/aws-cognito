# User Management with AWS Cognito
Amazon Cognito는 아래와 같이 크게 두가지 기능을 제공한다. 각 기능의 상세는 다음과 같다.
- User Pool: User Profile을 관리하는 기능을 제공한다. 가령, 특정 사용자의 이메일 정보와 패스워드를 가지고 있다.
- Federated Identities: 여러번의 로그인을 하나로 통하여 사용할 수 있도록 하는 기능을 제공한다. e.g) google, facebook, github를 통합하는 기능

> Free-tier로서 한달에 50,000 active user 접근을 허용한다.

## Chapters
- Initial Setup
- The Core Functionality
- Last Steps to FUll Fledged

### Intial Setup
초기 

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
 



### The Core Functionality

### Last Steps to FUll Fledged



## Reference
- [Cognito](https://medium.freecodecamp.org/user-management-with-aws-cognito-1-3-initial-setup-a1a692a657b3)
- [AWS WebService](https://medium.freecodecamp.org/the-complete-aws-web-boilerplate-d0ca89d1691f)
