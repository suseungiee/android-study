# MVVM정리(starbuskst Readme)

- MVVM에서는 단방향 데이터 흐름 원칙은 지켜야한다!!
     - View -> Event -> ViewModel -> Business Logic -> State -> View
- view에서는 viewModel의 데이터를 observe하고, viewModel에서는 UI에 표시될 데이터 및 데이터의 처리를 담당한다.
- viewModel을 다음과 같이 factory클래스를 이용해서 생성한다
```kotlin
class ViewModelFactory(private val context: Context) : ViewModelProvider.Factory {
    override fun <T : ViewModel?> create(modelClass: Class<T>): T {
        return with(modelClass) {
            when {
                isAssignableFrom(EventViewModel::class.java) ->
                    EventViewModel(
                        Injection.provideEventRepository(context.getSharedPreferences("starbuckst",
                            AppCompatActivity.MODE_PRIVATE
                        
                        ))
                    )
                isAssignableFrom(HomeViewModel::class.java) ->
                    HomeViewModel(
                            Injection.provideHomeRepository()
                    )
                isAssignableFrom(WhatsNewViewModel::class.java) ->
                    WhatsNewViewModel(
                            Injection.provideHomeRepository()
                    )
                else -> throw IllegalArgumentException("Unknown viewModel")
            }
        } as T
    }

    companion object {
        @Volatile private var instance: ViewModelFactory? = null

        fun getInstance(context: Context): ViewModelFactory =
            instance ?: synchronized(this) {
                instance ?: ViewModelFactory(context).also { instance = it }
            }
    }
}
```
- viewModel은 의존성 주입을 통해 repository를 가지고있으며 repository를 통해 데이터 처리를 담당한다
```kotlin
object Injection {
    fun provideEventRepository(sharedPref : SharedPreferences) : EventRepository = EventRepository(sharedPref)
    fun provideHomeRepository() : HomeRepository = HomeRepository()
    fun provideOrderRepository(): OrderRepository = OrderRepository()
    fun provideLocalRepository(context : Context) : LocalRepository = LocalRepository(context)
}
//Injection.kt

class OrderRepository {
    private val starbucksApi = ApiFactory.createStarbucksApi()

    suspend fun loadColdBrewCDList() : List<OrderItem> = starbucksApi.requestColdBrewCDList().productCDList
    suspend fun loadEspressoCDList() : List<OrderItem> = starbucksApi.requestEspressoCDList().productCDList
    suspend fun loadFrappuccinoCDList() : List<OrderItem> = starbucksApi.requestFrappuccinoCDList().productCDList
    }
//OrderRepository.kt
```

### binding adapter
바인딩 어댑터를 사용해서 해결하는것도 한 가지 방법이 될 수 있겠네요.
한 가지 주의할 점은 바인딩 어댑터를 너무 남용하는 것은 피해야 합니다.

바인딩 어댑터는 지정한 뷰와 값에 따라 전역적으로 적용이 되기 때문에 내가 만든 바인딩 어댑터가 개발하고 있는 다른 곳에 영향을 줄 수 있습니다.
그렇기 때문에 꼭 필요한지 검토를 해야하고 바인딩 어댑터를 범용적인 유틸 형태로 구현을 해서 사용하는 경우도 많이 있습니다.

View의 코드가 줄어드는 목적으로 바인딩 어댑터를 사용할 수 도 있지만 반대로 이 코드를 변경시키는 곳이 코드에도 있고 xml에도 있고 바인딩 어댑터에도 있으면 분석의 복잡도가 더욱 올라가는 역효과가 나기도 합니다
- binding adapter는 모든 xml에서 사용할 수 있으므로 전역적으로 사용가능한 부분만 작성하는 것이 좋다.
- 여러가지 인자를 받는경우 xml에서는 2개의 property를 적용시켜줘야한다
```kotlin
@BindingAdapter("app:rectUploadPath","app:rectThumbUrl")
fun loadRectImage(imageView: ImageView, uploadPath: String, thumbUrl: String){
    Glide.with(imageView.context)
            .load("${uploadPath}/upload/promotion/${thumbUrl}")
            .into(imageView)
}
이거를 쓰는 경우 아래와같이 2개 써줘야한다.
<ImageView
            android:layout_width="200dp"
            android:layout_height="150dp"
            android:id="@+id/iv_event_item"
            app:rectUploadPath="@{item.imageUrl}"
            app:rectThumbUrl="@{item.mobThumbnail}"
            />
```
- 질문 1 -> binding adapter 작동방식
```kotlin
@BindingAdapter("app:setImage")
load(textview, list)
@BindingAdapter("app:setImage")
load2(ImageView, list)
//다음과 같이 적용하는 뷰가 textview와 imageview처럼 다른 경우에는 annotation이 같아도 되나요?
```
바인딩 어댑터가 컴파일 시점에 구현체가 만들어지면서 뷰의 타입을 기준으로 만들어지기 때문에
다른 타입에 대해서는 동일한 바인딩 어댑터를 만드는것은 문제가 되지 않습니다

