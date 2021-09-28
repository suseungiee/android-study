# Generic programming

## 제네릭 프로그래밍
- 타입을 추상화 시켜서 모든 타입이 메서드를 쓸 수 있게 하는 프로그래밍
- 타입을 구체적으로 명시 x
- 타입 추론을 언어스택에서 적용시켜준다
ex) list, map, set ..etc

## 제네릭 클래스
- 타입 파라미터를 넣은 <>를 클래스/인터페이스 이름 뒤에 붙인다


## 타입 파라미터 제약 - 상한(upper bound)
- 제네릭 타입을 인스턴스화할 떄 사용하는 타입 인자는 상한 타입이거나, 그 상한 타입의 하위타입이어야 함
```
fun <T: Comparable<T>> max(first: T, second: T): T {
    return if (first > second) first else second
}
```
max("cat", "pengsoo")가 가능한 이유는 String이 Comparable을 구현한 class이기 떄문

## 다중 범위
- 상한을 여러개 선언하고 싶은 경우 
```kotlin
fun <T> maxSerializable(first: T, second: T): T where T : Comparable<T>, T : Serializable {
    return if (first > second) first else second
}
```
조건이 여러개일때는 where절을 사용한다
```kotlin
class Year(val value: Int) : Comparable<Year> {
    override fun compareTo(other: Year): Int = this.value.compareTo(other.value)
}

maxSerializable(Year(1969)) // 컴파일 에러난다 상한 중 1개만 구현했으므로
```

## 타입 소거
- 타입 인자 정보는 런타임에 사라진다
- 인스턴스를 생성할 때, 타입 인자 정보를 유지하지 않음

List<(String)> 객체를 만들었더라도 실행시점에는 원소 타입을 알 수 없음
ex) val arr = mutableListOf(1, 2, 3)

arr.add("hello") 이러면 런타임에서는 arr의 원소타입이 int인지 모르므로 hello string을 add가 가능한 것 처럼 보일 수 있다. but 이런에러는 컴파일러가 다 잡아주므로 에러발생하지 않는다.


```kotlin
val strings = listOf("a", "b")
val ints = listOf(1, 2, 3)
```

컴파일러는 두 리스트를 서로 다른 타입으로 인식  but 런타임에는 같은 타입의 객체(리스트)로 인식
but 컴파일러가 타입 인자 추정하고 올바른 타입을 보장하므로 에러발생하지 않는다
- but inline, reified를 붙이면 런타임시점에 타입 정보가 사라지지 않는다.

```kotlin
fun <T> isA(value: Any) = value is T
//에러난다
inline fun <reified T> isA(value: Any) = value is T
//이렇게하면 에러 안난다
```

## inline에서 reified붙이면 타입정보 살아남는 이유는?
- inline을 붙이면 콜스택이 생성되지않는다
수행해야할 함수의 body를 복사해왔기 때문 -> 앞뒤 추정으로 타입정보가 존재하게된다

- inline은 함수가 lambda가 있을때만 사용할 수 있는데, 이를 구별하기위해 reified 붙여준다

## 제네릭으로 만들 수 있는 타입
- List<(String)>, List<(String?)>, List<<(List<(Int)>)>> 등 무수히 많은 조합 가능
- 하지만 그 안에서 subtype, supertype이 존재할 수 있다
```kotlin
fun add(number: Int?){}
fun minus(number: Int) {}
fun main(){
    val nonNullNumber: Int = 0
    val nullableNumber: Int? = 1
    add(nonNullNumber)
    add(nullableNumber)
    minus(nonNullNumber)
    minus(nullableNumber) // 에러난다
    즉 nullable이 supertype이다
}
```

## 공변성 - 하위 타입 관계 유지
- 모든 숫자의 superType은 Number다

![](https://i.imgur.com/lVLWGeU.png)


관게가 유지되는 이유 = 리스트가 out키워드로 공변성을 가지고 있어서
- 하위타입에서 upcasting해서 반환할 수 있다

## 공변 규칙 활용
- 클래스 외부의 사용자가 클래스를 잘못 사용하는일을 막기 위함
- private 클래스, 메서드는 공변규칙 활용할 수 없다

## MutableList는 무공변, List 공변성 가짐. 어떤 차이가 있을까? (hint : 타입 안정성, subtype)
  
```kotlin=
var intList = listOf(1, 2, 3)
var numberList: List<Number> = listOf()
numberList = intList
intList = numberList

var intMutableList = mutableListOf<Int>()
var numberMutableList: MutableList<Number> = mutableListOf()
intMutableList = numberMutableList
numberMutableList = intMutableList  //에러난다

mutableList에서는 <>안에 들어오는 타입의 상하관계를 알 수 없으므로 

```
## 반공변성 - 뒤집힌 하위 타입 관계
![](https://i.imgur.com/pQKhVLV.png)
## 반공변 규칙 활용
- 제네릭 타입 인자를 함수 파라미터 타입 위치에서 사용 가능
ex) Comparable쓸 때 
![](https://i.imgur.com/PQhwgUn.png)
