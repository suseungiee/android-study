# MVP패턴

## 관심사의 분리
- 초기에는 물리적인 경계를 기준으로 Layer를 구분
- 각 Layer는 역할과 책임이 다르다
- 다른 Layer가 하는 일에는 관심 x
- 이렇게 분리된 class로 조합해서 프로그래밍 == OOP

## UI구현을 위해 Activity/ Fragment가 처리하던 UI로직
- Lifecycle
- Screen navigation
- Runtime permission
- Fragment생성 후 fragmentManager에추가
- Dialog띄우기
이러한 UI 로직을 분리시킬 수 없을까?

## UI를 분리할까?
- 대부분의 경우 UI는 변경 요청이 많다. 데이터구조(Model)는 변경요청 많이 x
    - screen size, 색상 변경, UI변경 ...
- 읽기 어려운 코드가 된다
    - 디테일한 UI구현 코드는 장황하고 지저분할 수 있다
- 테스트가 가장 어렵다

## MVP패턴
### view : 사용자 입력 감지, UI변경
### Presenter : 사용자 액션 처리, 상태변경 요청, Model변화 수신, Model전달
### Model : 상태변경
![](https://i.imgur.com/dWRmKrM.png)


- Model: data layer, 데이터 원형과 그 상태를 관리
- View: UI표시, 사용자 액션 감지(Activity, Fragment)
- Presenter: Model, View를 호출하여 UI를 표시하기 위해 필요한 작업 실행. 따라서 Model, View의 참조를 가지고 있어야한다

![](https://i.imgur.com/3Iwsp9Q.png)

- view에서만 com.android~이런 android framework에서 제공하는 import가 있어야한다
- Presenter, Model에 com.android import되면 잘못된 설계
- context, 이런거 필요하면 감싸서 보내야한다
```kotlin=
fun func(context: Context){
    context.SharedPreference
}
이거보단
fun func(prefs: SharedPreferences){
    prefs.getString()
}
뭐 이런식으로 감싸야한다. -> 그래야 나중에 testing 쉽다
```

## View와 Model의 바인딩을 끊어내고, 통신하려면?
- 인터페이스를 사용하면된다

### mvp패턴 장점
- 데이터의 흐름을 추적하기 쉽다

## Presenter의 역할
### 하면 안되는 것
- Android framework 클래스에 대한 참조 x
    - context, view, intent ...
### 해야 하는 것
- View에 대한 직접적인 참조 x but view와 통신해야한다
- interface 타입을 참조

## MVP패턴 기능 구현 요령 (매 기능별 해당 순서로 작성)
1. Contract 먼저 작성(Contract가 view, Presenter간의 설계도라고 생각할 수 있다. view, Presenter간의 통신 위해 Contract interface로 묶은 형태)
    - contract 기능 한개씩 구현하기. 한번에 모든 기능 추가 x
2. Presenter 작성(콜백함수 사용 or coroutine suspend함수)
    - Presenter는 Model을 알아도 된다. 즉 참조 가져도 된다.
3. Model 작성
    - Model은 Presenter를 알면 안된다(알게되면 순환참조가 발생해서 예상치 못한 에러 발생가능!!)
    - Model의 변화를 수신하기 위해 callback을 사용하거나, Rx library사용


## contract에 Model은 추가하지 않는다
- view, Model의 분리하기 위해서
- Model 원형과 Model관리하는 객체는 분리하기!!