- 질문 2 -> binding adapter쓸 때 view타입 같은 경우 주의사항.
```kotlin
@BindingAdapter("app:setImage")
load(textview, list)
@BindingAdapter("app:setImage")
load2(textView, list)
// annotation이 같아도 된다면 다음과 같이 같은 textview에 적용되는 부분에서는 문제가 있을까요?
```
이거는 데이터바인딩에서 어떤 바인딩 어댑터를 찾을지 결정을 모호하게 하기 때문에 위험합니다.
여러개의 모듈로 되어 있는 경우에는 app의 모듈이 제일 우선순위가 높게 되어 문제가 안생길 수 있는데
동일한 모듈에서 중복되 바인딩 어댑터를 사용하면 오류가 발생할 수 있습니다.

그리고 바인딩 어댑터가 겹친 경우에는 빌드 경고가 지속적으로 발생할거에요
- 질문3 -> binding adapter쓰면 null처리 꼭 해주기!!
```kotlin
load2(textView, list<String>?){
   list ?: null
   //작업
}

/// 이런식으로 null처리를 맨 처음에 해주고 이후 작업을 진행하니 해결이 되었습니다. bindingAdapter를 사용하는경우 bindingAdapter에서 참조하는 값이 초기화되어있지않은 경우 해당 변수가 null값을 가지므로 null처리를 해야한다고 이해했는데 이해한것이 맞을까요?
```
List<...>는 참조형이기 때문에 데이터바인딩으로 처리될때 기본 값으로 null이 사용됩니다.
java에서 호출하는 코드이기 때문에 null에 안전하지 않을 수 있어 null 관련 처리를 해 주시는 것이 좋습니다.

BindingAdapter는 기본적으로 입력으로 주어진 타입에 따라 기본 값을 설정하게 됩니다.
어떤 값으로 초기화 되는지 확인하는 가장 쉬운 방법은 BindingImpl 구현체의 java 코드를 보시면 됩니다.
빌드 이후 함수가 사용되는 부분을 확인해보면 BindingImpl.java 파일에서 사용하는 것을 볼 수 있으니 참고 해 주시면 됩니다.

### navigation graph
- 인자 전달
```kotlin
<fragment
        android:id="@+id/nv_order_list"
        android:name="com.example.starbuckst.ui.order.OrderListFragment"
        android:label="OrderListFragment" >
        <argument
            android:name="orderType"
            app:argType="com.example.starbuckst.ui.order.OrderType" />
        <argument
            android:name="item"
            app:argType="com.example.starbuckst.ui.order.model.ItemDetail"
            app:nullable="true" />

        <action
            android:id="@+id/action_order_list_to_order"
            app:popUpTo="@id/nv_order"
            app:popUpToInclusive="true"
            app:destination="@id/nv_order" />
// 다음과 같이 action을 호출할 때 인자를 전달할 수 있는데
val action = OrderFragmentDirections
        .actionOrderToOrderList(OrderType.DEFAULT, temp)
        findNavController().navigate(action)
//다음과 같이 인자 2개다 넘겨줘야한다.
//이후 받는쪽에서
private val args : OrderListFragmentArgs by navArgs()
args.orderType.toString()
args.item?.koreanName
//이렇게 접근가능
```
- toolbar 사용
```kotlin
private fun initToolbar() {
        with(activity as AppCompatActivity) {
            setSupportActionBar(binding.tbOrderList)
            supportActionBar?.setDisplayHomeAsUpEnabled(true)
            supportActionBar?.setDisplayShowTitleEnabled(false)
            supportActionBar?.setHomeAsUpIndicator(R.drawable.ic_baseline_arrow_back_ios_new_24)
        }

        binding.tbOrderList.setNavigationOnClickListener {
            findNavController().navigate(R.id.action_order_list_to_order)
        }
    }
// 이렇게 해줘야한다.
```
### 효과음
- soundManager를 사용하면 된다
- 소리는 res/raw에 저장하면 된다

