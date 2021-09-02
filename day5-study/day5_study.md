# day5-study

### **navigation graph - fragment 상태 저장**

action태그에서 app:destination을 사용하면 새로운 객체를 생성하게되서 이전 정보를 저장 불가

navigation graph는 기본적으로 replace로 되어있다(onCreate부터 다시 시작)

따라서 이 show/hide로 바꾸고 싶으면 navigation자체를 커스텀해야한다

[https://hide1202.github.io/android/bottom-navigator/](https://hide1202.github.io/android/bottom-navigator/)

~~따라서 한번 객체 생성했었다하면 destination대신에 popUp사용하면된다~~

주로 destination과 popUpTo는 같이사용하는데

destination은 해당 fragment로 이동

popUpTo는 fragment stack을 현재 popUpTo까지 pop시키고

popUpToInclusive는 현재 popUpTo fragment까지 pop시킬지를 true, false로 지정하는거다

```kotlin
<fragment
    android:id="@+id/setting"
    android:name="com.example.fruitcardgame.Setting"
    android:label="fragment_setting"
    tools:layout="@layout/fragment_setting" >
    <action
        android:id="@+id/action_setting_to_web"
        app:destination="@id/web"  //현재 setting -> web으로 이동
        app:popUpTo="@id/setting"  //setting까지 pop시키고
        app:popUpToInclusive="true" />  //현재 popUpTo까지 pop시킨다
    <action
        android:id="@+id/action_setting_to_playGame"
        app:destination="@id/playGame"  //현재 setting -> playGame이동
        app:popUpTo="@id/setting"  //setting이 맨위에 올 때 까지 pop시키고
        app:popUpToInclusive="true" /> //맨 위인 setting도 pop시킨다
```

### **단위테스트**

단위테스트할때 TestCase()를 자동으로 상속하는데

이걸 상속하면 @Before, @Test 어노테이션 상속하지 않아도된다..

@Before쓰려면 setUp함수를 override해서 작성하면되고 

@Test쓰려면 함수이름앞에 test를붙여서 이름을 만들면되고

@After쓰려먼 tearDown함수를 override해서 작성하면된다