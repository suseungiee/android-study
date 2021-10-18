# Notification & Broadcast

## Notification
- 앱이 실행되고 있지 않을 때 Notification으로 메시지 전달가능(주로 실행되고있지 않을때 뜬다)
- 잠금화면 알림, 알림 창, 헤드업 알림, 앱 아이콘 배지
![](https://i.imgur.com/pYqpPco.png)

## Pending Intent
- 안드로이드 시스템에 등록되는 intent의 종류
- System에 App을 대신에 Intent를 수행할 수 있는 권한을 부여
- 따라서 application process가 종료된 상태더라도, pending intent는 다른 process로부터 실행될 수 있다
![](https://i.imgur.com/s25nm1q.png)

## Broadcast

## Broadcast Intent
```kotlin
sendBroadcast(Intent)
sendOrderedBroadcast(Intent, String)
```
- 위 메서드를 호출하여 전파되는 Intent 객체
- Broadcast Intent의 수신자로 등록된 다수의 Activity가 동시에 응답할 수 있다.

## Intent vs Broadcast Intent
### Intent
- messaging object로 다른 앱 구성요소에 작업을 요청하기 위해 사용
- Activity시작, Service시작, Broadcast전달
- 한개의 수신자만 존재
![](https://i.imgur.com/hyhn3ca.png)

### Broadcast Intent
- broadcast는 모든 앱이 동일시점에 동시에 수신할 수 있는 메시지(다른 앱도 포함)
- broadcast수신자로 등록한 다수의 컴포넌트가 메시지를 동시에 받을 수 있다
![](https://i.imgur.com/jXkI9lw.png)

## Broadcast Receiver
- 독립 실행형(standalone) 브로드캐스트 수신자
- service, activity처럼 시스템에 등록한다 -> 앱이 실행중이지 않아도, 시스템이 앱을 시작하고 브로드캐스트를 전달한다
- intent-filter : 브로드캐스트 인텐트 필터링. 필터된 intent의 action만 감지한다

## Custom이벤트를 수신하는 방법
- 브로드캐스트 인텐트
    - 시스템의 어떤 컴포넌트도 리스닝할 수 있다
    - 문제상황 : 외부에서 우리 앱의 수신자를 작동시킬 수 있게 된다
- 해결 방식
    - custom이벤트는 우리 앱에서만 전송할 수 있도록 제한하는 방법은 없을까?

## Broadcast전송
- 일반적인 broadcast(sendBroadcast(Intent))
- 모든 수신자에 브로드캐스트 전송
- onReceive는 main스레드에서 호출되므로 동시에 실행되지는 않지만 순서가 정해져있지 않다.
- 끝나는 시점을 알 수 없어, 수신자간에 소통이 어렵다
![](https://i.imgur.com/au4paxH.png)

- 순차적인 broadcast(sendOrderedBroadcast())
- 한 번에 하나의 수신자에 브로드캐스트 전송
- 차례로 실행되므로, 다음 수신자로 전파하거나 다른 수신자로 전달되지 않도록 할 수 있다
![](https://i.imgur.com/xin5AC4.png)

### 퀴즈 sendOrderedBroadcast 활용
#### 앱이 foreground에 있는 경우 알림을 보여주지 않으려면?
- 브로드캐스트 수신자가 앱의 lifecycle과 같은 수명주기를 가질 수 있을까?
- 앱의 foreground 상태를 어떻게 감지할 것인가?

### manifest에서 exported=true란?
- 다른 앱이나 서비스에서 access가 가능하다는 의미
- false로하면 내 앱에서만 동작하겠다는 의미. 특정 broadcast를 내 앱에서만 감지할때 사용