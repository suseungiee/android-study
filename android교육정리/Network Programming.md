# Network Programming

## REST
- stateless Web architecture(이전 상태 저장 x, 매번 새롭게 load)

## URI vs URL
### URI: 통합 자원 식별자 (Uniform Resource Identifier)
- 인터넷의 리소스를 나타내는 유일한 주소

### URL: URI의 유형중 하나
- Uniform Resource Locator (네트워크 상에서 리소스가 어디있는지 알려주는 규약)
- 웹 사이트 주소뿐만 아니라 컴퓨터 네트워크 상의 자원도 나타낼 수 있음

## Queries는 함수의 parameter와 비슷하게 생각가능.
- ?가 Query를 나타낸다
- 각 query parameter는 name, value를 가지며 'name = value'로 표기한다
- 여러개의 query parameter를 가질 수 있다
![](https://i.imgur.com/0eY0xjc.png)

## HTTP
- 클라이언트와 서버 사이에 이루어지는 request/response 프로토콜
- HyperText Transfer Protocol
- 반드시 Server에 어떤 작업을 요청하는지 알려줘야한다(GET, POST/PUT. DELETE)

## Server Respons Format
- XML
- JSON(javaScript object Notation) : 언어와 상관없이 key, value쌍으로 자료 표현하는데 제약이 없어 가장 널리 쓰이는 포맷

json포맷에서 {}가 의미하는것은 object라는 의미

## 직렬화 vs 역직렬화
### 직렬화
- 객체를 byte나 json으로 피는것
- 저장, 네트워크로 전송 시 주로 사용

### 역직렬화
- byte, json을 객체로 다시 변환
- 받은 데이터를 객체로 변환하여 코딩하기 위함
- GSON을 이용하면 역직렬화 편하게 가능

## RetroFit의 역할
![](https://i.imgur.com/x9nKbVI.png)

## Okhttp
- 라이브러리
- fluent builder패턴으로 설계된 라이브러리 (.찍으면서 매서드 호출하는거)
- Retrofit에서 사용중인 라이브러리

## Retrofit으로 Network API와 통신하기 위해 필요한 것
- Web service의 Base URL
- Server response를 client코드에서 사용할 format으로 반환해주는 Converter Factory (Gson같은거)
- response를 다룰 수 있는 반환 타입

## Network library없이 구현한다면?
- HttpUrlConnection으로 HTTP client생성해 직접 구성하면된다

## Coroutine
- 네트워크 api요청에 대한 응답은 언제 끝나는지 예측 어렵다
- 오래 걸린다면 그시간만큼 UI thread를 점유하게 된다