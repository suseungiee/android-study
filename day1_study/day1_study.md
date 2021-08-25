# day1_study

### **day1**

### **activity와 fragment 차이?**

activity는 1개밖에 못 띄우지만 fragment는 여러개 띄울 수 있다

activity.xml에서 frameLayout부분에 fragment를 위치시키기 떄문

따라서 사용자는 activity전환 없이 더 편리하게 view를 변경할 수 있다.

fragment도 activity와 마찬가지로 생명주기 가진다.

즉 fragment는 activity에서 UI정의하기 위한 더 작은 단위

**단, activity는 back stack에 자동으로 저장되지만 fragment는 수동으로 저장해야한다!!**

한개 버튼에 리스너 여러개 가능? 가능하다

한개 리스너에 여러개 위젯 가능? 가능한 것 같다.

Activity가 VIew.onClickListener()상속하고 리스너 등록할때 this 쓰기때문에

같은 this이므로 1개 리스너에 여러개 위젯 가능

### **Activity 생명주기**

![Untitled](day1_study%20a36f749273e84f1c827b70b51b796a0f/Untitled.png)

- onCreate()
    - Activity가 처음 생성될 때 호출
    - savedInstanceState를 통해 이전 상태를 불러오거나, Activity에 필요한 구성 요소들을 초기화하며, UI 레이아웃을 정의
    - 저장되었던 데이터에 의해 화면을 이전상태로 복원
- onRestart()
    - Activity가 중단되었다가 다시 시작되기 전에 호출
- onStart()
    - Activity가 화면에 보이기 바로 직전에 호출
- onResume()
    - Activity가 사용자와 상호작용을 하기 바로 직전에 호출
    - 이 단계 이후, 우리가 사용 가능한 Activity가 실행 되게 된다.
- onPause()
    - Activity가 다른 Activity로 가려져 화면에서 사라지기 시작할 때 호출
    - 사용자가 필요하지 않는 리소스 등을 해제 할 수 있다.
- onStop()
    - Activity가 종료되거나, 다른 Activity가 화면을 가릴 졌을 경우 등의 상황에 호출
    - Activity Object가 메모리에만 남아있게 된다. 여기서는 자동으로 onSaveInstanceState()가 실행되어, 데이터를 Key-value 형태로 값을 저장하여 onCreate()에서 다시 복원을 하여 데이터만 있다면 화면을 다시 불러올 수 있다.
- onDestroy()
    - Activity가 완전히 소멸될 때 호출

### **Fragment생명주기**

![Untitled](day1_study%20a36f749273e84f1c827b70b51b796a0f/Untitled%201.png)

- onAttach()
    - Fragment가 Activity에 연결될 때 호출
- onCreate()
    - Fragment가 처음 생성될 때 호출
    - Activity와 마찬가지로 리소스들을 초기화
    - 생성시 넘겨받은 값들이 있다면 여기서 변수에 넣어주면 된다.
- onCreateView()
    - Fragment의 layout을 inflate하여 View 객체를 얻을 수 있음
- onActivityCreated()
    - Activity에서 Fragment를 모두 생성된 후에 호출
    - UI에 관련된 View를 변경하는 작업이 가능
- onStart()
    - Activity와 마찬가지로 Fragment가 화면에 보이기 바로 직전에 호출
- onResume()
    - 마찬가지로, Fragment가 사용자와 상호작용을 하기 바로 직전에 호출
- onPause()
    - 다른 Fragment가 화면을 가리게 되어 기존 Fragment의 화면이 사라지게 되는 시점에서 호출, 기존 레이아웃은 백스택으로 들어감.
- onStop()
    - Fragment가 화면에서 더이상 보여지지 않게 되었을 때 호출
    - 기능을 완전히 상실함을 의미
- onDestroyView()
    - Fragment의 View들의 리소스를 해제되는 구간
