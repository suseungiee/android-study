# day11

### 안드로이드 이벤트
ex) 입력 이벤트, 이벤트 큐, 이벤트 처리
![](https://i.imgur.com/kniEW25.png)

### Event Listener등록
- 익명 클래스 방식 (조금 더 선호)
- 리스너 구현

### 이벤트 소비 - true, false있는 리스너
- 정상적으로 소비를 했다 == 다음 순서의 리스너에게 전달 x == true
- 정상적으로 소비 안했다 == 다음 순서의 리스너에게 전달 == false
ex) onTouch -> onClick의 경우 클릭하면 onTouch, onClick순서대로 진행되는데 true하면 onClick수행되는데 false하면 onClick수행안된다

### MotionEvent
- 터치 위치
- 액션 타입 식별
- 단일 터치 (ACTION_down -> move -> up)
- 다중 터치 (ACTION_pointer_down -> move -> pointer_up)
ㄴ 여기서 pointer란 터치 event한 지점을 의미

### actionMasked -> 수행된 event 액션값 확인위한 변수/ event action대신에 사용 가능

## Custom View생성

### 언제 구현하는가? 
- Android가 제공하는 View, ViewGroup을 이용해서는 UI요구사항을 만족시킬 수 없을 때
- 더 정밀한 제어가 필요한 경우(사용자 입력에 따라 제어 필요할 때)
- 여러 화면에서 사용되는 경우

### 절차
1. super class선택하고, sub class만든다
    - 단순 커스텀 뷰 : 자식뷰 없이 단순한 구조를 갖는 경우
    - 복합 커스텀 뷰 : 여러 자식 뷰로 구성해야 하는 구조를 갖는 경우

2. super class의 함수를 override해서 커스터마이징한다

### 단순 Custom View (기능에 따라 특정 view를 상속받아서 구현)
- 상속받을 떄는 해당 view 생명주기 고려해야한다
< 기본 view 주요 생명주기>
![](https://i.imgur.com/Kg8A8fo.png)

- onDraw에서는 최대한 작은 작업을 처리해야한다

### Canvas
- 2차원사의 그림을 그릴수있는 surface
- Paint객체로 style, color설정 가능
- 커스텀뷰를 생성하는 일반적인 패턴 + onDraw, onSizeChanged override

### 복합 Custom View (Layout의 subclass만들기)
- layout을 상속받아 구현
- 최소 조건(Context, AttributeSet를 생성자로 제공해야한다)

## Layout Inspector로 계층 구조 분석
- 중첩된 Layout객체는 Layout렌더링 비용을 추가합니다