# 피드백 - 1주차

### 1. git ignore를 먼저 push한다음에 commit을 해야 적용이된다!!
ㄴ 작업 내용과 관계없는 file changed가 많아지면 리뷰 어려워진다

### 2. 코드 마지막에 항상 enter쳐주기 -> 그래야 git에 '}'전에 공백만 보인다

### 3. 수정을 하지 않는 변수는 왠만하면 val로 처리 -> 유지보수나 실수를 줄이는데 도움이 된다

코틀린에서 var은 가변, val은 불변이다

- val로 선언할 경우, '메모리,멀티쓰레드 안전성, 함수형 코드'등 얻을수 있는 이점이 많다.

### 따라서 코틀린은 가급적 모든 변수를 'val로 선언하여 불변으로 설정하고, 필요한 경우에만 'var'를 쓰도록 권장하고 있다.

### 4. 변수의 첫글자는 소문자, xml id는 camelCase
### 5. 객체인스턴스 이름 명확하게!
```kotlin
var plane1 = MyPlane()    x
var mainPlane = MyPlane() o
```
### 6. !!쓰지말고 ?.나 ?:로 null check해주기!
```kotlin=
val allCaps: String? = s?.toUpperCase()
if (s != null) s.toUpperCase() else null

val name = str ?: "Unknown"
if (str != null) str else "Unknown"
```
### 7. 256, 10 이런 숫자는 상수로 변경하여 사용하면 실수도 줄일 수 있고, 해당 숫자가 어떤 의미를 가지는지 쉽게 알 수 있게한다

### 8. constrainLayout은 1개만 사용할 때 이점을 발휘한다, 필요하면 flow사용하기

### 9. textSize는 sp! 길이는 dp!

### 10. viewBinding은 동적으로 추가된 view에는 접근 불가!

### 11. enum class는 모두 대문자로!!

### 12. 변수나 상수이름으로 is가 붙는거는 주로 true, false의 값을 가질때 사용된다

### 13. 변수 네이밍 제대로!!
```kotlin=
var bcButton  
var backgroundColorButton 이렇게 풀네임을 쓰는게 협업자가 이해하기 쉽다
```
### 14. apply scope함수는 한 줄 속성변경에는 사용하지 않는다

### 15. onClick을 override하면 가독성이 떨어진다. 자주 왔다갔다해야해서...

# week3
## activity에서 viewbinding사용하기

setContentView(R.layout.activity_main) 지우고

```kotlin
binding = ActivityMainBinding.inflate(layoutInflater)
setContentView(binding.root) 

onCreate()내부에 이거 추가하기
```

## **프로그래밍 방식으로 view 추가하기**

### 1. 해당 도형의 layout_width, height 설정하기

```kotlin
android:layout_width="wrap_content"
android:layout_height="wrap_content"

↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓

var params = ConstraintLayout.LayoutParams(
                    ConstraintLayout.LayoutParams.WRAP_CONTENT,
                    ConstraintLayout.LayoutParams.WRAP_CONTENT
             )
```

### 2. ConstraintLayout사용하는경우 startStart, toptop 설정해주기

```kotlin
app:layout_constraintTop_toTopOf="parent"
app:layout_constraintStart_toStartOf="parent"

↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓

params.startToStart = binding.mainLayout.id
params.topToTop = binding.mainLayout.id

여기서 binding.mainLayout은 root layout.
저렇게 .id를 붙여야 적용된다
```

### 3. margin이 필요한 경우 다음과 같이 작성

```kotlin
params.marginStart= changeDP(shape.point.start_x)
params.topMargin = changeDP(shape.point.start_y)

여기서 changeDP는 int -> dp로 바꿔주는 custom 함수
fun changeDP(value: Int) : Int{
    var displayMetrics = resources.displayMetrics
    var dp = Math.round(value * displayMetrics.density)
    return dp
}
```

### 4. 추가할 view객체 생성 및 property 설정

```kotlin
var view = View(this)
view.setBackgroundColor(Color.parseColor(shape.color.res))
```

### 5. 추가할 view에 위에서 추가한 LayoutParams 적용

```kotlin
view.layoutParams = params
```

### 6. root layout에 해당 view 추가 및 UI 갱신

```kotlin
binding.mainLayout.addView(view)
~~binding.root.invalidate()  //안해줘도 자동 갱신된다~~
~~ㄴ 모든 view객체는 view를 갱신해주는 invalidate함수가 있다
ㄴ root에 view를 추가해주고 다시 갱신하면 새롭게 ui가 갱신되는 것과 같은 효과~~

~~setContentView(binding.root)해도 되지만 이건 onCreate에서 한번만 하는게 좋을 것 같다~~
```
## **LiveData사용방법**

### 1. 적용할 변수와 타입을 선언한다. 아래에서는 특정String을 observe하는 bcButtonLiveData, 특정int를 observe하는 seekBarLiveData로 선언


```kotlin
var bcButtonLiveData : MutableLiveData<String> = MutableLiveData()
var seekBarLiveData : MutableLiveData<Int> = MutableLiveData()
```

### 2. LiveData에 변화가 일어났을 떄 동작 정의. it으로 값 가져올 수 있다

    아래에서는 it이 None이 아니라면 배경색바꿔주고 아니면 text만 바꿔준다