```kotlin
soundPool = SoundPool.Builder().build()
soundManager = SoundManager(requireActivity(), soundPool)
soundManager.addSound(0, R.raw.bell_ring)
//이렇게 등록한다음
soundManager.playSound(0) //이걸로 시작
soundManager.stopSound(0)  //이걸로 정지
soundManager.addSound(0, R.raw.bell_ring) //이걸로 정지한다음 바로 등록해놔야 이후 playSound로 실행가능하다.


public class SoundManager {
    lateinit var mSoundPool : SoundPool
    var mSoundPoolMap: HashMap<Int, Int> = hashMapOf()
    lateinit var mAudioManager: AudioManager
    lateinit var mContext: Context

    constructor(mContext: Context, mSoundPool: SoundPool) {
        this.mContext = mContext
        this.mSoundPool = mSoundPool
        mSoundPoolMap = hashMapOf<Int, Int>()
        mAudioManager = mContext.getSystemService(Context.AUDIO_SERVICE) as AudioManager
    }
    fun addSound(index: Int, soundId: Int) {
        mSoundPoolMap.put(index, mSoundPool.load(mContext, soundId, 1))
    }
    fun playSound(index: Int): Int {
        var streamVolume = mAudioManager.getStreamVolume(AudioManager.STREAM_MUSIC)
        return mSoundPool.play(mSoundPoolMap.get(index)!!, streamVolume.toFloat(), streamVolume.toFloat(), 1, 0, 1f)
    }
    fun stopSound(streamId: Int){
        mSoundPool.stop(streamId)
    }
    fun pauseSound(streamId: Int){
        mSoundPool.pause(streamId)
    }
    fun resumeSound(streamId: Int){
        mSoundPool.resume(streamId)
    }
}
```
### animation
- 기본적으로 move, rotate, resize, transparent등이 있다
- res/anim폴더에 animation효과에 따른 내용 작성한다
```kotlin
<?xml version="1.0" encoding="utf-8"?>
<translate
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:duration="2000"
    android:fromYDelta="0%"
    android:toYDelta="88%">
</translate>
```
- 이후 activity, fragment에서 view랑 animation 연결해주면된다
```kotlin
lateinit var animation : Animation

animation = AnimationUtils.loadAnimation(requireActivity().applicationContext, R.anim.move_animation)
binding.ivStar.animation = animation
```
- setAnimationListener리스너가 있어서 animation start, end를 감지할 수 있다

### Notification(푸시알림)
- channel을 만들어줘야 푸시알림이 보여된다. 즉 createChannel을 호출해줘야한다(application()상속받은 최상위에서 해주면 된다.)
```kotlin
const val CHANNEL_ID = "ID_NOTIFICATION"
const val NOTIFICATION_ID = 88
object Notification {
    fun show(context: Context, productName: String, ind: Int) {
        val intent = Intent(context, HomeActivity::class.java).apply {
            flags = Intent.FLAG_ACTIVITY_NEW_TASK or Intent.FLAG_ACTIVITY_CLEAR_TASK
        }
        val pendingIntent: PendingIntent = PendingIntent.getActivity(context, ind, intent, 0)

        // TODO custom notification 작성하기
        /*val notificationLayout = RemoteViews(context.packageName, R.layout.notification_layout)
        notificationLayout.setTextViewText(R.id.title, productName)*/

        val notification = NotificationCompat.Builder(context, CHANNEL_ID)
            .setSmallIcon(R.drawable.blank_heart)
            .setContentTitle("음료 준비 완료")
            .setContentText("주문하신 ${productName}가 준비되었습니다")
            .setPriority(NotificationCompat.PRIORITY_DEFAULT)
            .setContentIntent(pendingIntent)
            .setAutoCancel(true)
            .build()
        with(NotificationManagerCompat.from(context)) {
            // notificationId is a unique int for each notification that you must define
            notify(ind, notification)
        }
    }

    fun createChannel(context: Context) {
        println("createChannel")
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
            val name = "테스트 이름"
            val descriptionText = "테스트 설명"
            val importance = NotificationManager.IMPORTANCE_DEFAULT
            val channel = NotificationChannel(CHANNEL_ID, name, importance).apply {
                description = descriptionText
            }
            NotificationManagerCompat.from(context).createNotificationChannel(channel)
        }
    }

}
```
- notification id별로 푸시알림이 추가되거나 갱신된다. 즉 notification id가 같다면 1개의 푸시알림에 계속 update되고, 다르다면 새로운 푸시알림이 생성된다.
- 앱이 종료되면 알림안온다. -> broadcast receiver사용해야한다
- pending intent를 이용하면 푸시알림 클릭했을때 동작할 이벤트 설정가능

