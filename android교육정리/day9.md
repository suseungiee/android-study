# 가시성 변경자
- public : 모든곳에서 접근 가능
- internal : 같은 모듈 안에서만 접근 가능
- protected : 최상위 선언에 적용 불가
- private : 같은 파일 안에서만 접근 가능


# 모듈이란
- build gradle로 인해 한번에 컴파일되는 묶음

## 확장함수는 어디서든 쓸 수 있다 (public)
ㄴ 따라서 internal로 선언된 클래스는 확장함수 쓸 수 없다. ( class의 범위가 더 좁아서)
ㄴ class를 public으로 변경해줘야 확장함수를 사용할 수 있다

## internal 사용하는 때
한 모듈에 속한 어떤 클래스를 모듈 밖에서 사용하는경우, 하위 클래스 내부의 메서드 이름이 상위 클래스의 메소드와 같아지는 경우를 방지
즉 모듈 외부에서 사용하는 것을 막기 위함

# 중첩클래스
내부에서 바깥쪽 클래스 인스턴스에 대한 접근 권한이 없다

## 언제 사용할까?
바깥쪽 클래스와 가까이 배치하여 캡슐화의 의도를 드러내고 싶을때

# 내부 클래스
바깐 클래스에 대한 참조를 저장 -> 중첩클래스와 차이점

최상위함수
자바에서는 모든 코드를 클래스의 메서드로 작성해야했다.
다양한 정적 메서드를 모아두는 역할만을 담당하는 특별한상태나 인스턴스가 필요없는 클래스가 생긴다. -> utility class같은거
-> 클래스로 안묶고 패키지로 묶어둘 수 있다.

main.kt에서 선언한 func()함수를 쓰려면 mainkt.func() 뭐 이런식으로 쓸 수 있다

# 최상위 프로퍼티
프로퍼티 역시 함수처럼 최상위 수준에 둘 수 있다 -> 전역에서 참조가능

## inline함수
함수 인자 중 1개이상이 람다여야 inline키워드를 추가할 수 있다
```kotlin
fun calculateBy(initial: Int, expr: (Int)->Int) = expr(initial)
println(calculateBy(3) { it*2}) 이렇게 쓴다
```

## inline함수의 이점
- 함수 본문을 번역한 바이트 코드로 컴파일한다
- 따라서 메서드를 호출해도 call stack에 쌓이지 않아 부하가 없다

### let
- null이 될수 있는 식을 다룬다

```kotlin 
taskId?.let { start(it, true)} 
```

### run
- 수신객체가 불필요


## kotlin과 메모리 관리

### 지역 변수
스택 프레임이 사라지면 함께 사라진다

### 클래스 멤버 변수
한번 static영역에 배치되면 JVM이 종료될 때까지 남아있는다

### 객체 멤버 변수
Garbage collector가 힙 메모리를 회수할 때 소멸

따라서 너무 자주사용되는 함수나 이런거는 클래스 멤버변수로 두는거 고려해보기

### 전역변수는 언제 문제가 되는가?
mutable일때 어디에서 변화되었는지 찾기 어렵다


# <span style = "color:green">LiveData</span> 
- Activity, Fragment와 같은 컴포넌트의 생명주기 상태가 변경됨에 따라 동작을 조정할 수 있는 클래스와 인터페이스를 제공한다

![](https://i.imgur.com/rpAcHxc.png)

### Observer Pattern
## Subject(관찰대상)
- 관찰대상에서 Observer가 등록되어야한다
그래야 LiveData(subject)에서 observe리스너를 이용하여 subject를 관찰할 수 있다
![](https://i.imgur.com/Se7UjN8.png)
subject에 observer를 등록하고 변화가 생기면 해당 등록된 리스너로 observer가 notify를 보낸다.
## 따라서 알림의 주체가 subject다!!

## LifecycleOwner
- Lifecycle의 소유권을 추상화하는 인터페이스 ( Lifecycle을 볼 수 있는 인터페이스)
- AppCompatActivity, Fragment는 이미 LifecycleOwner가 구현되어있다
- LifecycleOwner의 생명주기를 관찰하려면 LifecycleObserver를 구현해야한다
    ㄴ LifecycleOwner : 생명 주기 정보 제공
    ㄴ 등록된 LifecycleObserver : 생명 주기 변화 관찰


## LifecycleOwner가 없는 경우 생명주기 변경은 누가 알려주는가
- LifecycleRegistry클래스를 사용해 직접 이벤트를 전달해야한다.
```kotlin=
lateinit var lifecycleRegistry: LifecycleRegistry
lifecycleRegistry.markState(Lifecycle.State.CREATED)
```

## LiveData + Observer Pattern
- Lifecycle을 통해 생명주기를 인식하는 Observable한 data holder class
- LiveData는 LifecycleObserver로 UI Controller의 생명주기를 observe한다
- UI Controller는 LiveData의 값의 변경을 observe한다
![](https://i.imgur.com/wF3XC7G.png)

## Singleton
- 인스턴스를 하나만 만들어 사용하기 위한 패턴
- 인스턴스를 여러개 만드는 것이 불필요한 자원을 사용하는 것으로 판단될 때 사용한다

## 왜 사용하지 말라고 할까?
- 공유객체로 사용되는경우 위험하다 -> 어디서 변화되었는지 알기어렵다
- 오용을 줄이기위해 주로 immutable하게 구현해야한다. 즉 setter없이 getter로 하는게 좋다

### Eager Singleton
ㄴ class가 로드되자마자 생성된다
```kotlin=
class EagerSingleton private constructor() {
    val instance = EagerSingleton()
}
```

### Lazy Singleton
ㄴ Singleton을 처음으로 요청할 떄, 생성된다
```kotlin=
class Singleton private constructor() {
    var instance : Singleton? null
        get() {
            if (field == null) {
                field = Singleton()
            }
            return field
        }
}
```

### Object키워드는 Eager Singleton에 해당!!
ㄴ Lazy 쓰려면 Object쓰지말고 직접 구현해야한다