```kotlin
bcButtonLiveData.observe(this,Observer{
Log.d("LiveData","bcButtonLiveData   ${it}")
    if (it!= "None") {
        currentView.setBackgroundColor(Color.parseColor("#${it}"))
        binding.backgroundColorButton.text=it
} else { binding.backgroundColorButton.text=it}
})
```

### 3. LiveData에 값 변화시키기. LiveData.value로 값 set할 수 있다
ㄴ Model에서 변경해야한다

```kotlin
if (currentObj.src != 0) {
    bcButtonLiveData.value= "None"
} else {
    seekBarLiveData.value= currentObj.color.transparent
    bcButtonLiveData.value= currentObj.color.rgbToString()
}
```
## LiveData는 Controller와 Model분리시키기 위함.
ㄴ 따라서 Controller에서 직접 set해서 Model의 값 가져오기 x
ㄴ Controller는 LiveData로 리스너만 등록하고 값 변경은 모델에서하기!!
ㄴ 그니깐 controller에서 Model에있는 setter이런 함수 호출하는 방식으로 진행해봐


## 갤러리에서 사진 가져오기
우선 갤러리에 접근할 수 있는 권한을 manifest에 추가해줘야한다

```kotlin
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE"/>

AndroidManifest.xml
```
이후에 intent를 이용해 갤러리에서 데이터를 가지고와야한다.

이를위해 startForActivityResult로 intent를 보내고 onActivityResult를 override해줘야한다

```kotlin
binding.picture.setOnClickListener{
		Intent().apply{
			type= "image/*"
			action= Intent.ACTION_GET_CONTENT
			startActivityForResult(this,requestPicture)
		}
}

intent의 property를 다음처럼 지정해주면 이후 startActivityForResult시 갤러리에 접근하게된다

override fun onActivityResult(requestCode: Int, resultCode: Int, data: Intent?) {
        super.onActivityResult(requestCode, resultCode, data)

        if(resultCode == RESULT_OK) {
            if (requestCode == requestPicture) {
                try {
                    mainPlane.addObject((rectangleModel.makeInstance() as Rectangle).apply {
                        src = MediaStore.Images.Media.getBitmap(contentResolver, data?.data)
                    })
                    addToView(mainPlane.getObject(mainPlane.rectList.lastIndex))
                } catch (e: Exception) {
                    Log.d("gallery", "Something wrong")
                }
            }
        }
    }

이후에 갤러리에서 사진을 선택하게되면 data에 선택정보가 담긴다. 이를 bitmap정보로 변환하여 추가할 Rectangle의 src값에 넣어주었다.
이후 사각형 view를 동적으로 생성할 때 src가 null이면 색상만 담긴 view, null이 아니면 src로 bitmap이미지를 그려주도록하였다.

if (obj.src != null) {
		setImageBitmap(obj.src)
} else { setBackgroundColor(Color.parseColor(obj.color.toString()))
}
```

### 드래그하기 (한 손가락 + 임시뷰 x)

한 view에 setOnClickListener랑 setOnTouchListener를 동시에 적용할 수 없는 것 같다.

따라서 setOnTouchListener를 등록해주고 이 안에 OnClick에서 사용한 함수를 넣어주었다.

[https://sunghyun1038.tistory.com/24](https://sunghyun1038.tistory.com/24)

위 링크를 참조하여 사용하였다.

핵심 동작부분만 보자면 다음과 같다.

```kotlin
val parentWidth = (view.parentas ViewGroup).width// 부모 View 의 Width
val parentHeight = (view.parentas ViewGroup).height// 부모 View 의 Height

if (event.action== MotionEvent.ACTION_DOWN) {
    // 뷰 누름
} else if (event.action== MotionEvent.ACTION_MOVE) {
    // Actual ImageView 이동 중
    actualImageView.x= (view.x+ event.x- view.width/ 2)
    actualImageView.y= (view.y+ event.y- view.height/ 2)
    // Border ImageView 이동 중
    borderImageView.x= (view.x+ event.x- view.width/ 2)
    borderImageView.y= (view.y+ event.y- view.height/ 2)

} else if (event.action== MotionEvent.ACTION_UP) {
    // 뷰에서 손을 뗌
    if (view.x< 0) {  //x값 경계값 처리
        actualImageView.x= 0F; borderImageView.x= 0F;
    } else if (view.x+ view.width> parentWidth) {
        actualImageView.x= ((parentWidth - view.width).toFloat())
        borderImageView.x= ((parentWidth - view.width).toFloat())
    }

    if (view.y< 0) {  //y값 경계값 처리
        actualImageView.y= 0F;  borderImageView.y= 0F
    } else if (view.y+ view.height> parentHeight) {
        actualImageView.y= ((parentHeight - view.height).toFloat())
        borderImageView.y= ((parentHeight - view.height).toFloat())
    }
}
```

view.x = view 의 좌측 상단이 되는 지점의 **절대 좌표값**

event.x = View 내부에서 터치한 지점의 **상대 좌표값**

ACTION_MOVE에서 view.width/2 부분은 화면을 양 테두리까지 view를 드래그했을때 얼마나 보이게할지를 정의해주는 경계값 처리이다. 실행해보고 view를 이리저리 드래그해보면 알 수 있다.

↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓ 챌린지때 배운 frame based 방식에 대해 다시 기억해볼 수 있었다
![](https://i.imgur.com/ZmWedwO.png)
