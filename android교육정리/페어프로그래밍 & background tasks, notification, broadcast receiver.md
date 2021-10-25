# 페어프로그래밍 & background tasks, notification, broadcast receiver
 
## Service
- 명시하지 않는 한, 별도의 process에서 실행되지 않음.
- 스레드가 아니다
- 앱의 컴포넌트이므로 main thread에서 실행된다
    - 오래 걸리는 작업인 경우 작업이 끝날때까지 앱이 응답할 수 없으므로 추가 스레드를 생성해야한다.

※ Main thread와 다른 별도의 스레드 
= background thread 
= worker thread

### 소멸된 서비스 재시작 옵션
START_STICKY : 서비스가 소멸된 후 가능한 한 빨리 다시 시작되어야 함
START_NOT_STICKY : 기다리는 intent가 없다면 서비스가 소멸될 때 다시 시작하지 않음

## WorkManager 구현
- step 1
    - Work의 subclass에 background에서 수행할 작업 구현
- step 2
    - WorkRequest의 subclass로 작업 요청
- step 3
    - 작업 완료 알림 결정(by Broadcast Receiver)

## 복습 - Broadcast Receiver
- 독립 실행형(standalone) 브로드캐스트 수신자
- Service, Activity처럼 시스템에 등록한다
    - 앱이 실행중이지 않아도, 시스템이 앱을 시작하고 브로드캐스트를 전달한다
- intent-filter: 브로드캐스트 인텐트 필터링