### Application()상속한 최상위 클래스
- 어플리케이션 컴포넌트 사이에서 공동으로 멤버를 사용할 수 있게해주는 클래스
- Manifest에 name 등록해줘야한다
```kotlin
class App: Application() {
    init {
        instance = this
    }
    companion object {
        lateinit var instance : App
        lateinit var db : LocalDatabase
        lateinit var itemDetailDao: ItemDetailDao
        lateinit var favoriteDao: FavoriteDao
        fun config(){
            db = LocalDatabase.getInstance(instance.applicationContext)!!
            itemDetailDao = db.itemDetailDao()
            favoriteDao = db.favoriteDao()
        }
    }

    override fun onCreate() {
        super.onCreate()
        Notification.createChannel(this)
        config()
    }
}
//db를 등록해놔서 context없이 전역으로 App.itemDetailDao.insert() 이렇게 접근가능하다

<application
        android:name=".App"
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher_foreground"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/Theme.AppCompat.Light.NoActionBar"
        android:usesCleartextTraffic="true">
//Manifest.xml에 name 등록해줘야한다.
```
### RecyclerView(data binding으로 사용하는 방법)
- view에서 adapter 객체를 만들어주고 recyclerview와 연결해준다
```kotlin
with(binding){
    rvProduct.adapter = OrderListAdapter()
}
//with쓰면 해당블록내부에서는 binding 안써줘도된다.
```
- xml에서 binding adapter이용해서 viewModel의 item 연결해주기
```kotlin=
    <data>
        <variable
            name="viewModel"
            type="com.example.starbuckst.ui.order.OrderViewModel" />

        <import type="android.view.View"/>
    </data>
    
<androidx.recyclerview.widget.RecyclerView
            android:id="@+id/rv_product"
            android:layout_width="match_parent"
            android:layout_height="0dp"
            app:items="@{viewModel.itemList}"
            app:layoutManager="androidx.recyclerview.widget.LinearLayoutManager"
            app:layout_constraintTop_toBottomOf="@id/tv_title"
            app:layout_constraintEnd_toStartOf="parent"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintBottom_toBottomOf="parent"/>
//fragment_orderList.xml -> 여기서는 viewModel variable로 선언하기
@BindingAdapter("app:items")
fun setProductItems(recyclerView: RecyclerView, items: List<ItemDetail>?) {
    items ?: return
    println("setProductItems!!@@")
    items.forEach{println(it)}
    (recyclerView.adapter as OrderListAdapter).loadOrderItemList(items)
}
//binding adapter

fun loadOrderItemList(items : List<ItemDetail>) {
        val oldData = orderItems

        val diffResult = DiffUtil.calculateDiff(object: DiffUtil.Callback(){
            override fun getOldListSize(): Int {
                return oldData.size
            }

            override fun getNewListSize(): Int {
                return items.size
            }

            override fun areItemsTheSame(oldItemPosition: Int, newItemPosition: Int): Boolean {
                return oldData[oldItemPosition].koreanName == items[newItemPosition].koreanName
            }

            override fun areContentsTheSame(oldItemPosition: Int, newItemPosition: Int): Boolean {
                return oldData[oldItemPosition] == items[newItemPosition]
            }
        })
        orderItems = items.toMutableList()
        diffResult.dispatchUpdatesTo(this)
    }
//OrderListAdapter.kt 
//adapter에서 diffUtil구현하기(live data 갱신하기 위해)
//DiffUtil대신에 ListAdapter사용해도 사용해도 된다고한다
// https://developer.android.com/codelabs/kotlin-android-training-diffutil-databinding
```
- adapter 및 viewHolder 만들기
```kotlin
    <data>
        <variable
            name="itemDetail"
            type="com.example.starbuckst.ui.order.model.ItemDetail" />
    </data>
    
    <de.hdodenhof.circleimageview.CircleImageView
            android:id="@+id/cv_thumbnail"
            android:layout_width="120dp"
            android:layout_height="120dp"
            android:layout_margin="20dp"
            android:src="@color/light_gray"
            app:firstUrl="@{itemDetail.imagePath}"
            app:secondUrl="@{itemDetail.imgUploadPath}"
            app:layout_constraintTop_toTopOf="parent"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintBottom_toBottomOf="parent"/>
            
    //order_item.xml
    //여기서는 들어갈 데이터만 variable로 선언해주기
    
    fun bindCategory(fragment: Fragment, item: ItemDetail) {
        binding.itemDetail = item
    //OrderItemViewHolder.kt에서 xml의 variable이랑 연결만해주면된다.
    
    companion object {
        fun from(parent: ViewGroup): OrderItemViewHolder {
            return OrderItemViewHolder(
                OrderItemBinding.inflate(
                    LayoutInflater.from(parent.context),
                    parent,
                    false
                )
            )
        }
    }
    //OrderItemViewHolder.kt에서 이렇게 작성해줘서
    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): OrderItemViewHolder {
        return OrderItemViewHolder.from(parent)
    }
    //OrderListAdapter.에서 이렇게 return해줘서 viewholder에서 binding사용할 수 있게 해준다
```
- 마지막으로 activity나 fragment에서 liveData의 값의 변경을 observe할 수 있게 해준다
```kotlin
 binding.lifecycleOwner = this
```
### Room(Database)
- Database사용하기위한 라이브러리
- DB인스턴스를 만든 뒤 DAO라는 인터페이스를 통해 query를 수행한다
- 선언은 상관없지만 query는 background thread에서 수행해야한다.
```kotlin=
@Database(entities = [Favorite::class, ItemDetail::class], version = 1)
abstract class LocalDatabase : RoomDatabase() {
    abstract fun favoriteDao() : FavoriteDao
    abstract fun itemDetailDao() : ItemDetailDao
    companion object {
        private var instance : LocalDatabase? = null

        @Synchronized
        fun getInstance(context: Context) : LocalDatabase? {
            if(instance == null) {
                synchronized(LocalDatabase::class.java) {
                    instance = Room.databaseBuilder(
                        context.applicationContext,
                        LocalDatabase::class.java,
                        "local-database"
                    ).build()
                }
            }
            return instance
        }
    }
}
//LocalDatabase.kt
//Entity를 여러개 둘 수 있다

@Dao
interface FavoriteDao {
    @Insert
    fun insertFavorite(vararg favorite : Favorite)

    @Delete
    fun deleteFavorite(favorite: Favorite)

    @Query("SELECT * FROM favorites")
    fun selectFavorites() : List<Favorite>?

    @Query("SELECT * FROM favorites WHERE titleKR = :krStr")
    fun selectCertainFavorite(krStr: String) : Favorite?
}
//FavoriteDao.kt
//인터페이스 정의

@Entity(tableName = "favorites")
data class Favorite(
    @PrimaryKey val titleKR : String,
    val titleENG : String,
    val price : String,
    val imageUrl : String,
    val productCD : String
)
//Favorite.kt
//entity 정의. data class만들고 Entity annotation만 붙여주면된다.

val db = LocalDatabase.getInstance(instance.applicationContext)!!
val itemDetailDao = db.itemDetailDao()
val favoriteDao = db.favoriteDao()
//이렇게 선언한 뒤
CoroutineScope(Dispatchers.IO).launch{
    favoriteDao.deleteFavorite(favoriteTemp)
}
//이렇게 사용하면된다.

```
### Data binding
```kotlin
    <data>
        <variable
            name="viewModel"
            type="com.example.starbuckst.ui.home.HomeViewModel" />

        <import type="android.view.View"/>
    </data>
    //이렇게 View를 import해줘야만
    <ProgressBar
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:visibility="@{viewModel.eventTask ? View.GONE : View.VISIBLE}"
                    app:layout_constraintStart_toStartOf="parent"
                    app:layout_constraintEnd_toEndOf="parent"
                    app:layout_constraintTop_toTopOf="@id/rv_event_list"
                    app:layout_constraintBottom_toBottomOf="@id/rv_event_list"/>
    //다음과 같이 View.GONE을 쓸 수 있다.
```
### LifeCycleOwner, LifeCycleObserver란
#### LiveData란
- LifeCycle을 알고있으며 데이터를 들고있는 클래스. LifeCycleOwner의 LifeCycle에 따라 데이터를 처리하여 여러 이점 확보. 메모리 누수 방지, 데이터 유지 + 실시간 반영 ..
#### LifeCycleOwner란
- 안드로이드 생명주기를 알고있는 클래스.
- 메소드가 getLifeCycle()밖에 없는 단일 메소드 인터페이스 클레스이며 activity, fragment에서 이를 상속한다. 즉 activity, fragment는 lifeCycleOwner
### LiveData 동작방식
- LiveData는 LifeCycleOwner가 START나 RESUME 상태에 다다르면 활성화 상태가 되어, 할당된 값이 변할 경우 observe 메소드로 인해 참조하고 있는 Observer의 onChanged 메소드를 발동시켜 이벤트를 전달한다. 
- 또한 LifeCycleOwner가 START나 RESUME이 아닌 상태가 되면, 비활성화되어 이벤트를 전달하지 않는다. 
- LifeCycleOwner가 Destory되게 되면 할당된 Observer 역시 Destory된다.(LiveData 사용 이점)
- LifeCycleOwner가 Destroy되지 않는 한 할당된 Observer를 강한 참조한다.
- 이미 LifeCycleOwner가 있는데도 불구하고 또다른 LifeCycleOwner를 참조시킬 경우 IllegalArgumentException이 발생한다.
### LiveData 사용 이점
- 메모리 누수(memory leak) 방지
    - LifeCycleOwner가 Destoryed되면 자신과 Observer 역시 Clean-up 된다.
