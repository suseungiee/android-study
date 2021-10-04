# UI상태 유지하기
### UI상태가 유실되는 상황
1. Process가 종료될 때
    - homebutton 누르거나, backbutton 누를 때
3. Configuration Change
    - device의 주요 변경사항이 발생하면 Activity를 재구성
    - ex) 언어 변경, device 회전
## Activity상태 저장(stashed 상태 추가)
![](https://i.imgur.com/Us9pYjA.png)

### Activity Lifecycle callback 호출 순서
onPause
↓
onStop
↓
onSaveInstanceState(==SIS)
↓
onDestroy
↓
onCreate

여기서 onSaveInstanceState와 onCreate의 인자가 같다. 따라서 값 저장하면 onCreate에서 값을 불러올 수 있다

### Fragment Lifecycle callback 호출순서
![](https://i.imgur.com/EN7rmpi.png)
보라색이 fragment(activity에 fragment attach되므로)
- fragment와 activity에서 UI나눠서 그리고있으면 둘다 복원해줘야한다

### Activity가 Stopped상태가 될 때
- ex) 회전 (activity 재생성된다. log찍어보면 instance값 다르다)

### Activity가 Restore될 떄
- data bundle에서 Activity를 직전과 동일한 상태로 유지하는 데 필요한 정보를 얻는다. -> OS가 복원하는 데 필요한 data를 찾을 수 있어야 한다

### Bundle에 데이터를 저장하는 처리가 필요하다.
- 데이터는 자동으로 Bundle에 저장되지 않는다!!

### onSaveInstanceState
- Android OS가 app을 종료시키는 경우, onSaveInstanceState callback에서 데이터를 저장할 수 있다.
- onSaveInstance를 override하고 super method 생략하면 안된다!! 생략하면 데이터 저장 안된다.

![](https://i.imgur.com/qM0mAj3.png)

## 한계
- Bundle에 50KB이하의 데이터를 담는 것을 권장한다.
- 너무 많은 데이터를 저장하면 exception을 발생시킨다
- 단순한 primitive type, String과 같은 작은 객체를 저장할 때만 사용한다.
- Serialization/ Deserialization이 필요한 길이가 긴 복잡한 객체를 저장하면 안된다!!
- Bitmap과 같은 대용량 데이터를 저장하면 안된다!!

## ViewModel
- 앱의 UI data를 저장하고 관리할 수 있게 설계된 Abstract class.
- MVVM의 VM과 상관없다
### 장점
1. onSaveInstanceState와 다르게 size 제한이 없다.
2. 메모리에 data를 저장하므로, dist/네트워크 data보다 읽는 속도가 빠르다. 
ㄴ ViewModel Lifecycle떄문

### 관심사의 분리
![](https://i.imgur.com/6BWumGj.png)
#### UI Controller
- User interface와 관련된 task만 처리한다
    - display data
    - capture user/OS events
#### ViewModel
- UI에 필요한 모든 데이터를 담아두고, 화면에 어떤 데이터를 display할지 준비한다

즉 ViewModel은 특정 화면에서 필요한 데이터를 형식화 한다 + 모델 객체의 역할 수행
즉 presenter + Model의 기능
-> MVP를 쓰면 데이터의 흐름 방향이 직관적이다
### ViewModel의 LifeCycle
![](https://i.imgur.com/jMTzJtk.png)

- OnSaveInstanceState는 backStack에 bundle 정보를 저장. -> 앱이 백그라운드로 가더라도 OS의 manager한테 저장되므로 데이터 불러올 수 있다
## ViewModel한계
- OS가 앱의 Process를 종료시키면 ViewModel은 메모리에 존재하는 것이므로 소멸됨
- ViewModel은 UI Controller(Activity, Fragment)의 reference를 갖지 않으므로 변경된 데이터를 전달할 방법이 없다.
- ViewModel의 데이터가 변경되면 UI update가 되어야하는데, 이를 강제할 수 없다.

## 해결책
- ViewModel, onSaveInstanceState, Local Database를 조합해 UI상태를 복원해야한다 
![](https://i.imgur.com/TnUpsZb.png)

## Android Debug Bridge(ADB)
- command line tool
- shared Preference로 앱 계속 지웠다 깔았다 해주는거 편리하게 해주는 tool

- Nothing : 모든 에러타입의 최상위
- 즉 에러상태임을 의미하는 타입
- ex) TODO : Nothing타입