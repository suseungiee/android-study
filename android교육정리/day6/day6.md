# day6

### **kotlin문법**

모든 kotlin클래스는 equals, hashCode, toString메서드를 갖는다

Any는 null이 될 수 없는 모든 클래스의 조상 클래스

data class 권장사항 : 모든 데이터를 읽기 전용으로 만들자(immutable → val 키워드 사용)

### **동등성**

객체마다 생성될 때 해시값이 부여되며 이는 다 다르다.

객체 동등성비교를 해주기위해서는(hashCode()함수 override필요)

따라서 데이터를 저장하는 클래스라면 toString, equals, hashCode를 override해줘야한다 → data class는 이러한 번거로움을 덜어준다

### **hashCode**

해시 함수 : 임의의 길이의 데이터를 고정된 길이의 데이터로 매핑하는 함수

hashing : 해시테이블을 인덱싱하기 위해 해시 함수를 사용하는 것

ㄴ 검색 최적화에 필요하다.

### **HashSet의 원소 비교 방식**

- 객체의 해시코드 비교 후, 해시 코드가 같은 경우에만 실제 값 비교

### **중첩클래스**

한 클래스가 다른 클래스와 강하게 연관되어 있다는 의미를 전달하기 위함

중첩클래스 vs 내부클래스

1. 중첩 클래스는 각 내부와 외부 클래스간 데이터 공유가 불가능

    but 내부클래스를 사용하면 내부에서 외부 클래스 데이터 접근가능/ 그반대는 불가

    ㄴ this@외부클래스변수 이렇게 접근가능

2. 중첩 클래스는 외부 클래스의 인스턴스 없이도 생성가능→ 즉 독립적 객체 생성가능

     but 내부 클래스는 외부 객체가 있어야만 인스턴스 생성가능

 

코틀린에서 ${}안에 객체 가져오는 이유는 {}이 toString() override해서 그런거다

따라서list를 ${}로 출력하면 내부 원소가 찍힌다

일반객체는 직접 toString() override해줘야한다

static 클래스의 함수, 변수 : 클래스 함수, 변수

일반 클래스의 함수, 변수 : 인스턴스 함수, 변수

### **companion object**

- 클래스 안에 정의된 일반 객체

    ㄴ 이름 붙이기, 인터페이스 상속, 확장 함수, 프로퍼티 정의 가능

- 부 생성자개념? **같은 클래스의 여러객체에 공유하는 값이라 생각**

    ㄴ static변수와 같은 개념

- 한 클래스당 1개만 선언 가능

```kotlin
class FoolPoll (val name : String) {
	companion object {
		var total = 0
	}
	var count = 0
	fun vote() {
		total++
		count++
	}
}
var a = FoolPoll("자장면")
var b = FoolPoll("짬뽕")

a.vote(); a.vote();
b.vote(); b.vote(); b.vote()

print("${a.count}, ${b.count}, ${FoolPoll.total}) 
// 다른 인스턴스이지만 companion object로 공유하는 값이므로 2, 3, 5가 출력된다
```

### **람다**

- 다른 함수에 넘길 수 있는 작은 코드 조각
- 메소드가 하나뿐인 무명 객체(SAM) 대신 사용할 수 있다

    ㄴ SAM (Single Abstract Method)

### **고차 함수**

- high order function : 다른 함수를 인자로 받거나 함수를 반환하는 함수

    코틀린의 람다 : 함수를 값으로 표현할 수 있다

    즉 코틀린의 고차함수 : 람다를 인자로 받거나 반환하는 함수

    ```kotlin
    val sum : (Int, Int) → Int = { x : Int, y : Int → x + y }
    print(sum(4,5))  // 9
    ```

### **확장 함수 (extension function)**

- 어떤 클래스의 멤버변수인것처럼 사용 가능
- 클래스 밖에 선언된 함수

```kotlin
fun **String**.lastChar(): Char = **this**[**this**.length - 1]
```

String : 수신 객체 타임

this : 수신 객체

이후 모든 String타입의 객체에 대해 lastChar()사용가능

ex) 리스트 stack pop기능

```kotlin
fun<T> MutableList<T>.pop() = if (isEmpty()) null else removeAt(lastIndex)
```

### **자주 사용하는 inline 함수**

**inline사용하면 이점?**

- 프로그램의 실행 속도를 높이기 위한 방법

인라인 함수를 사용하면 컴파일된 함수 코드가 프로그램의 코드 안에 직접 삽입된다. 즉 컴파일러가 함수를 호출하는 대신 그에 대응하는 함수 코드로 대체하여 함수의 위치로 점프할 필요가 없다.

함수의 크기가 크고 오래걸린다면 오히려 시간이 많이 걸릴 수 있다.

**장점** : 함수가 인라인화되어 성능의 향상으로 이어질 수 있다

**단점** : 메모리 사용면에서 불리하다. 함수 1개가 10번쓰여진다면 10번 복사해야하므로.

**범위 지정 함수**

- with, run, let, apply, also함수로 구성. 다들 비슷하다

    **with** : 결과가 필요하지 않는 경우 사용

    **apply** : 객체 초기화에 사용

    **run** : 어떤 값을 계산하거나, 여러 지역 변수의 범위를 제한할 때 사용

    **let** : 해당 값이 null이 아닌 경우 코드를 실행할 경우, 단일 지역변수의 범위 제한할 떄 

    **also** : 객체의 멤버 변수에 값 할당하기 전 유효성 검사할 때 사용

