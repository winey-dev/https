# 확장자 설명 
https://ililil9482.tistory.com/115
## .pem 
 인코딩 방식을 설명하는 확장자 이다.   
 다른 파일의 확장자 명으로 덮어질 수 있다.

## .key
 개인 키 파일을 구분하기 위한 확장자이다.   
 보통 .pem 인코딩 방식을 사용 한다.

## .csr
 SSL 발급 신청을 위해 인증기관 CA에 제출하는 요청서를 구분하기 위한 확장자이다.   
 보통 .pem 인코딩 방식을 사용 한다.   
 Certificate Signing Request의 약자이다.

## .crt/.cert
  생성한 .key(.pem)를 .csr(.pem) 요청하여 인증서 파일을 생성한다.

<br>
<br>

# 인증서 파일 생성
개인키/공개키를 키 알고리즘 암호화 알고리즘을 통해 생성 한다.
## 1. KEY(PEM) 생성 
~~~bash
# openssal genrsa [알고리즘] -out [파일명] [암호 길이]
~~~

### .key 확장자 
~~~bash
>> openssl genrsa -out server.key 2048
~~~

### .pem 확장자
~~~bash
>> openssl genrsa -out server.pem 2048
~~~

### 특정 알고리즘을 사용할 경우
~~~bash
>> openssl genrsa -aes256 -out server.key 2048
~~~

### 개인 private key를 공개키로 전환 
~~~bash
>> openssl rsa -in server.key -pubout -out public.key 2048
~~~

## 2. 다른 형식의 KEY 생성
~~~bash
# openssl genpkey -algorithm [알고리즘] -pkeyopt [알고리즘]_keygen_bits:[암호 길이] -out [파일명]
>> openssl genpkey -algorithm RSA -pkeyopt rsa_keygen_bits:2048 -out genpkey.key
~~~



## 3. 두개의 방식 차이점
1. genrsa vs genpkey -algorithm rsa 
> Differences between “BEGIN RSA PRIVATE KEY” and “BEGIN PRIVATE KEY"
>> https://stackoverflow.com/questions/20065304/differences-between-begin-rsa-private-key-and-begin-private-key

`genrsa`는 `PKCS#1`   
`genpkey -algorithm rsa`는 `PKCS#8`

# CSR 파일 생성 
(KEY에 대한 인증 요청서 파일을 생성한다)   
~~~bash
#openssl req -new -key {개인 key} -out {인증 요청서 이름}  -config {설정파일}// .csr로 만듬
>> openssl req -new -key server.key -out server.csr -config ./srver.conf
~~~

# KEY, CSR을 이용해서 CRT 파일 생성

### 사설 CA로 부터 인증서 생성
~~~bash
# rootCA key를 생성
openssl genrsa -aes256 -out rootCA.key 2038

# rootCA.key의 csr 생성
openssl req -x509 -new -nodes -key rootCA.key -days 3650 -out rootCA.csr -config ./rootCA.conf

# 나의 csr을 rootCA를 통해 인증서 생성
openssl x509 -req -in server.csr -CA rootCA.csr -CAkey rootCA.key -CAcreateserial -out server.crt -days 3650
~~~


### 공인 CA로 부터 인증서 생성
blabla...


## 정리하자면 .. 
키의 종류는 개인키/공개키가 존재하고 
개인키로 인증 요청서를만들고 
이 키과 요청서를 결합하여 인증서를 생성한다.



# 이런방법도.....1
~~~bash
# 개인 키와 공개 인증서를 생성
openssl req -newkey rsa:2048 -nodes -keyout key.pem -x509 -days 365 -out certificate.pem

# 작성된 인증서를 검토
openssl x509 -text -noout -in certificate.pem

# PKCS#12(P12) 번들에서 사용자 키와 인증서를 결합
openssl pkcs12 -inkey key.pem -in certificate.pem -export -out certificate.p12

# P2 파일의 유효성을 검증 
openssl pkcs12 -in certificate.p12 -noout -info
~~~
https://www.ibm.com/docs/ko/api-connect/5.0.x?topic=profiles-generating-self-signed-certificate-using-openssl



# 이런방법도.....2
https://endland.medium.com/private-key-public-key-8aa94b47edb9