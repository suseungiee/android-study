# DiffUtil구현 Companion vs Class?

### ListAdapter 등장배경
- 기존 Recyclerview를 위해서는 adapter가 필요
- 아이템 갱신위해서는 notifyItemChanged() 호출 but 매번 전체 갱신하기엔 비효율적
- notifyItemPosition(), notifyItemInserted()등 여러 함수 존재 but position값 알아야한다는 단점 존재

### DiffUtil이란
- 아이템의 변경을 확인하고 최소 횟수의 업데이트로 아이템의 갱신해주는 Util class
- areItemsTheSame() && areContentsTheSame()가 false일떄 동작
- 주로 areItemsTheSame에서는 item의 고유 id를 기준으로 판단한다

### AsyncListDiffer
- DiffUtil을 이용해서 진행하는 비교연산을 백그라운드에서 진행해주는 클래스
- submitList()를 이용해 알아서 데이터 갱신해준다

### ListAdapter
- RecyclerView.Adapter를 상속받으며 내부적으로 AsyncListDiffer를 사용할 수 있는 adapter
- DiffUtilCallback을 구현해서 넘겨주면 submitList()하나만으로 데이터 갱신처리 가능

### Java에서 static이란
- Java에서 객체는 new로 생성하며 new로 생성되면 heap에 올라간다
- heap에 올라간 메모리는 garbage collector에 의해 참조가 없어지면 사라진다
- static으로 선언된 변수나 메서드는 메모리에 상주한다 -> 프로그램 종료시 해제된다
- 객체 생성없이 클래스이름으로 사용가능하다 -> 메서드같은경우 초기화되어있는 값들만 사용되어야한다
- 객체 생성하고도 static변수에 접근가능
- 주로 클래스내에 선언하여 클래스가 공유하는 영역으로 사용한다

### Kotlin에서 싱글톤
- Object키워드를 이용해서 싱글톤을 구현할 수 있다 -> 단, 생성자가 필요없는 경우에만 가능
- 생성자가 필요하다면 companion object를 사용한다
```kotlin
class Test private constructor(private val name: String){
    companion object {
        private var instance: Test? = null
        fun getInstance(_name: String): Test? {
            if (instance == null) {
                synchronized(this) {
                	instance = Test(_name)
            	}
            }
            return instance 
        }
    }
}
val inst1 = Test.getInstance("name1")
val inst2 = Test.getInstance("name2")
println("inst1   ${inst1}")  // 같은 주소
prniltn("inst2   ${inst2}")  // 같은 주소
```
### Companion object란
- 자바의 static과 유사 but 객체로 companion변수, 메서드 접근 불가
- 이름을 특정지을 수 있다
- 객체 생성없이 클래스명으로 사용가능 -> 클래스 내 1개만 사용가능
- 상속관계에서 같은 이름이라면 가려질 수 있다 = Shadowing
- 객체다
```kotlin
class A {
    companion object {
        fun printName() {
            println("This is A")
        }
    }
}
class B {
    companion object COMP {
        fun printName(){
            println("This is B")
        }
    }
}
println(A.printName())
println(A.Companion.printName())
println(B.COMP.printName())
```

## ListAdapter에서 DiffUtilCallback구현 시 Companion object가 아니라 class써야하는 이유? + 내생각
- ListAdapter의 companion object를 Callback으로 넘겨주면 ListAdapter의 정보가 알려지게되는데 DiffUtilCallback은 단순히 2개의 값을 비교하므로 알 필요가 없다.
- DiffUtilCallback 재활용하기위해서 (내생각)

[스택오버플로우 질문](https://stackoverflow.com/questions/67420516/diffutil-itemcallback-define-as-a-companion-object-or-as-a-class)

This is probably a matter of opinion. Mine is that the callback should be an object, or anonymous object, but not a companion object.

All it's doing is comparing properties of two objects. It doesn't have to hold any state. So it makes sense for it to be a singleton object rather than a class that you have to instantiate. Whether you define it as a named singleton object or define in place as an anonymous object assigned to a property doesn't make much different in communicating intent.

But it doesn't make sense to me to make it a companion. It's already nested and has a name. All companion does is suggest that you should need to call its functions directly and that the name PhotoGridAdapter should also be thought of as a callback. For instance, it enables you to pass the name PhotoGridAdapter to some other adapter as its DiffUtil callback, which is nonsensical. The only reason it might possibly make sense is if you also want to use it as a utility for comparing items, so you could call functions like PhotoGridAdapter.areContentsTheSame directly. However, I don't think this is likely. Usually, the contents of the callback's functions are either very trivial like passing through equals() or they are very specific to the nature of updating the displayed list.