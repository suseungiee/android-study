# day3_study

**유니코드 사용방법**

UTF-8같은경우는 1바이트이기때문에

var char = '\uAC00'이렇게하면 된다

그런데 UTF-16같은경우는 2바이트이기때문에 위와같은 방식으론 안된다.

대신에 string으로 var str = "\uDED0\uDBC0"이렇게하면된다

잘 몰랐는데 유니코드 그냥 복사해서 print에 넣어주니깐 자동으로 저렇게되었다.

enum class장점

- 직관적이라서 0이 apple, 1이 orange 이런식으로 따로 정의할 필요없다.
- 클래스이름이 Suits라면 Suits.values()[ind]이렇게 인덱스로 접근가능하다

### **JUnit**

- 자바용 단위테스트 도구
- @Test메서드 호출할 때마다 새로운 새로운 인스턴스 생성하여 독립적인 테스트 이루어짐
- JUnit4부터 어노테이션 지원
- 테스트결과 확인 이외에 최적화된 코드 유츄가능
- assert 메서드로 수행결과 판별가능

### **Unit Test(단위테스트)**

- 소스코드의 특정 모듈이 의도한대로 동작하는지 검증하는 절차
- 일일히 print해주는 번거로운 디버깅이 필요없다

**JUnit4에서 사용하는 annotation**

@before : @Test진행하기에 앞서 항상 먼저 실행

@after : @Test진행하고나서 항상 나중에 실행

@beforeClass : 단위테스트 클래스단위로 실행하는데 있어 맨처음 한번만 실행

@afterClass : 단위테스트 클래스단위로 실행하는데 있어 맨마지막 한번만 실행

@Test : 해당 메서드가 테스트 대상임을 알려준다

![Untitled](day3_study%2045fa194910ef48aaa3971e08b2fb2bf8/Untitled.png)

단위테스트는 assert문을 이용하여 (예상결과, 실제 수행결과) 판단

assert문의 종류로는 

**assertArrayEquals(a, b)** :  배열 A와 B가 일치함을 확인한다.

**assertEquals(a, b)** : 객체 A와 B가 같은 값을 가지는지 확인한다.

**assertEquals(a, b, c)** :  객체 A와 B가 값이 일치함을 확인한다.( a: 예상값, b:결과값, c: 오차범위)

**assertSame(a, b)** : 객체 A와 B가 같은 객체임을 확인한다.

**assertTrue(a)**: 조건 A가 참인지 확인한다.

**assertNotNull(a)** : 객채 A가 null이 아님을 확인한다.

주로 이정도 사용된다

[https://devuna.tistory.com/39](https://devuna.tistory.com/39)