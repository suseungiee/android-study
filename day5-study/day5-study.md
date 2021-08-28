# day5-study

### **navigation graph - fragment 상태 저장**

action태그에서 app:destination을 사용하면 새로운 객체를 생성하게되서 이전 정보를 저장 불가

따라서 한번 객체 생성했었다하면 destination대신에 popUp사용하면된다

```kotlin
app:popUpTo="@id/fragment1"  //이 fragment나올떄까지 pop
app:popUpToInclusive="false"  //false하면 이 fragment가 보이게, true면 이 fragment이전까지
```

즉 뭐 action을 2개씩 만들어주면 되지 않을까싶다.

```kotlin
<action
    android:id="@+id/action_web_to_fragment1"
    app:destination="@id/fragment1" />
<action
    android:id="@+id/action_web_to_fragment1_again"
    app:popUpTo="@id/fragment1"
    app:popUpToInclusive="false" />
```

### **단위테스트**

단위테스트할때 TestCase()를 자동으로 상속하는데

이걸 상속하면 @Before, @Test 어노테이션 상속하지 않아도된다..

@Before쓰려면 setUp함수를 override해서 작성하면되고 

@Test쓰려면 함수이름앞에 test를붙여서 이름을 만들면되고

@After쓰려먼 tearDown함수를 override해서 작성하면된다