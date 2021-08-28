# day2

### **Activity**

- Android앱 만드는 주요 component중 1개
- 사용자와 상호작용 하기위한 entry point
- 화면을 통해 앱이 상호작용 할 수 있도록 기능지원 → layout inflate로
- UI를 포함한 하나의 screen의미
- 주로 AppCompatActivity를 상속한다

### **AppCompatActivity**

- 오래된 버전에 대한 호환성 + 최신버전 자동으로 호환시켜주는 activity

### **layout inflate**

- xml코드가 source code에서 다룰 수 있는 객체화 되는 것
- resource id를 인자로 받아서 해당 위젯객체의 참조 얻을 수 있다
- xml에 정의한 속성(property)에 맞게 인스턴스 생성된다

ex) android:text="ddf" : setText()호출한것이라 생각

### **Activity Stack == Back Stack**

안드로이드 시스템은 실행중인 앱의 activity stack을 관리한다

### **Activity상태와 LifeCycle**

Focus : activity가 사용자와 상호작용할 수 있는 상태

Foreground : activity가 화면에 보이는 경우 ↔ background(화면에 안보임)

**onCreate()**

- UI준비 - app을 처음 실행할 때 1번만 호출된다
- 처리하는 작업 : 리스너 등록, widget inflate, 외부의 모델 데이터 연결

**onStart()**

- UI변경 시작 - 다중 창 모드에서 앱이 선택되는 경우
- 처리하는 작업 : 애니메이션, 데이터 갱신 시작

**onResume()**

- 오버뷰 화면에서 앱이 다시 선택되는 경우(activity가 focus얻음)
- 오직 1개의 activity만 Resumed상태!

onCreate → onStart→onResume까지는 빠르게 진행된다

**onPause()**

- 다중창 모르도 다른 앱 사용하는 경우(activity가 focus 잃음)
- diaglog로 화면이 덮일 때

**onStop()**

- 홈버튼 눌러 다른앱 사용할 때
- onStop이후의 activity는 종료 대상이된다(메모리 부족하면 자동삭제)
- 처리하는 작업 : 애니메이션 정지, 데이터 갱신 중단, 데이터 저장

**onDestroy()**

- back버튼으로 앱 완전히 종료될 때
- 오버뷰 화면에서 해당 앱 목록에서 제거할 때
- 처리하는 작업 : 해제되지 않은 리소스 모두 해제

**onRestart()**

- 종료해도 오버뷰에는 남아있다 → 다시 클릭하면 activity created상태 된다

![Untitled](./day2/Untitled)

![Untitled](day2%2095274db282db41d597b76c8189c233ca/Untitled.png)

하늘색은 콜백함수, 파란색은 activity 상태

주로 앱은 Resumed, Paused상태가 자주 발생한다

ㄴ 오래걸리는 작업은 여기에 추가하면 안된다

생명주기 함수들은 모두 콜백함수다

주로 처리하는 작업에 맞게 코드 배치하는게 좋다 → 안정성 확보위함

### **Callback함수**

- 다른 함수의 인자로 전달되는 함수
- 특정 이벤트가 발생한 후 호출되는 함수 (생명주기 함수는 여기에 해당)

### **Fragment**

- UI 모듈화, 재사용 하고 싶을 때 사용 → UI 조립가능
- 크기 조절 가능 (activity는 항상 화면 전체 차지한다)

    ㄴ 여러 fragment 1개 activity에 결합가능

- 생명주기 가진다(모든 UI는 상태를 가진다 = 생명주기 가진다)
- 사용자 이벤트 처리가능
- activity처럼 독립적으로 존재 x → 호스트 액티비티가 필요

    ㄴ fragment의 계층구조는 호스트의 계층구조의 한 부분이다

    ㄴ activity는 fragment 넣을 container가지며 여기에 fragment view가 inflate된다

    ㄴ fragmentManager가 호스트 activity와 연결해준다

### **FragmentManager**

- fragment의 뷰를 activity view 계층에 추가, 삭제, 교체해준다
- fragmentTransaction으로 묶어서 fragment 전환해준다

![Untitled1](./day2/Untitled1)

![Untitled](day2%2095274db282db41d597b76c8189c233ca/Untitled%201.png)

activityManager는 존재하지만 fragmentManager처럼 코드에 드러나지 않는다

fragmentManager : supportFragmentManager()이렇게 존재한다

### **FragmentTransaction**

- fragment리스트에 fragment추가, 삭제, 변경하는데 사용
- fragmentManager가 fragment 인스턴스화 → transcation으로 backstack에 추가
- fragment back stack은 activity back stack과 유사 but 전체 stack은 activity 내에 포함

즉 activity stack에 fragment stack추가된 구조. fragment stack 다 비어질 때까지 activity stack pop안된다 

### **Transaction**

변경(추가, 삭제, 교체)을 요청하기위한 작업요청 의 단위

### **Fragment LifeCycle**

**onCreate()**

- fragment instance 구성

    ㄴ fragment구성에 필요한 것 초기화

- 여기서는 xml inflate하면 안된다

**onCreateView()**

- 여기서 xml inflate해야한다
- inflate한 view를 호스트 activity로 반환
- 항상 view의 component는 루트view가 존재한다

    ㄴ inflate한 view는 호스트 activity의 container에 추가되어야하므로 부모(루트)view에 attach되면 안된다

**onViewCreated()**

- view의 초기값, 리스너 설정을 해준다

**onStart()**

- 데이터가 설정될 떄 작동하는 리스너 설정

    ㄴ 디바이스  회전 등 할 때 데이터 저장해줘야한다 →  회전 후 데이터 복원

fragmentManager가 fragment 관리하므로 호스트 activity, activityManager는 fragement의 정보 잘 모른다. 

그 외엔 activity와 거의 대응된다

fragment는 여러개 띄워져있어도 onResume 여러개 될 수 있다

### **Jetpack Navigation**

- back stack관리 자동으로 해준다 + 화면이동을 편하게 할 수 있다
- 시각화된 navigation graph 지원

### **fragment, activity 구분기준**

flow의 진입만 activity, 이후로는 fragment로 처리

### **ViewBinding**

- findViewById의 중복 방지하기위해
- layout component 객체화 시키지않고 바로 사용 가능

    ㄴ 모듈별로 사용 설정하면 각 xml파일에 대해 binding class 자동생성

    ㄴ binding class는 id가지고 있는 모든 뷰에 대한 직접 참조를 포함한다

- id 존재하지 않는 뷰에 대해서는 클래스에 참조 존재하지 않는다
- findViewById의 없는 아이디로 참조(null point exception) 불가. null safety
- type safety : 항상 뷰 타입이 일치한다(Class Cast Exception)

ex) title, subtext, button이라는 id 있으면 이렇게 사용가능

```kotlin
override fun onCreate(savedInstanceState: Bundle?) { 
	super.onCreate(savedInstanceState) 
	val binding = ActivityAwesomeBinding.inflate(layoutInflater)
	binding.title.text = "Hello"
	binding.subtext.text = "Concise, safe code" 
	binding.button.setOnClickListener {/* ... */ } 
	setContentView(binding.root) 
}

https://tourspace.tistory.com/314
```

### **DataBinding??**

- viewBinding보다 컴파일이 빠르다

사용방법

[https://salix97.tistory.com/243](https://salix97.tistory.com/243)

즉 뭐 findViewById는 약간 무겁고 null point exception, class cast exception발생가능