# 캐싱 전략 & background tasks

## 캐싱 방법
- Room/SQLite
    - 자주 사용하는 데이터인 경우 주로 사용 
- SharedPreferences 
    - key/Value 형태
    - No query support
    - 작은크기의 데이터만 저장
- Files
    - 앱 별로 Private하게 관리 가능
    - 여러 개의 이미지/데이터 파일 저장
    - 데이터 조회 번거롭다

## Offline Caching 전략
1. 요청에 따른 결과 응답. Repository -> Network -> Repository
2. 응답 저장 Repository -> Database 
3. 부가적으로 저장 결과 알림 Database -> Repository
4. UI 갱신 View가 ViewModel통해 UI 업데이트

- Database에서 데이터 정제가 이루어지므로 Database를 원본으로 본다. (Single Source == 원본 소스를 1개로 두고 관리하자)
![](https://i.imgur.com/QTOkya7.png)

## OffLine Caching - 데이터 변경이 필요한 경우
### Database에 업데이트 후  Server에 갱신한다면(부적합)
- Database업데이트 후 Server에 갱신하다가 갱신 실패하면 Database의 정보와 Server의 정보가다르다. 즉 동기화가 되어있지 않은 상태. 서버와 다른 데이터를 가지고있는것은 의미가 없다..
### Server에 갱신 후 Database에 업데이트(적합)
- Server데이터 갱신에 성공할 때까지 Local Database 갱신 delay
- 그전까진 이전의 Database 사용
- 
## 고려사항 - Cache Invalidation
- 서버에서 데이터가 지워져서 caching한 데이터가 더이상 유효하지 않을 때 해결 방법?
- 서버에서 삭제 대상을 알려주거나, 데이터 저장 시간이 지나면 local에서 지워주면된다

## Background Tasks
- UI thread외의 다른 작업을 처리할때 만든다
    - 로그 전송
    - 데이터 가공
    - 미리 데이터 로드하기
### Coroutine으로 다 처리할 수 있지 않을까??
- background task는 보통 반복적으로 요청하게 되는 작업
    - 어떻게 자원을 효율적으로 사용하게 할 것인가?
    - 언제 실행하면 좋을까? (충전중, 디바이스 사용안할때, wifi 연결상태 ...고려)
### 효율적인 리소스 사용
- CPU, Disk 사용 최적화
- 네트워크 사용량 최소화
- 반복적인 작업은 최대한 적게
- background작업이 필요한지 다시 검토하기

## 캐싱 전략은 Offline mode를 위해서만 필요한가?
- Pre-fetching
    - 사용자가 다음에 요청할 작업을 미리 로드하기
    - 앱을 사용하고 있지 않을때
## Background work APIS
### 상황에 맞는 API 선택
- JobScheduler, AlarmManager, Service ...
### workmanager없을땐 버전별 분기 처리 필요했었다

## Recommended solutions
- kotlic coroutine
    - 사용자와의 상호작용이 끝날 때 종료되는 작업
- WorkManager
    - 지속적으로 실행해야 하는 경우
    - 몇 분 안에 완료되는 짧은 작업
    - 밤에 업데이트..
- Foreground Service
    - 음악 실행상태 계속 보여주는 것
- AlarmManager
    - 사용자와의 interaction이 없을 때

## Service
- background에서 오래 실행되는 작업을 수행할 수 있는 구성요소
- Foreground Service
    - 사용자가 인지할 수 있는 작업 수행
    - 음악 앱 재생 상태 표시/ 건강 앱 실시간 기록 표시
- Background Service
    - 사용자에게 직업 보이지 않는 작업 수행
    - 이미지 다운로드/ 예약된 작업
- Bound Service
    - 바인딩된 서비스 간 Client-Server로 상호작용할 수 있는 인터페이스 제공(다른 앱과 통신 가능)

## Service 생명주기
- component라서 생명주기 존재
![](https://i.imgur.com/GcGVowF.png)

## Started Service
- 다른 컴포넌트(activity, broadcast receiver)에 의해 실행됨
- 기본적으로 메인스레드 사용(broadcast reveiver도 main thread로 실행)
    - 성능 저하 고려 필요
    - 요청 간의 동기화가 필요한 작업 고려 필요
## Intent Service
- 메인 스레드와는 별도의 worker스레드에서 실행
- 큐에 담긴 모든 요청을 처리한 후 중단됨
- 각 요청을 동기식으로 처리할 필요가 없는경우에 적합

## WorkManager
- Service, background task사용이 어려워서 선호된다
### Worker
- task를 정의
### WorkRequest
- 언제 어떻게 task를 실행할 지 정의
- task 실행 제약조건 정의
- OneTimeWorkRequest : 한번만
- PeriodicWorkRequest : 주기적으로
### Enqueue
- WorkRequest를 넣어준다
- 실행 보장하는 방법? Threading in WorkManager
    - WorkManager database에 모든 작업 정보 저장
    - constraint가 없다면 바로 실행/ 있다면 해당 상태 알림
    - executor는 workerFactory로 worker생성 후, 실행 
    - WorkManager내부적으로 작업 상태 알려주는 객체(WorkInfo) 존재 + LiveData로 반환해주는 api존재 -> observing 가능
### Chains(작업의 순서 보장)
- 실행하려는 순서대로 workRequest에 enqueue
- workContinuation: acyclic graph node개념
- 아래 형태의 작업도 가능
![](https://i.imgur.com/CCHBBzY.png)
