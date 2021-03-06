# day3

### **layout**

- 화면에 보이는 모든것은 view / view group역시 view를 상속
- view들을 view group에 넣을 수 있다
- 이러한 view group을 구현한 것이 layout이다.

ex) Constraint layout, Linear layout, Relative layout 등이 있다

안드로이드 개발할떄는 view의 hierarchy를 최대한 깊지않게. 즉 depth가 깊어지지 않게 해야한다.

주로 Constraing layout을 사용하지만 각 layout에 적합한 화면구성이 있을때도 있다.

**Constraint layout**

- 가로, 세로의 제약조건을 추가해 view의 위치를 지정할 수 있다

    ㄴ depth를 줄일 수 있다, 중첩된 layout을 최소화할 수 있다

**Linear layout**

- orientation속성으로 가로, 세로로 쌓을 수 있다
- depth가 늘어난다

**padding, margin**

- 사용자 편의 고려에 필요  ex) 터치 영역이 너무 좁다면 margin을 늘린다

속성값으로 odp 넣으면 그냥 끝까지 다 차지한다. match parent와 차이?

**App bar = Actionbar = Toolbar**

- 커스텀 App bar를위해선 전체 style을 NoActionBar로 해줘야한다

### **Kotlin 기본 문법**

- Data type을 객체로만 제공 →toByte(), toChar()로 가능

toFloat, toDouble등 type casting이 잘되는 이유는 모든 숫자는 Number class를 상속받았기떄문

- 모든 타입의 array를 지원하며 고차함수도 대부분 지원한다
- Safe Call Operator(?)

    ㄴ null일수도 있다는것을 알려주는 것

- Not null assertion operation(!!) → 자주 안쓰는게 좋다

    ㄴnull이 반드시 아니라는것을 알려주는것

- Elvis Operator(?;)

    ㄴ 삼항 연산자같은거. null이 아니면 기존대로 수행, null이면 뒤에꺼 수행

    ex) val parent = node.getParent() ?: return null

- Safe cast operator(?) → 역시나 자주 안쓰는게 좋다

    ex) val aInt : Int? = a as? Int

    Int로 변환가능하면 변환, 아니면 null처리

- lambda식

    ㄴ type추론 생략가능(input, output타입 어떤거인지 명시해주는것)

    ex) val sum : (Int, Int) → Int 이런거

    @로 return, break 영역 설정가능 → 기명 람다 사용한다

### **kotlin 코드 style convention**

- lateinit 사용 생활화 → non null assertion(!!) 사용 최소화
- SAM??
- companion object : 객체 생성안하고도 미리 만들어놓을수있다

    ㄴ navigation graph에 사용하면 destination안써도 될듯?

- single expression function : 짧으면 최대한 한줄로

    ```kotlin
    fun to String():String{
    	return "hello"
    }
    fun toString():String = "hello"		

    이렇게 대체가능
    ```

- ctrl + alt + L : kotlin방식에 맞게 자동정렬해준다
- backing property

    ㄴ 데이터를 외부 공개용, 내부 관리용으로 두는것

    ㄴ 내부 관리용은 변수이름앞에 _를 붙인다