- onDestroy(
    - Fragment가 완전히 소멸될 때 호출
- onDetach()
    - Fragment가 Activity와 연결이 끊기기 직전에 호출

[https://hwanine.github.io/android/Android-Fragment-Stack/](https://hwanine.github.io/android/Android-Fragment-Stack/)

전반적으로 **onCreate → onStart → onResume → onPause → onStop →onDestroy**과정이다

fragment는 oncreate이전에 onAttach, onDestroy이전에 onDetach해주고

onCreate다음에 onCreateView에서 layout inflate

onDestroy이전에 onDestroyView에서 resoure 해제.

### **inflate**

xml에 표기된 레이아웃들을 메모리에 객체화 시키는 것

즉 xml코드를 객체화해서 코드에서 사용하게하기 위함

setContentView(R.layout.activity_main)이게 activity_main.xml의 코드를 객체화시키는 과정

### **Text Style 커스텀**

```kotlin
<TextView
    style = "@style/TEXTVIEW_STYLE"
    android:id="@+id/tv_nickname"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:layout_marginTop="80dp"
    android:text="별명"
    android:layout_marginStart="20dp"
    android:textSize="30dp"
    app:layout_constraintEnd_toStartOf="@id/et_nickname"
    app:layout_constraintStart_toStartOf="parent"
    app:layout_constraintTop_toTopOf="parent" />

activity_main.xml
```

사용한 것만 봐도 이정도다. 여기서 textSize를 직접 20dp로 주어도되지만

textSize같은경우 중복이 많이 될 경우 따로 resource로 빼는게 좋다고하셔서

res>values>TextStyle.xml로 따로 빼보았다.

```kotlin
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <style name = "TEXTVIEW_STYLE">
        <item name = "android:textSize">20dp</item>
    </style>
    <style name = "BUTTON_STYLE">
        <item name = "android:textSize">20dp</item>
        <item name = "android:textStyle">bold</item>

    </style>
</resources>

TextStyle.xml
```

style이름을 설정하고 안에 item태그로 해당 속성(property)를 커스텀하게 정의하고

style = @style/TEXT_STYLE이렇게하면 정의한 item태그들이 반영되는 것 같다.

BUTTON_STYLE도 정의해주었다(textSize : 20dp + textStyle : bold)

### **Button 테두리**

```kotlin
<androidx.appcompat.widget.AppCompatButton
    style = "@style/BUTTON_STYLE"
    android:textColor="@color/purple"
    android:id="@+id/recommend_button"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:layout_marginTop="80dp"
    android:text="추천"
    app:layout_constraintStart_toEndOf="@id/et_nickname"
    app:layout_constraintEnd_toEndOf="parent"
    app:layout_constraintTop_toTopOf="parent"
    android:background="@drawable/roundbox"/>

activity_main.xml
```

우선 버튼에 테두리를 만들기위해 button에 테두리있는것을 커스텀하게 정의해서 사용하였다

res>drawable>roundbox.xml에 따로 정의해주었으며 해당 코드는 다음과 같다

```
<shape xmlns:android="http://schemas.android.com/apk/res/android"
    android:shape="rectangle">
    <solid android:color = "@color/white"/>
    <corners android:radius="10dp"/>
    <stroke android:width="1dp"
        android:color="@color/gray"/>

</shape>
```

shape태그안에 shape : rectangle로 해주었으며

solid : 내부 색상

corners : 모서리 둥근정도

stroke : 테두리. 다음과 같이 태그를 추가해주었다.

근데 일반 Button으로하면 solid부분만 적용되지않아 보라색버튼이 되어서 Button자체를 AppCompatButton으로 바꾸어주었다.

### **bottom navigation**

res>menu폴더만들어서>menu.xml 만들고

내부에 다음처럼 id, title, icon을 작성해주면된다. 나는 fragment보여주기위해 id를 fragment로 했다

```kotlin
<item
    android:id="@+id/item_fragment1"
    android:enabled="true"
    android:title="아이템1"
    android:icon="@android:drawable/ic_menu_call"
    />

menu.xml
```