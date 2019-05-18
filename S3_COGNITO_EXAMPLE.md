# S3 Direct Upload and Cognito
아래 그림과 같은 순서로 접근해서 s3에 최종적으로 파일을 업로드하게 된다. 아래의 각 컴포넌트의 역할은 다음과 같다.
- User Pool: 사용자 관리 기능
- Identity Pool: AWS credential을 제공하는 기능을한다.

![S3 and Cognito](./1.png)


## Reference
- [Github](https://github.com/tensult/ngx-s3-upload)
