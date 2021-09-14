## 디자인패턴
### 1. 목적에 따른 분류
#### Creational : 생성패턴, 객체의 생성에 관여
ex) SingleTon
#### Structural : 구조패턴, 클래스, 객체의 합성
ex) Adapter, Proxy
#### Behavioral : 행위패턴, 책임 분산방법, 클래스, 객체 상호작용 방법
ex) Observer

### 2. 범위에 따른 분류
#### 클래스 : 클래스와 서브 클래스 간의 관련성을 다루는 패턴
- 상속, 컴파일 타임에 결정
#### 객체 : 객체 관련성을 다루는 패턴
- 런타임에 변경 가능. 일부 상속 구현
![](https://i.imgur.com/PIIpARV.png)

## 디자인 패턴 활용
### 1. 적당한 객체 찾기
#### 객체(object) : 데이터와 그 데이터를 연산하는 프로시저를 함께 묶은 단위
#### 프로시저 : 메서드 또는 연산 / 정해진 절차에 따라 내부의 상태를 변경하는 루틴
즉 객체는 메세지를 받으면 연산을 수행한다
#### 객체지향 설계 : 캡슐화, 종속성, 유연성, 재사용성을 고려해 객체 분할
### 2. 객체 인터페이스의 명세
#### signature : 객체가 선언하는 연산의 이름, 매개변수, 반환값을 이르는 말. 즉 메서드를 의미
#### interface : 객체가 정의하는 연산의 모든 signature를 일컫는 말(즉 클래스안의 모든 메서드)
- 객체 인터페이스 : 객체가 처리할 수 있는 연산의 집합 
#### type : 특정 interface를 나타날 때 사용하는 이름
- subtype은 supertype의 interface를 상속하며 subtype은 supertype에 정의된 연산을 포함한다
#### 객체 지향과 interface
- 외부에서는 객체의 interface를 통해서만 처리 요청 가능
#### dynamic binding : 어떤 메시지를 처리할 객체를 런타임에 연결 짓는 것(상속이 유연하지 못한 이유 : 컴파일타임에 결정되므로)
- 동일한 interface를 갖는 다른 객체로 대체 가능 -> 다형성
## 즉 디자인 패턴은 interface에 정의해야하는 요소, 데이터를 규정한 모음

### 3. 객체 구현 방법
#### 클래스 : 객체의 내부상태, 연산에 대한 구현방법 정의
- 클래스 상속(subclassing) -> 정의된 객체의 구현을 공유

#### 인터페이스 : 그 객체가 응답할 수 있는 메시지의 집합
- 객체의 구현은 달라도, 인터페이스는 같을 수 있다
- 인터페이스 상속(subtyping) : 어떤 객체를 다른 객체 대신에 사용될 수 있도록 지정

#### 추상클래스를 상속하고 인터페이스를 공유하는 방법
- 사용자가 원하는 인터페이스를 객체가 만족하면, 사용자는 특정 객체 타입에 대해 알 필요가 없다
- 사용자는 구현 클래스를 알 필요가 없고, 인터페이스를 정의하는 추상 클래스만 알면 된다
- 클래스 사용할 떄 보다 더 유연하다
### 즉 구현 종속성이 사라진다, 구현이 아닌 인터페이스에 따라 프로그래밍한다

### 4. 재사용 가능한 구조
#### 상속
- 목적 : 기능의 재사용
- subclassing : 부모클래스에서 상속받아 클래스 구현 정의
- 화이트박스 재사용 : 상속을 받으면 부모클래스의 내부가 서브클래스에 공개된다
- 컴파일 시점에 정의된다 -> 런타임에 구현 변경 불가
- 부모 클래스 변경시 서브 클래스도 변경 필요 -> 덜 유연
- 단순하게 빨리 구현할 때 자주 사용

#### 객체 합성(object composition) 
- 여러 다른 객체를 합성해 새로운 객체 구성
- 다른 객체의 참조를 얻는 방식
- 블랙 박스 재사용 : 객체의 내부는 공개되지 않고 인터페이스를 통해서만 재사용된다
- 런타임에 정의된다 -> 변화된 부분 찾기 어렵다

#### 매개변수화된 타입 - 제네릭 generic
- 클래스가 사용하는 타입을 변경한다
- 확장성이 있다

## Adapter Pattern
- 구조패턴 : 더 큰 구조를 형성하기위해 클래스/객체를 합성하는 여러가지 패턴
- 목적 : 클래스의 인터페이스를 사용자가 기대하는 인터페이스로 변환
![](https://i.imgur.com/4W83crw.png)
클라이언트가 필요한 기능의 일부가 이미 구현된 Adaptee에 존재.
따라서 Adaptee의 기능의 일부를 Adapter를 이용해서 구현하고 이를 이용해 클라이언트의 요구사항을 만족시킨다
ex) recyclerView에서 view는 클라이언트에 해당
recyclerview.adapter에서 .adapter는 클라이언트가 요구하는 인터페이스에 해당. 이를 우리가 MyAdapter이렇게 구현하는게 Adapter
Adaptee는 데이터라고 생각하면 된다
![](https://i.imgur.com/jGAPXLf.png)


### Adapter Pattern in RecyclerView
#### Adapter interface
- RecyclerView가 화면에 data를 display할지 알 수 있도록 메서드 지원
- item갯수
- item을 draw + add/update/remove
- 어떤 view를 새로 생성하고, 재사용 해야하는지 결정
### 즉 Adapter는 RecyclerView를 사용해 View를 효율적으로 hold, recycle하기 위해 ViewHolder를 사용
#### ViewHolder : 데이터를 저장하는 뷰
#### Adapter : item을 화면에 그릴 수 있도록 변환
#### RecyclerView : adapter를 통해 ViewHolder를 생성/재사용해서 item 표시
- Adapter를 통해 몇개의 item을 그려야할 지 안다
- 화면 밖으로 스크롤된 ViewHolder를 재사용
- 재사용 : View는 유지하고 값만 재설정
- 이점 : 높은수준의 커스텀이 가능
- 목록을 그리는데 효율적
![](https://i.imgur.com/6PY22ea.png)

### RecyclerView
- itemView : RecyclerView의 자식 View
- 각 itemView는 하나의 row/column으로 나타내며, 자신만의 뷰 계층 구조를 가질 수 있다

### ViewHolder
- RecyclerView는 itemView가 ViewHolder인스턴스에 포함되어 있다고 간주
- View group이라 생각
#### ViewHolder는 itemView의 참조를 가짐
- RecyclerView는 View를 생성하지 않고, View를 참조하는 ViewHolder를 생성
![](https://i.imgur.com/zJjPgrU.png)

### ViewHoler는 누가 생성할까?
#### Adapter
- 필요한 ViewHoleder인스턴스 생성
- 모델 계층의 데이터를 ViewHolder와 바인딩
#### RecyclerView
- 새로운 ViewHolder 인스턴스 생성을 어댑터에게 요청
- 지정된 위치의 데이터 항목에 ViewHolder를 바인딩하도록 어댑터에 요청 
![](https://i.imgur.com/AkopP0g.png)
즉 RecyclerView는 혼자선 아무것도 못하고 RecyclerView.Adapter에게 모든 기능 위임한다
RecyclerView에 그려지는 데이터는 List<!DataType>이다
![](https://i.imgur.com/gxtYPT9.png)

### RecyclerView의 관심사
- View가 아니라, ViewHolder이다
- onCreateViewHolder: 새로운 ViewHolder 필요할 떄 호출 -> ViewHolder생성
- Adapter는 ViewHolder가 하는 일을 몰라야 한다
ㄴ onBindViewHolder에서 view set하면 안된다. ViewHolder에서 view set해야한다
- onBindViewHolder : 화면에서 ViewHolder가 스크롤을 벗어나 더이상 사용되지 않을 때, 호출되어 재사용된다
ㄴ 사용되지 않는 ViewHolder를 인자로 받고, 해당 position의 item을 반영하기 위해 ViewHolder갱신
ㄴ 기존 설정정보 바꾸기 위해 reset처리가 필요하다 
![](https://i.imgur.com/h7riZCp.png)

### 즉 Adapter Pattern은 이미 구현된 기능을 사용하고 싶은데 수정이 불가능하고 도입하고 싶은 부분이 다를때 사용한다

### RecyclerView in MVC
- Model : data
- Controller : Fragment, RecyclerViewAdapter, ViewModel(가져도 된다)
- View : RecyclerView

Adapter의 책임 : 데이터를 RecyclerView API와 연결
ㄴ 어떤 데이터를 보여주는가는 관심사가 아니다
ㄴ 데이터가 변경되는 경우, 다른 객체가 알려줘야 한다
![](https://i.imgur.com/wStMBUD.png)

ViewHolder의 책임 : 데이터와 View 연결

### Data refresh
#### notifyDataSetChanged : 화면의 모든 item을 즉기 redraw
- poor performance, 복잡한 계산 or 사용자가 스크롤링을 동시에 하는 경우 등 flicker현상이 나타나거나 스크롤이 버벅일 수 있다
- update가 필요한 위치만 RecyclerView에게 정확히 알려줄 필요가 있다
#### DiffUtil
- RecyclerView Adapter가 최근 데이터에서 이전의 데이터와 달라진 부분만 갱신할 수 있도록 계산 

### RecyclerView에서 ClickListener배치
- itemView의 clickListener배치? -> ViewHolder가 적합
![](https://i.imgur.com/5PqyaRi.png)

#### Fragment : RecyclerView이외의 많은 view들 존재
#### RecyclerView : 정확히 어떤 item을 클릭했는지 알기 어렵다
#### Adapter : 목록에 보여줄 data 관리
#### ViewHolder : Adapter보단 더 적당한 위치
- RecyclerView의 각 item에 대한 정보를 추가하기에 적합
- but click이후의 처리는 어디서? -> Activty, Fragment에서

## Multiple ViewType
- 여러 Data Type을 보여줘야 하는 경우

## Summary
- RecyclerView Adapter는 Adapter패턴을 사용해 이미 구현된 기능을 클라이언트의 니즈에 맞게 사용할 수 있도록 지원한다
- Adapter, ViewHolder 각 객체를 관심사에 맞게 캡슐화할 수 있다
- RecyclerView는 많은 데이터를 효율적으로 표시할 수 있다
- item 갱신을 최소화할 수 있는 기능을 지원한다
- 화면에서 스크롤을 벗어난 View를 재사용한다
- item을 list, grid형태로 표시가능하고, 좌우/상하 스크롤 모두 가능하다
- 지원하는 layoutManager로 UI를 구현할 수 없다면, 상속받아 커스텀할 수 있다