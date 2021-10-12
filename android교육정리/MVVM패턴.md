# MVVM패턴

## View
- UI 그리는 역할
- 사용자의 action을 ViewModel한테 알리는 역할

## ViewModel
- View에 표시할 데이터 소유
- view가 reactive하게 업데이트 되기를 의도

## Model
- 데이터 관리

## MVP와 MVVM 구별점
### ViewModel
- View에 대한 참조를 가지면 안된다
- 데이터를 제공하기만 하고, 소비에 관여하면 안된다
- 여러화면에서 동일한 데이터가 필요하다면, 여러 View가 같은 ViewModel에 데이터 요청 가능!! (view이랑 ViewModel이랑 다:1 매핑)
- Presenter는 view랑 1:1 매핑!!

# ViewModel
- MVVM의 ViewModel은 AAC(Android Architecture Component) ViewModel없이도 구현가능
- ViewModel은 데이터의 변경을 Viewrk observe할 수 있도록 노출한다 (view는 ViewModel로부터 받은 이벤트에 따라 UI갱신)
- 사용자 액션 이벤트에 따라 필요한 데이터 변경 처리
# AAC ViewModel
- 앱의 UI data를 저장하고 관리할 수 있게 설계된 Abstrac class
- MVVM위해 만든것 x
- configuration이 변경되어도 data를 유지한다
## 장점
- onSavedInstanceState와 다르게 size제한이 없다
- 메모리에 data 저장하므로, disk/네트워크 data보다 읽는 속도가 빠르다

### AAC ViewModel의 Lifecycle
![](https://i.imgur.com/aVH98zs.png)

## 2way binding
### User Input
- 사용자 action이 변경된 것을 알리면 ViewModel이 그에따른 데이터를 갱신 (1 way)
- ViewModel이 소유한 상태와 Data Binding으로 연결됨
### View Output 
- ViewModel로부터 받은 이벤트에 따라 UI갱신 (2 way)
- call back interface
- library: RxJava, LiveData + Data Binding
![](https://i.imgur.com/3hnygsZ.png)

# App Architecture Guide
- MVC, MVP, MVVM모두 가장 부각되는 목적이 있다. 누가 더 좋다 이런것 없다.
- 모두 관심사 분리를 위함(UI구현, business logic처리를 위한 데이터 핸들링, Repository)
![](https://i.imgur.com/NORP3TZ.png)

### MVP, MVVM 주의사항
- MVP에서 Presenter는 android 패키지가 포함되지 않아야한다
- MVVM에서 ViewModel은 android패키지 중 lifecycle은 포함되도 된다. 

