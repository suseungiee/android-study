# MVP과제 + feedback

### gradle에서 라이브러리 버전값 변수로 분리 가능 -> project수준의 bulid.gradle에 추가
```kotlin
ext.kotlin_version = "1.4.21"
ext.retrofit2_version = "2.6.2"
ext.glide_version = "4.11.0"
    
```
### serialized사용할 때 명시적 naming을 해주는게좋다. 받아오는 값은 naming, 그 외의 값은 그냥 선언
### recyclerview adapter layout설정해주는거 xml에서도 가능.
![](https://i.imgur.com/RIVgUQB.png)

### 메서드 이름은 camel case
### data class에 constructor 추가하면 생성자 사용가능
### 값 설정할때 그냥 2 이렇게보다 상수로 선언해주기
### 인터페이스 이름은 Pascal case
### 모듈이름은 소문자로
### 패키지이름도 소문자 -> Class로 착각할 수 있다.
### mvp패턴으로 구현할 때 recycler view는 bind로 충분하다. 추가적으로 view와 presenter로 나누면 번거롭다
### 클래스에서 생성자에 var, val붙여주면 필드값으로 사용가능하다 -> init{}불필요
```kotlin
class ImageAdapter(var data: MutableList<String>, val hastStr: String, val foodName: String)
```
### var는 getter, setter가 이미 있으므로 setter 필요없다
### 모듈화 분리시킬때 java version은 웬만하면 통일시키기
### 모듈화 분리시킬때 데이터클래스도 보내줘야한다. -> 이름만 다르게해줘서 나중에 내부적으로 convert해주는 코드 필요. 이거는 자동으로 해주는거 없다. 일일히 수작업 필요
```kotlin=
data class OnbanResponse(
        @SerializedName("statusCode")
        val statusCode: Int,
        @SerializedName("body")
        val foodList: MutableList<FoodResponse>
)
//network모듈의 OnbanResponse.kt

data class Onban(
        @SerializedName("statusCode")
        var statusCode: Int,
        @SerializedName("body")
        var foodList: MutableList<Food>
){
        constructor(): this(0, mutableListOf())
}
//app모듈의 Onban.kt

fun change2Onban(response: OnbanResponse): Onban{
    val data = Onban().apply{
        statusCode = response.statusCode
        response.foodList.forEach{
            foodList.add(change2Food(it))
        }
    }
    return data
}
//내가만든 OnbanResponse -> Onban 변환 함수
```

## 모듈화 시키는 방법
- File -> New Module -> Java or Kotlin Library클릭해서 생성
- 해당 모듈의 build.gradle에서 dependency추가
- 모듈간은 build.gradle은 project수준의 build.gradle 공유한다. 따라서 project수준의 build.gradle에 추가해주기

### Multiview RecyclerView
- 나머진 똑같고
```kotlin=
override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): RecyclerView.ViewHolder {
        return when (FoodViewType.values().get(viewType)) {
            FoodViewType.FOOD_DESCRIPTION -> {
                val view = LayoutInflater.from(parent.context).inflate(R.layout.foods_description, parent, false)
                FoodDescription(view)
            }
            FoodViewType.FOOD_COUNT -> {
                val view = LayoutInflater.from(parent.context).inflate(R.layout.foods_count_description, parent, false)
                FoodCount(view)
            }
            FoodViewType.FOOD_ITEM -> {
                val view = LayoutInflater.from(parent.context).inflate(R.layout.food_item, parent, false)
                FoodItem(view)
            }
        }
    }
    
    override fun getItemViewType(position: Int): Int {
        return when (data.get(position)) {
            is FoodTitle -> 0
            is FoodCnt -> 1
            is Food -> 2
            else -> -1
        }
    }

    override fun onBindViewHolder(holder: RecyclerView.ViewHolder, position: Int) {
        when (data.get(position)) {
            is FoodTitle -> {
                (holder as FoodDescription).bind(data.get(position) as FoodTitle)
                holder.setIsRecyclable(false)
            }
            is FoodCnt -> {
                (holder as FoodCount).bind(data.get(position) as FoodCnt)
                holder.setIsRecyclable(false)
            }
            is Food -> {
                (holder as FoodItem).bind(data.get(position) as Food)
                holder.setIsRecyclable(false)
            }
        }
    }
    
// getItemViewType, onBindViewHolder, onCreateViewHolder만 구분해서 작성하기 + inner class 3개 만들어줘야한다
```

### recyclerview adapter외부에 리스너 달수있다.
```kotlin=
var mListener: OnItemClickListener? = null
    interface OnItemClickListener{
        fun onItemClick(v: View, position: Int)
    }
    fun setOnItemClickListener(listener: OnItemClickListener){
        this.mListener = listener
    }
//adapter내부에 이거 선언해주고

init{
            view.setOnClickListener(object: View.OnClickListener {
                override fun onClick(v: View?) {
                    mListener?.onItemClick(view, adapterPosition)
                }

            })
        }
//클릭 발생할 inner class의 init에 이거 작성해주고
foodAdapter.setOnItemClickListener(object : MultiViewAdapter.OnItemClickListener {
            override fun onItemClick(v: View, position: Int) {
                val clickItem = foodAdapter.data.get(position)
                if (clickItem is Food) {
                    val intent = Intent(applicationContext, FoodDetailActivity::class.java)
                    intent.putExtra("detail_hash", clickItem.detailHash)
                    intent.putExtra("title", clickItem.title)
                    network.changeNetworkType()

                    startActivity(intent)
                }
            }
        })
//외부에서 이렇게 리스너 달아주면 된다. 아직 잘 이해못함..
```
### 이미지 캐싱(Disk caching. memory caching은 따로)
- URL -> bitmap -> 파일로 저장
- 이미지 캐싱은 Model에서 하는거다.(View에서해도 되었다고한것같은데. 아닌가)
```kotlin=
fun getImageFromURL(imageURL: String?): Bitmap? {
    val url = URL(imageURL)

    val conn = url.openConnection() as HttpsURLConnection
    conn.connect()
    val nSize: Int = conn.contentLength
    val bis = BufferedInputStream(conn.getInputStream(), nSize)
    val imgBitmap = BitmapFactory.decodeStream(bis)
    bis.close()
    conn.disconnect()

    return imgBitmap
}
// URL에서 이미지 가져오는 부분. url이 http가 들어올 수 있으면 외부에서 .replace("http:","https:")이렇게 변환해서 넣어주기
//기본적으로 getCacheDir하면 data -> data-> 해당 패키지이름 -> cache를 가리킨다. 안드로이드 스튜디오 Device File Explorer 들어가면 루팅안하고도 내부 볼 수 있다. 실물 폰도 가능
![](https://i.imgur.com/TSArdAI.png)
![](https://i.imgur.com/OiZmXXk.png)

fun saveBitmapToJpeg(bitmap: Bitmap, name: String, cachePath: String, type: String) {
    //storage 에 파일 인스턴스를 생성합니다.
    val path = "${cachePath}/${type}"
    File(path).mkdir()  //해당 경로 만들어주기. File생성할때 이름에 /들어가면 안된다. 따라서 경로 만들어주고 name.jpg이렇게 넣어준거다
    val tempFile = File(File(path), "${name}.jpg")
    // 자동으로 빈 파일을 생성합니다.

    tempFile.createNewFile()

    // 파일을 쓸 수 있는 스트림을 준비합니다.
    val out = FileOutputStream(tempFile)

    // compress 함수를 사용해 스트림에 비트맵을 저장합니다.
    bitmap.compress(Bitmap.CompressFormat.JPEG, 100, out)

    // 스트림 사용후 닫아줍니다.
    out.close()
}

//↓이미지 캐시에서 가져오기
val path = "${itemView.context.cacheDir}/$FOOD_DETAIL_DIR"
            if (File("${path}/${foodName}_image_0.jpg").exists()) {
                val bitmapFile = BitmapFactory.decodeFile("${path}/${foodName}_image_${position}.jpg")
                descriptImg.setImageBitmap(bitmapFile)
            } else {
                Glide.with(itemView)
                        .load(item)
                        .into(descriptImg)
            }
```
### ViewPager
- recyclerview랑 같다
- viewPager스크롤 무한으로 하려면 getItemCount만 큰 값으로 설정해주고 onBindViewHolder할때 position값만 처리해주면 된다
```kotlin=
override fun onBindViewHolder(holder: PagerViewHolder, position: Int) {
        if (data.size != 0){
            holder.bind(data.get(position % data.size), position)
        }
    }

    override fun getItemCount(): Int {
        return SCROLL_INFINITE // 100
    }
// 이때 getItemCount값 너무 크게하면 에러났던것같다...
```
- 자동스크롤(handler이용)
- 드래그할때 자동 스크롤 막으려면 viewpager의 registerOnPageChangeCallback에서 onPageScrollStateChanged함수에서 state로 확인한다
```kotlin=
binding.foodDetailFoodViewpager.registerOnPageChangeCallback(object :
            ViewPager2.OnPageChangeCallback() {
            
            override fun onPageScrollStateChanged(state: Int) {
                super.onPageScrollStateChanged(state)
                when (state) {
                    ViewPager2.SCROLL_STATE_IDLE -> autoScrollStart(700)
                    ViewPager2.SCROLL_STATE_DRAGGING -> autoScrollStop()
                }
            }
        })
```
```kotlin=
inner class CustomHandler : Handler(Looper.getMainLooper()) {
        override fun handleMessage(msg: Message) {
            super.handleMessage(msg)
            if (msg.what == 0) {
                binding.foodDetailFoodViewpager.setCurrentItem(
                    ++binding.foodDetailFoodViewpager.currentItem,
                    true
                )
                autoScrollStart(700)
            }
        }
    }

    fun autoScrollStart(intervalTime: Long) {
        handler.removeMessages(0)
        handler.sendEmptyMessageDelayed(0, intervalTime)
    }

    fun autoScrollStop() {
        handler.removeMessages(0)
    }
//커스텀 핸들러 만들어서 autoscroll구현해줬다. 잘 이해못함...
```

### Retrofit사용법
- get, set함수 만들어주고
```kotlin=
interface RetrofitService {
    @GET("/default/SwiftCamp")
    fun getUsers(): Call<List<String>>

    @POST("/default/SwiftCamp")
    fun postUser(@Body user: UserResponse): Call<UserResponse>

    @GET("/onban/main/")
    suspend fun getMain(): OnbanResponse
    
    @POST("/services/T02B3276ZT4/B02D4660XK2/itQUMQeQsdiUWZ330Q5sxCLG")
    fun postOrder(@Body order: OrderRequest): Call<OrderRequest>
    //이건 인자를 받는 동적 retrofit post
}

```
- base url바탕으로 builder만들어주고
```kotlin=

    val retrofitUser = Retrofit.Builder()
        .baseUrl("https://8r6ruzgzve.execute-api.ap-northeast-2.amazonaws.com")
        .addConverterFactory(GsonConverterFactory.create())
        .build()

    val retrofitFood = Retrofit.Builder()
        .baseUrl("https://api.codesquad.kr/")
        .addConverterFactory(GsonConverterFactory.create())
        .build()

    val retrofitOrderFood = Retrofit.Builder()
        .baseUrl("https://hooks.slack.com")
        .addConverterFactory(GsonConverterFactory.create())
        .build()
```
- 호출할 함수만들어주고

```kotlin=
    fun addUser(tempUser: UserResponse, callback: (Int) -> Unit) {
        val api = retrofitUser.create(RetrofitService::class.java)
        api.postUser(tempUser).enqueue(object : Callback<UserResponse> {
            override fun onResponse(call: Call<UserResponse>, response: Response<UserResponse>) {
                callback(response.code())
            }

            override fun onFailure(call: Call<UserResponse>, t: Throwable) {
                callback(-1)
            }
        })
    }

    fun order(orderList: OrderRequest) {
        val api = retrofitOrderFood.create(RetrofitService::class.java)
        api.postOrder(orderList).enqueue(object: Callback<OrderRequest>{
            override fun onResponse(call: Call<OrderRequest>, response: Response<OrderRequest>) {
                print("order success")
            }

            override fun onFailure(call: Call<OrderRequest>, t: Throwable) {
                print("order fail")
            }
        })
    }

    suspend fun getMainFood(): OnbanResponse {
        val api = retrofitFood.create(RetrofitService::class.java)
        return api.getMain()
    }
    
```
- 객체 생성해서 함수호출하듯 사용하면된다
```kotlin=
override fun getData() {
        CoroutineScope(Dispatchers.Main).launch {
            var mains : Onban? = null
            var soups : Onban? = null
            var sides : Onban? = null
            launch {
                launch { mains = change2Onban(RetrofitInterface.getMainFood()) }
                launch { soups = change2Onban(RetrofitInterface.getSoupFood()) }
                launch { sides = change2Onban(RetrofitInterface.getSideFood()) }
            }.join()
            makeData(mains!!.foodList, soups!!.foodList, sides!!.foodList)
            view.setData(data)
        }
    }
//여기서는 RetrofitService객체를 object로 선언했다.
```
### MVP패턴
- view에서 액션들어오면 view -> presenter로 알려주고
- presenter에서 Model로 필요한 데이터 요청보내면
- Model에서 데이터 가공해서 다시 presenter로 보내면
- presenter는 그려야할 데이터만 view로 보내주는 방식
- View, Presenter만 Contract라는 인터페이스로 묶어주는 형태. Model은 주로 안넣는다.

### Toast 시간 줄이기
- handler이용한다
```kotlin=
    val toast = Toast.makeText(this, "주문 완료", Toast.LENGTH_SHORT)
        toast.show()
        val handler = Handler(Looper.getMainLooper())
        handler.postDelayed({ toast.cancel() },700)
```
### callback 만드는방법
- 인터페이스 만들어서 넘겨주던가(onClick()처럼), 1개짜리 인터페이스면 람다로 넘겨주면된다
```kotlin=
RetrofitInterface.addUser(tempUserRespone) { responseCode: Int ->
            when (responseCode) {
                200 -> {
                    view.signInResult(State.OK)
                    mainModel.saveUser(tempUser)
                    //sharedPreference에 저장
                }
                400 -> {
                    view.signInResult(State.ERROR)
                    //id, pw를 제대로 입력하세요 ??
                }
                409 -> {
                    view.signInResult(State.DUPLICATE)
                    //이미 존재하는 아이디입니다
                }
                else -> {
                    view.signInResult(State.ERROR)
                }
            }
        }
//원래 (인자, {람다식})이 형태를 (인자) {람다식} 이렇게 보낸거

fun addUser(tempUser: UserResponse, callback: (Int) -> Unit) {
        val api = retrofitUser.create(RetrofitService::class.java)
        api.postUser(tempUser).enqueue(object : Callback<UserResponse> {
            override fun onResponse(call: Call<UserResponse>, response: Response<UserResponse>) {
                callback(response.code())
            }

            override fun onFailure(call: Call<UserResponse>, t: Throwable) {
                callback(-1)
            }
        })
    }
//호출되는 입장에서는 callback을 인자로 input, output타입 명시해서 선언해주고 callback(람다식 input)이렇게해주면된다
```
### TextInputLayout사용방법
- error, hint, 다 구현되어있는 edittext layout이다
```kotlin=
    <com.google.android.material.textfield.TextInputLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintTop_toBottomOf="@id/id_title"
        android:layout_marginTop="10dp"
        android:layout_marginStart="40dp"
        android:layout_marginEnd="40dp"
        android:id="@+id/id_edit_layout"
        app:errorEnabled="true"
        app:hintEnabled="false"
        style="@style/Widget.MaterialComponents.TextInputLayout.OutlinedBox"
        >
        <EditText
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:inputType="text"/>
    </com.google.android.material.textfield.TextInputLayout>
```
### ScrollView
- scrollview는 1개의 layout만 담을 수 있으므로 scroll view에 들어갈 view들을 큰 layout1개로 묶어줘야한다