- 특정 범위(scop)내에서 특정 객체를 this, it으로 사용할 수 있게 해준다 → 코드 간결하게 해준다

**제네릭 약어**

E : Element        K : Key

N : Number      T : Type

V : Value           R : Return Type

**일반 람다**

- (T) → R 형태

**수신 객체 지정 람다**

- T.() → R 과 같은 형태
- 람다의 확장함수처럼 수신객체를 사용한 것

즉 확장함수에서 수신객체를 사용하여 블록내에 객체를 전달한 것 처럼 수신객체를 이용하여 객체 전달

- **public inline fun <T, R> with(receiver: T, block: T.() → R): R {**

    ㄴ return값이 필요하지 않은 경우에 사용

```kotlin
val person: Person = getPerson()
	with(person) {
		print(name)
		print(age)
	}
```

- **public inline fun <R> run(block: () → R): R {**

    ㄴ 값을 계산하거나 여러개의 지역 변수의 범위를 제한할 떄 사용

```kotlin
val isAdd: Boolean =run{
// 지역변수1
        val person1 = Person("park", "jieun")
        // 지역변수2
        val person2 = Person("park", "jieun")

        isAdd()
}
```

- **public inline fun <T, R> T.run(block: T.() → R): R {**

    ㄴ 수신객체만 다르지 동작은 위와 동일

```kotlin
fun test(person: Person) = person.run{
// person을 수신객체로 변환하여 사용
        printPerson(lastName + firstName)
}
```

- **public inline fun <T, R> T.let(block: (T) -> R): R {**

    ㄴ 지정된 값이 null이 아닌경우 코드를 실행해야하는 겨우

    ㄴ Nullable객체를 다른 Nullable객체로 변환하느 ㄴ경우

    ㄴ 단일 지역 변수의 범위를 제한하는 경우

```kotlin
getNullablePerson()?.let {
    // null 이 아닐때만 실행됩니다.
    promote(it)
}
val driversLicence: Licence? = getNullablePerson()?.let {
    // nullable personal객체를 nullable driversLicence 객체로 변경합니다.
    licenceService.getDriversLicence(it) 
}
val person: Person = getPerson()
getPersonDao().let { dao -> 
    // 변수 dao 의 범위는 이 블록 안 으로 제한 됩니다.
    dao.insert(person)
}
```

- **public inline fun <T> T.apply(block: T.() → Unit): T {**

    ㄴ 객체 초기화에 사용

```
val result = person?.apply{
this.lastName = ""
        this.firstName = ""

        lastName = ""
        firstName = ""
}
```

- **public inline fun <T> T.also(block: (T) → R): R {**

    ㄴ객체의 멤버 변수에 값 할당하기 전 유효성 검사할 때 사용

```kotlin
class Book(author : Person) {
	val author = author.also {
		requireNotNull(it.age)
		print(it.name)
	}
}
```

[https://medium.com/@limgyumin/코틀린-의-apply-with-let-also-run-은-언제-사용하는가-4a517292df29](https://medium.com/@limgyumin/%EC%BD%94%ED%8B%80%EB%A6%B0-%EC%9D%98-apply-with-let-also-run-%EC%9D%80-%EC%96%B8%EC%A0%9C-%EC%82%AC%EC%9A%A9%ED%95%98%EB%8A%94%EA%B0%80-4a517292df29)

**그 외 자주쓰는 inline함수**

- **public inline fun <T> T.takeIf(predicate: (T) → Boolean): T? {**

    ㄴ 람다식이 true면 결과값 반환

- **public inline fun <T> T.takeUnless(predicate: (T) → Boolean): T? {**

    ㄴ 람다식이 false면 결과값 반환. 

둘다 filter 고차함수랑 유사한 것 같다

```kotlin
val user = "KaSha" 
val str = "Sha" 

var index = 0 
index = user.indexOf(str).takeIf { it >= 0 } ?: -1 
index = user.indexOf(str).takeUnless { it < 0 } ?: -1
```

- **public inline fun repeat(times: Int, action: (Int) → Unit) {**

    ㄴ 내부적으로 for문을 사용하므로 일반 반복문과 흡사하지만 함수로 반복문을 사용하는 형태

    ㄴ index를 굳이 사용하지 않아도 될 때 사용. times : 순수 반복 횟수

```kotlin
repeat(5){ println(it) }
```

### **TDD**

- test를 먼저 작성한다
- TDD를 준수하면 개발 속도가 처음엔 늦을 수 있으나 버그 확률이 적다
- 테스트가 통과해야 이후 코드 작성 + 리팩토링 가능

### **패키지 관리**

- 구조 파악 + 변경사항 적용이 용이하다

여러 방식이 있지만 우선 이렇게 해보자

**com.example.appname**

- **activity**

    ㄴ액티비티 들을 모두 모아 둠

- **fragment**

    ㄴ프래그먼트 들을 모두 모아 둠

- **adapter**

    ㄴAdapter와 관련한 모든 class 모음

- **util**

    ㄴUtil과 관련한 class 모음

**utility class**

여러 클래스에서 공통적으로 사용되는 메서드를 효율적으로 관리하기위한 클래스