- LifeCycle Stop에 따른 Crash 방지
    - LifeCycleOwner가 BackStack으로 들어가 비활성화 상태일 경우, 별다른 조치를 해주지 않으면 크래쉬가 날 수 있는 상황을 미연에 방지해준다.
- LifeCycleOwner 재시작 시 active상태임을 포착하고 자신이 들고있는 데이터를 바로 넘긴다.
    - 다시 활성화 상태로 돌아왔을 때 특별한 호출을 따로 해주지 않아도, 가장 마지막인 최신 데이터를 갱신해주므로 편하다.
- 즉 위의 모든 장점의 결론은 개발자의 실수를 줄여준다는 것이다

### 기타
- xml에서 tool:를 쓰면 디자인에서는 볼 수 있지만 실제 코드에는 반영되지 않게 할 수 있다.
- data binding을 사용해서 recyclerview의 데이터를 보여주는 경우 Spannable은 viewHolder의 bind에서 처리해줄 수 있다 or binding adapter에서 작성가능

## Review
###  서로다른 activity간의 viewModel공유는 옳을까?
서로 역할이 다른 액티비티간의 공유를 뷰모델을 통해 할 수 있을까? 라는
생각을 여러분이 하고 계신 것 같아서 같이 답변 드려봅니다.
뷰모델은 뷰에 대한 상태를 캡슐화 하고 UI 비즈니스 로직을 수행하게 됩니다.
여기서 뷰를 액티비티라고 볼 수 있을 텐데요.
뷰모델이 여러개의 뷰와 1:N 구조를 가질 수 있다고 하는데 이 말은 어떠한 UI를 보여주는데 있어서 필요한 로직과 UI 상태를 여러번 만들 필요 없이 동일한 뷰모델을 사용할 수 있다는 의미로써
"뷰모델을 공유한다" 와는 전혀 다른 애기입니다.
뷰모델을 공유한다는 의미는 서로 다른 뷰들에 대해 로직과 책임을 공유한다는 말이라 정말 공유가 필요한지
적절한 인터페이스로 관심사를 분리하는게 맞는지를 고민해야합니다.
한 예로 Fragment가 보여주는 정보를 액티비티에서도 있기 때문에 공유해서 쓰지라고 쉽게 생각할 수 있는데
이 Fragment가 다른 액티비티에도 있어야 한다면 어떻게 해야 할까요?
그만큼 커플링이 깊게 붙어 버려서 객체지향 설계에 어려움이 생길 수 있습니다.
위에서는 개념과 역할..에 대한 이론적인 내용이라 무슨소리야? 라고 하실 수도 있어서 일단은 그런가보다 하고 봐주셔도 됩니다. 나중에라도 이 말이 무슨 의미인지 아실 수 있는 경험을 꼭 하게 될거라 그 때 다시 생각 해 보셔도 됩니다.
기능적인 부분에서 뷰모델은 하나의 액티비티 아래에 여러개의 프래그먼트간의 공유하는 기준을 제공합니다.
그러기 때문에 서로 다른 액티비티간 뷰모델로 공유하는건 옳지 않습니다.
