# day1

### **res(xml) ↔ Java(source code부분))**

res : 화면 배치 정의

즉 코드가 아닌 모든 리소스를 포함

mipmap : launcher아이콘만

drawable : png, 9패치 등 이미지

values : 문자열 ,색상, 문자스타일

font, menu, animator존재 (menu : bottom navigator에서 사용)

source code : 사용자와의 상호작용 처리

### dp, dip (density independent pixel)

안드로이드 제조사마다 화면 픽셀갯수가 다르다.

따라서 다른 픽셀갯수라도 똑같이 보이게 하기위해 dp라는 단위사용한다.

즉 view의 크기, padding, margin은 dp, 글자는 주로 sp를 사용한다

### **SVG(Standard Vector graphic)**

벡터정보로 그래픽을 정의하는것

### **Manifest.xml**

- 앱의 필수 정보가 있는곳
- 패키지이름 + 앱의 전체적인 구조 + 앱에서 필요한 사용자 권한 등..
- 안에서 activity순서는 중요x but 없으면 해당 엑티비티 보여질때 앱 죽는다.

### build.gradle

Java에 있는 코들린파일 + xml파일을 컴파일 해서 1개의 output을 만들어주는것

즉 안드로이드 build tool

Gradle Wrapper이용해서 IDE없이 build가능

api정보, 라이브러리 종속성 등 존재

### **look and feel**

소프트웨어 디자인에서 룩앤필은 GUI(Graphical User Interface)측면과 디자인의 형태 구성에서 사용되는 용어로 색상, 모양, 레이아웃, 서체(look)와 같은 요소와 버튼, 박스, 메뉴(feel)와 같은 동적 요소의 행위를 포함한다.

![https://user-images.githubusercontent.com/52225690/130503112-d747d9ab-9681-4299-ae7e-555ec6123222.png](https://user-images.githubusercontent.com/52225690/130503112-d747d9ab-9681-4299-ae7e-555ec6123222.png)

즉 글씨체, 두께, 버튼, 이런거 다 간략하게 보여주는 거 같다

### **view**

기기 화면에 보이는 모든 것

그중에서 위젯 : 사용자가 화면을 보면서 상호작용하는 view

위젯 ex) textView, button

### **layout**

view들의 집합

view, viewGroup을 일반적으로 layout이라한다

viewGroup역시 view를 상속받았다

### **Theme, Style 차이**

Style : font color, font size.. 등의 속성모음

style태그에서 parent를 상속받을 수 있다.

Widget은 하나의 style만 적용할 수 있다

Theme : view, 앱, Activity 전체적으로 적용되는 속성모음

따라서 Theme을 사용하면 개별 activity, view내용 일일히 변경할 필요없이 한번에 변경가능

(상단)AppBar == ToolBar == ActionBar

margin : View간의 간격

padding : View의 경계와 내부 콘텐츠 간의 간격