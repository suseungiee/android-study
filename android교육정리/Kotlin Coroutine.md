# Kotlin Coroutine

## 동시성 코드의 여러가지 오류
### 경합 조건 = Race Condition
- 코루틴에서 suspend키워드는 현재 thread block시킨다음 실행시키므로 동시성 개념
- 코드를 동시성으로 작성했지만 특정한 순서로 실행된것으로 가정한 경우. 즉 종료가 순차적으로 될것이라 예상한 경우

### 원자성 위반
- 원자성 작업(atomic operations) : operation이 사용하는 데이터를 간섭없이 접근할 수 있다
- 단일 스레드(activity, fragement 등 ui영역)에서는 모든 코드가 순차 실행 -> 모든 작업이 원자성을 보장. 스레드가 하나만 실행되므로 간섭이 없다. 따라서 lifecycler이 순차적으로 실행된다
- 원자성 : 객체의 상태가 동시에 수정될 수 있을때, 그 상태의 수정이 겹치지 않도록 보장해야한다. 동시에 수정할 수 있다 = 데이터 손실이 발생가능하다

### 교착 상태
- 동시성 코드는 다른 스레드에서 작업이 완료되는 동안 실행을 일시 중단 / 차단하여 동기화를 진행해야한다 -> 안그러면 원자성 위반 발생가능
- 순환적 의존성이 있다면 앱의 실행이 중단될 수 있다
![](https://i.imgur.com/mrkg7Im.png)

## 코틀린 코루틴의 동시성
### 일시 중단 연산 = suspending computations
- 해당 스레드를 차단하지 않고 실행을 일시 중지할 수 있는 연산(block시키는것 x)
- 일시 중단된 스레드를 다시 시작하기 전까지, 다른 연산에서 사용할 수 있다
- 코루틴 = nonblocking연산
- 코루틴은 thread context전환이 아니라  coroutine context를 전환한다

### 코루틴 Dispatcher - Main
- Dispatcher.Main은 UI스레드에서 코드를 실행하겠다는 의미
![](https://i.imgur.com/co4CmkA.png)
- Main은 단일 스레드이므로 event queue에 쌓이는 순서대로 실행된다
- 따라서 왼쪽은 1 2 3순서로, 오른쪽은 3 1 2 순서로 실행 
- launch를 호출한 스레드가 Main이 아니라면 context스위칭 발생하고 아니면 이어서 실행된다 

### 코루틴 Dispatcher - Background
- Dispatcher.IO가 Dispatcher.Default보다 낫다. 스레드 많이 쓸 수 있어서.
- 네트워크 요청과 같이 waiting state가 존재하는 작업

### 코루틴 Dispatcher - Unconfined
- launch method가 호출되는 스레드에 따라, 실행되는 스레드가 결정된다 -> 블록이 어떤 스레드에서 호출되는지 따라가야하므로 잘 안쓰인다
- 즉시 실행된다(Main.immediate와 동일)

## 코루틴 in MVP pattern
### 호출하는 Thread 타입
- M, V, P 각 레이어는 어떤 Thread로부터 호출되는가?
- Thread를 지정해서 호출하는 코드를 어디에 배치해야 좀 더 읽기 쉬울까?
![](https://i.imgur.com/UpWlE4k.png)

## Data & File Storage
### Application Sandbox directory
- 안드로이드 기기의 각 app은 sandbox에 directory를 갖는다
- sandbox에 file을 저장하면 다른 app에서 접근하는것을 막아준다

### Rooting된 device
- Root 권한 획득
- 권한이 없어서 할 수 없던 작업 수행가능 ex) 다른 앱의 데이터를 읽어올 수 있다

### Internal Storage directories
- 이 디렉터리에 file을 읽고, 쓰는데 시스템권한이 추가로 필요 x
- 다른 앱은 내부 저장소에 저장된 파일에 접근할 수 없다 -> 민감한 데이터 저장하기에 유리
#### 구현 전 고려사항
- 저장하기 전 device의 여유공간이 있는지 확인
- 저장 가능한 형태인지
    - persistent file (DB)
    - cache file
    - /res/raw 디렉터리의 file을 read하는것도 가능


### External Storage

#### Scoped Storage
![](https://i.imgur.com/clAPVAT.png)
#### 구현 전 고려사항
- 외부 저장소 접근 가능 여부 확인 -> 저장위치 선택
- 저장 가능한 형태인지
    - persistent file
    - cache file
    - media 콘텐츠

### Shared Preference
#### 간단한 데이터 보존 용도
- 저장하려는 key-value집합이 작은 경우
- SharedPreference객체는 key-value쌍이 포함된 파일로 XML File이다
- SharedPreference interface를 구현하는 클래스를 통해 데이터를 읽거나 수정
- 앱의 sandbox에 저장된다
- 사용자 정보, 암호와 같은 민감정보는 저장 x -> 로그인 성공여부를 token(암호화 된 상태)으로 받아서 이걸 SharedPreference에 저장. 이걸로 로그인여부 판단한다.