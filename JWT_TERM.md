# JWT

## 구성
+-------------------+
|      Header       |
+-------------------+
|     Payload       |
+-------------------+
|       signed      |
+-------------------+


## Header
- alg: JWT를 signing을 하거나 복호화하는 알고리즘 
- typ: JWT 자체의 MEDIA 타입(거의 사용하지 않음)
- cty: JWT의 Pyload 안content type
