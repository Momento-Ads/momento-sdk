
목차
=================
- [목차](#목차)
  - [시작하기](#시작하기)
  - [SDK 가이드](#SDK-가이드)
    - [Momento SDK 추가하기](#Momento-SDK-추가하기)
    - [Initialize SDK](#Initialize-SDK)
    - [전면(인터스티셜) 광고](#전면인터스티셜-광고)
    - [리워드 비디오(보상형 비디오) 광고](#리워드-비디오보상형-비디오-광고)
    - [배너 광고](#배너-광고)
    - [네이티브 광고](#네이티브-광고)
    - [Momento 광고 연동 예시](#momento-광고-연동-예시)
    - [에러코드](#에러코드)
  - [Ads.txt, App-ads.txt 적용하기](#adstxt-app-adstxt-적용하기)
  - [타사 미디에이션 SDK 활용 시 어댑터 적용가이드](#타사-미디에이션-sdk를-활용하고-momento-android-어댑터-적용)
  
## 시작하기
1. contact@momento.team 을 통해 Momento에 연락주시고, 계약을 완료합니다.
2. Momento에서 unit을 생성해 드리고 appid 및 unitid를 발급해드립니다.
3. 아래의 내용에 따라 Momento SDK를 추가하면서 발급 받으신 appid를 및 unitid를 넣어줍니다.
4. 테스트 연동을 진행하고 상호간에 기술적인 문제가 없는지 확인합니다.
5. 라이브 연동을 진행합니다.
6. 문의사항이 있으실 경우 contact@momento.team을 통해 연락주시면 도와드리겠습니다. 

## SDK 가이드

### Momento SDK 추가하기
>

- SDK Import(외부 배포시)
    - build.gradle(project) 
    - <b>gradle 7이상인 경우 setting.gradle에 탑재</b>
    
	```js
      allprojects {
	  repositories {
	    ...
	    maven { url 'https://jitpack.io' }
	  }
	}
	```

    
- build.gradle(app)
    - 의존성 추가

		```js
      dependencies {      
		implementation 'com.github.momento-ads:momento-android-sdk:1.0.4'
      }
		```
    
- AndroidManifest 설정
    - YOUR_MOMENTO_APP_ID 대신 Momento에게서 전달 받으신 Momento APP_ID를 입력해주세요

		```xml
		<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
		<uses-permission android:name="android.permission.INTERNET"/>    
		<meta-data 
		android:name="com.momento.sdk.APPLICATION_ID"
		android:value="YOUR_MOMENTO_APP_ID"/> 

		<!-- Momento 내부 브라우저 관련 설정 -->
		<activity 
		android:name="com.momento.services.browser.MomentoBrowser"
		android:configChanges="keyboardHidden|orientation|screenSize"/>
		```

- Proguard 적용 시
    
	```
	    -keep class com.momento.ads.** { *; }		    
	```

### Initialize SDK
> 
- initialize() Method
    - `initialize()`
        - SDK를 initialize하기 위한 메소드
        
		```kotlin
		Momento.initialize(context: Context)

		Momento.initialize(context: Context, options: MomentoInitializeOption?)

		Momento.initialize(context: Context, listener: OnInitCompleteListener?)

		Momento.initialize(context: Context, listener: OnInitCompleteListener?, options: MomentoInitializeOption?)
		```
        
    - `MomentoInitializeOption`
        - SDK 초기화에 대한 옵션을 주기 위한 클래스 (Optional)
        
		```kotlin
		/* 랜딩 진행 시 타겟 브라우저를 설정하기 위한 메소드 */
		fun setTargetBrowser(vararg browsers: String): Builder

		/* 초기화 시 설정되어 있는 타겟 브라우저를 리턴하기 위한 메소드 */
		fun getTargetBrowser(): ArrayList<String>?

		/* 랜딩을 진행하지 않을 브라우저를 설정하기 위한 메소드 */
		fun setIgnoredBrowser(vararg browsers: String): Builder

		/* 초기화 시 설정되어 있는 랜딩을 진행하지 않을 브라우저를 리턴하기 위한 메소드 */
		fun getIgnoreBrowser(): ArrayList<String>?

		/* 유저의 전화번호를 저장하는 메소드 countrCode 예시(KR) */
		fun setPhoneNumber(context: Context, countryCode: String, phoneNumber: String)

		/* 리워드 비디오 광고로부터 리워드를 받기 위해 필수적으로 설정해주어야 하는 값 */
		fun setUserId(userId: String)
		```
        
    - 예시
        
		```kotlin
		val option = MomentoInitializeOption.Builder()
			.setTargetBrowser("TARGET_BROWSER_PACKAGE_NAME")
			.setIgnoredBrowser("IGNORED_BROWSER_PACKAGE_NAME")
			.build()

		val targetBrowsers = option.getTargetBrowser()
		val ignoredBrowsers = option.getIgnoreBrowser()

		Momento.setPhoneNumber(this@MainActivity,"KR","01012345678")
		```
        
    - `OnInitCompleteListener`
        - SDK 초기화 동작 완료 관련 콜백 처리를 위한 리스너 (Optional)
        
		```kotlin
		Momento.initialize(this, object : OnInitCompleteListener {
			/* 초기화 동작이 완료된 경우 호출 */
			override fun onInitComlete(status: InitCompleteStatus?)
			}
		)
		```
        
    - `InitCompleteStatus`
        - 초기화 동작에 대한 상태 값
        
		```kotlin
		enum class InitCompleteStatus {
			/* initialize 시 넘겨받은 Context가 null인 경우 */
			CONTEXT_NULL_ERROR,

			/* initialize 시 APPLICATION_ID가 null인 경우 */
			APP_ID_INVALID_ERROR,

			/* 기타 선언은 되어있지만 현재 사용하지 않는 Status */
			CONNECTING,
			CONNECT_FAIL,
			AUTHENTICATION_FAIL,
			INIT_SUCCESS
		}
		/* 초기화가 되어있다면 true를 반환함. 사용 예시(Momento.isInitialized) */
		val isInitialized: Boolean

		/* SDK Version이 14이상이라면 true를 반환함. 사용 예시(Momento.isSupported) */
		val isSupported: Boolean

		/* 전화번호가 저장되어있다면 true를 반환함. 사용 예시(Momento.isPhoneIdExisted) */
		val isPhoneIdExisted: Boolean

		/* 버전을 반환함. 사용 예시(Momento.isInitialized) */
		val version: String
		```
        
    - `DebugMode`
        - 테스트 모드 여부를 결정하는 함수 (Optional)
        
		```kotlin
		/* 디버그 모드를 false로 설정 (Default) */
		Momento.debugMode = false 

		**/* 디버그 모드를 true로 설정 */**
		/* 디버그 모드 설정 시 테스트 서버로 광고 요청 */
		Momento.debugMode = true
		```


### 전면(인터스티셜) 광고 
> 
- Implement the Listener
    - `FullScreenAdsListener`
    - FullScreen(Interstitial/Rewarded Video)광고 관련 콜백 처리를 위한 리스너
    
	    ```kotlin
	    MomentoFullScreenAds.setFullScreenAdsListener(object : FullScreenAdsListener {
			/* FullScreen 광고 로드를 성공한 경우 호출 */
		override fun onAdLoadSuccess(adUnitId: String)

			/* FullScreen 광고 로드를 실패한 경우 호출 */
		override fun onAdLoadFailed(adUnitId: String, error: MomentoError)

			/* FullScreen 광고 송출이 시작된 경우 호출 */
		override fun onAdStarted(adUnitId: String)

			/* FullScreen 광고를 클릭한 경우 호출 */
		override fun onAdClicked(adUnitId: String)

			/* FullScreen 광고가 닫힌 경우 호출 */
		override fun onAdClosed(adUnitId: String)

			/* FullScreen 광고 송출이 완료된 경우 호출 */
		override fun onAdCompleted(adUnitId: String)

			/* FullScreen 광고 송출이 실패한 경우 호출 */
		override fun onAdShowError(adUnitId: String, error: MomentoError)

			/* FullScreen 광고 송출이 완료되지 않고 종료된 경우 호출 */
			/* Rewarded Video 광고인 경우에만 호출 */
		override fun onAdIncompleted(adUnitId: String)

			/* Callback Url을 통한 리워드 제공이 완료된 경우 호출 */
			/* Rewarded Video 광고인 경우에만 호출 */
		override fun onRewardCompleted(adUnitId: String)

			/* Callback Url을 통한 리워드 제공이 실패한 경우 호출 */
			/* Rewarded Video 광고인 경우에만 호출 */
		override fun onRewardFailed(adUnitId: String, error: MomentoError)
		}
	    )
	    ```
    
- Load Interstitial Ad
    - FullScreen(Interstitial/Rewarded Video)광고의 로드를 위한 메소드
    
	    ```kotlin
	    MomentoFullScreenAds.loadAd(activity: Activity, adUnitId: String)
	    ```
    
- Show Interstitial Ad
    - FullScreen(Interstitial/Rewarded Video)광고의 송출을 위한 메소드
    
	    ```kotlin
	    MomentoFullScreenAds.showAd(adUnitId: String)
	    ```
    
- Check Ad Availability
    - FullScreen(Interstitial/Rewarded Video)광고의 유무 판별을 위한 메소드
    
	    ```kotlin
	    MomentoFullScreenAds.hasAd(adUnitId: String): Boolean
	    ```


### 리워드 비디오(보상형 비디오) 광고 
> 
- Implement the Listener
    - `FullScreenAdsListener`
    - FullScreen(Interstitial/Rewarded Video)광고 관련 콜백 처리를 위한 리스너
    - *유의사항:* <b>setFullScreenAdsListener로 리스너를 초기화 할 때마다 해당 리스너를 참조하게 됨</b>
    
	    ```
	    public class MomentoNativeView extends ConstraintLayout {

		private OnAdNativeListener mNativeListener;
		public MomentoNative mMomentoNativeBanner;
		private String mAdUnitId;
		private final Context mContext;

		private ImageView iv_logo;
		private TextView tv_title;
		private TextView tv_description;
		private ImageView iv_main_image;
		private TextView tv_button;
		private ImageView iv_info;
		private ConstraintLayout cl_parent;

		public MomentoNativeView(Context context, String adUnitId, OnAdNativeListener listener) {
		    super(context);
		    mContext = context;
		    mAdUnitId = adUnitId;
		    mNativeListener = listener;
		    initView();
		}

		public void initView() {
		    String infService = Context.LAYOUT_INFLATER_SERVICE;
		    LayoutInflater li = (LayoutInflater) getContext().getSystemService(infService);
		    View view = li.inflate(R.layout.layout_mmt_native_banner, this, false);
		    iv_logo = view.findViewById(R.id.iv_logo);
		    tv_title = view.findViewById(R.id.tv_title);
		    tv_description = view.findViewById(R.id.tv_description);
		    iv_main_image = view.findViewById(R.id.iv_main_image);
		    tv_button = view.findViewById(R.id.tv_button);
		    iv_info = view.findViewById(R.id.iv_info);
		    cl_parent = view.findViewById(R.id.cl_parent);

		    initAd(view);
		    addView(view);
		}

		private void initAd(View view) {
		    mMomentoNativeBanner = new MomentoNative(mContext, mAdUnitId, mNativeListener);
		    NativeViewBinder binder = new NativeViewBinder.Builder(view)
			    .titleTextViewId(tv_title.getId())
			    .logoImageId(iv_logo.getId())
			    .mainImageId(iv_main_image.getId())
			    .textTextViewId(tv_description.getId())
			    .callToActionButtonId(tv_button.getId())
			    .adInfoImageId(iv_info.getId())
			    .build();

		    mMomentoNativeBanner.setNativeViewBinder(binder);
		}

		public void loadAd(){
		    if (mMomentoNativeBanner != null){
			mMomentoNativeBanner.loadAd();
		    }
		}

		public void reload(){
		    if (mMomentoNativeBanner != null){
			mMomentoNativeBanner.reload();
		    }
		}

		public void destroy(){
		    if (mMomentoNativeBanner != null){
			mMomentoNativeBanner.destroy();
		    }
		}

		public boolean isReady() {
		    if (mMomentoNativeBanner != null){
			return mMomentoNativeBanner.isReady();
		    } else {
			return false;
		    }
		}

		public void sendTimeoutEvent() {
		    if (mMomentoNativeBanner != null){
			mMomentoNativeBanner.sendTimeoutEvent();
		    }
		}

		public void setLogCallback(MomentoLogCallback momentoLogCallback) {
		    mMomentoNativeBanner.setLogCallback(momentoLogCallback);
		}
	    }
	    ```
    
	    ```kotlin
	    /* Sample Code */
	    class VideoActivity : AppCompatActivity(), FullScreenAdsListener {

			override fun onCreate(savedInstanceState: Bundle?) {
				MomentoFullScreenAds.setFullScreenAdsListener(this)
		}
	    /* FullScreen 광고 로드를 성공한 경우 호출 */
		override fun onAdLoadSuccess(adUnitId: String)

			/* FullScreen 광고 로드를 실패한 경우 호출 */
		override fun onAdLoadFailed(adUnitId: String, error: MomentoError)

			/* FullScreen 광고 송출이 시작된 경우 호출 */
		override fun onAdStarted(adUnitId: String)

			/* FullScreen 광고를 클릭한 경우 호출 */
		override fun onAdClicked(adUnitId: String)

			/* FullScreen 광고가 닫힌 경우 호출 */
		override fun onAdClosed(adUnitId: String)

			/* FullScreen 광고 송출이 완료된 경우 호출 */
		override fun onAdCompleted(adUnitId: String)

			/* FullScreen 광고 송출이 실패한 경우 호출 */
		override fun onAdShowError(adUnitId: String, error: MomentoError)

			/* FullScreen 광고 송출이 완료되지 않고 종료된 경우 호출 */
			/* Rewarded Video 광고인 경우에만 호출 */
		override fun onAdIncompleted(adUnitId: String)

			/* Callback Url을 통한 리워드 제공이 완료된 경우 호출 */
			/* Rewarded Video 광고인 경우에만 호출 */
		override fun onRewardCompleted(adUnitId: String)

			/* Callback Url을 통한 리워드 제공이 실패한 경우 호출 */
			/* Rewarded Video 광고인 경우에만 호출 */
		override fun onRewardFailed(adUnitId: String, error: MomentoError)
		}
	    }
	    ```
    
- Load Rewarded Video Ad
    - FullScreen(Interstitial/Rewarded Video)광고의 로드를 위한 메소드
    
	    ```kotlin
	    MomentoFullScreenAds.loadAd(activity: Activity, adUnitId: String)
	    ```
    
- Show Rewarded Video Ad
    - FullScreen(Interstitial/Rewarded Video)광고의 송출을 위한 메소드
    
	    ```kotlin
	    MomentoFullScreenAds.showAd(adUnitId: String)
	    ```
    
- Check Ad Availability
    - FullScreen(Interstitial/Rewarded Video)광고의 유무 판별을 위한 메소드
    
	    ```kotlin
	    MomentoFullScreenAds.hasAd(adUnitId: String): Boolean
	    ```
    
- Set UserID (Optional)
    - Callback Url로 리워드 제공을 할 경우 전달할 유저의 식별자를 설정하기 위한 메소드
        - <span style="color:red"><u>**UserId가 없으면 Callback Url 요청 불가**<span></u>
    - initialize 시에 함께 설정 (Recommend)
    
	    ```kotlin
	    Momento.setUserId(userId: String)
	    ```
    

### 배너 광고
> 
- Implement the Listener
    - `BannerListener`
    - Banner(50/100/250) 광고 관련 콜백 처리를 위한 리스너
    
	    ```kotlin
	    bannerListener = object : BannerListener(){

			// Banner 광고 Load 성공한 경우에 호출
			override fun onAdLoaded()

			// Banner 광고 Load 실패한 경우에 호출
		override fun onAdFailed(error: MomentoError)

			// Banner 광고 클릭한 경우에 호출
		override fun onAdClicked()

			// Banner 광고 Show 성공한 경우에 호출
		override fun onAdShowed(isShown: Boolean)

		}
	    ```
    
- Banner Load를 위한 필수 설정 요소
    - Banner size(50/100/250) = BannerSize 클래스의 상수인 `BANNER_320_50`,`BANNER_320_100`,`BANNER_300_250` 으로 설정 가능
    - unitId = 배너 광고 사이즈에 맞는 각 Unit ID를 삽입
    - banner Listener
    
	    ```kotlin
	    val momentoBannerView = BannerView(this)
	    momentoBannerView.apply {
		size = BannerSize.(Your Banner Size)
		unitId = "Your UnitID"
		bannerListener = object: BannerListener() {
			~ ~ ~
		}
	    ```
    
- Show Banner Ad
    - Banner 광고의 송출을 위한 메소드
    
	    ```kotlin
	    //Banner 광고를 Load 하는 메소드
	    fun loadAd()
	    //Banner 광고를 Destroy 하는 메소드
	    fun destroy()
	    //Banner 광고 Load를 재시도 하는 메소드
	    fun reload()

	    //Example
	    val momentoBannerView = BannerView(this)
	    momentoBannerView.apply {
		size = BannerSize.(Your Banner Size)
		unitId = "Your UnitID"
		bannerListener = object: BannerListener() {
			~ ~ ~
		}
	    (YourBannerContainerView).addView(momentoBannerView)
	    momentoBannerView.loadAd()
	    }
	    ```
    

- Check Ad Availability
    - Banner 광고의  위한 메소드
    
	    ```kotlin
	    fun isReadyToLoadAd() : Boolean
	    ```
    

### 네이티브 광고 
> 
- Implement the Listener
    - `OnAdNativeListener`
    - Native 광고 관련 콜백 처리를 위한 리스너
    
	    ```kotlin
	     nativeListener = object : OnAdNativeListener {

			//Native 광고를 Load 했을 시
			override fun onLoaded() 

			//Native 광고를 Show 했을 시
		  override fun onShow()

			//Native 광고를 Click 했을 시 
			 override fun onClick() 

			//Native 광고를 Load 실패 했을 시
		  override fun onFailed(error: MomentoError) 


	       }
	    ```
    
- Show Native Ad
    - Native 광고의 송출을 위한 메소드
    
	    ```kotlin
	    //Native 광고를 Load 하는 메소드
	    fun loadAd()
	    //Native 광고를 Destroy 하는 메소드
	    fun destroy()
	    //Native 광고 Load를 재시도 하는 메소드
	    fun reload()

	    //Example
	    momentoNativeBannerView =
		  MomentoNativeView(this, "UnitID", object : OnAdNativeListener{

		  override fun onClick(){}

		  override fun onFailed(error: MomentoError) {}

		  override fun onLoaded() {}

		  override fun onShow() {}

		    })

	    momentoNativeBannerView.loadAd()
	    ```
    
- MomentoNativeView Sample
    - Native 뷰를 직접 구성하여 각 뷰 요소에 맞는 데이터를 보여주도록 구성해야 합니다.
    
	    ```java
	    class MomentoNativeView(
		private val mContext: Context,
		private val mAdUnitId: String,
		private val mNativeListener: OnAdNativeListener
	    ) : ConstraintLayout(mContext) {

		private var mMomentoNativeBanner: MomentoNative? = null
		private var iv_logo: ImageView? = null
		private var tv_title: TextView? = null
		private var tv_description: TextView? = null
		private var iv_main_image: ImageView? = null
		private var tv_button: TextView? = null
		private var iv_info: ImageView? = null
		private var cl_parent: ConstraintLayout? = null

		private fun initView() {
		    val infService = Context.LAYOUT_INFLATER_SERVICE
		    val li = context.getSystemService(infService) as LayoutInflater
		    val view = li.inflate(R.layout.layout_mmt_native_banner, this, false)
		    iv_logo = view.findViewById(R.id.iv_logo)
		    tv_title = view.findViewById(R.id.tv_title)
		    tv_description = view.findViewById(R.id.tv_description)
		    iv_main_image = view.findViewById(R.id.iv_main_image)
		    tv_button = view.findViewById(R.id.tv_button)
		    iv_info = view.findViewById(R.id.iv_info)
		    cl_parent = view.findViewById(R.id.cl_parent)
		    initAd(view)
		    addView(view)
		}

		private fun initAd(view: View) {
		    try {
			mMomentoNativeBanner = MomentoNative(mContext, mAdUnitId, mNativeListener)
			val binder = NativeViewBinder.Builder(view)
			    .titleTextViewId(tv_title!!.id)
			    .logoImageId(iv_logo!!.id)
			    .mainImageId(iv_main_image!!.id)
			    .textTextViewId(tv_description!!.id)
			    .callToActionButtonId(tv_button!!.id)
			    .adInfoImageId(iv_info!!.id)
			    .build()
							//해당 예시처럼 NativeViewBinder.Builder를 통해 binder를 생성하여 MomentoNativeBannerView에 set해주어야 함.
			mMomentoNativeBanner!!.setNativeViewBinder(binder)
		    } catch (e: Exception) {
			e.printStackTrace()
		    }
		}

		fun loadAd() {
			mMomentoNativeBanner?.loadAd()
		}

		fun reload() {
			mMomentoNativeBanner?.reload()
		}

		fun destroy() {
			mMomentoNativeBanner?.destroy()
		}

		val isReady: Boolean
		    get() = if (mMomentoNativeBanner != null) {
			mMomentoNativeBanner!!.isReady
		    } else {
			false
		    }

		fun sendTimeoutEvent() {
		    if (mMomentoNativeBanner != null) {
			mMomentoNativeBanner?.sendTimeoutEvent()
		    }
		}

		fun setLogCallback(momentoLogCallback: MomentoLogCallback?) {
		    mMomentoNativeBanner?.setLogCallback(momentoLogCallback)
		}

		init {
		    initView()
		}
	    }
	    ```
    

### Momento 광고 연동 예시
```kotlin
class MainActivity : AppCompatActivity() {

	override fun onCreate(savedInstanceState: Bundle?) {
			super.onCreate(savedInstanceState)
			/* If you set debug mode */
			Momento.debugMode = true

			/* Initialize SDK */
			Momento.initialize(this)

			/* Set a FullScreenAdsListener */
      MomentoFullScreenAds.setFullScreenAdsListener(object : FullScreenAdsListener {
	  override fun onAdLoadSuccess(adUnitId: String) {

	  }

	  override fun onAdLoadFailed(adUnitId: String, error: MomentoError) {

	  }

	  override fun onAdStarted(adUnitId: String) {

	  }

	  override fun onAdClicked(adUnitId: String) {

	  }

	  override fun onAdClosed(adUnitId: String) {

	  }

	  override fun onAdCompleted(adUnitId: String) {

	  }

	  override fun onAdIncompleted(adUnitId: String) {

	  }

	  override fun onRewardCompleted(adUnitId: String) {

	  }

	  override fun onRewardFailed(adUnitId: String, error: MomentoError) {

	  }

	  override fun onAdShowError(adUnitId: String, error: MomentoError) {

	  }
      })

			/* Load a fullscreen ad */
      MomentoFullScreenAds.loadAd(this, "YOUR_PLACEMENT_UNIT_ID")

			/* Check and show a fullscreen ad */
			if(MomentoFullScreenAds.hasAd("YOUR_PLACEMENT_UNIT_ID") {
		MomentoFullScreenAds.showAd("YOUR_PLACEMENT_UNIT_ID")
			}
	}
}
```
	
### 에러코드

| Name | Code | Description |
| --- | --- | --- |
| CODE_OK | 0 | 광고 요청 성공 |
| CODE_NO_AD | 20401 | 광고 물량이 없음 |
| CODE_MISSING_PARAMS | 40001 | 잘못된 파라미터로의 광고 요청 |
| CODE_UNAUTHORIZED_APP_ID | 40101 | 유효하지 않은 APP ID로의 광고 요청 |
| CODE_UNAUTHORIZED_UNIT_ID | 40102 | 유효하지 않은 UNIT ID로의 광고 요청 |
| CODE_NOT_FOUND_END_POINT | 40401 | 존재하지 않는 END POINT |
| CODE_TIME_OUT | 40801 | 광고 요청에 대한 타임아웃 |
| CODE_SERVER_ERROR | 50001 | 모멘토 서버 내부 오류 발생 |
| INTERNAL_CODE_NO_INPUT_UNIT_ID | 90001 | UNIT ID를 설정하지 않음 |
| INTERNAL_CODE_NO_INPUT_AD_SIZE | 90002 | AdSize를 설정하지 않음 |
| INTERNAL_CODE_NO_PARENT_LAYOUT | 90003 | View의 Parent View가 없음 |
| INTERNAL_CODE_NO_GOOGLE_AD_ID | 90004 | 유저의 AdId를 가져올 수 없음 |
| INTERNAL_CODE_NO_CREATE_TIMEOUT_LAYOUT | 90005 | 배너 노출 시 타임아웃 발생 |
| INTERNAL_CODE_WEBVIEW_NOT_INSTALLED | 90006 | 디바이스에서 WebView를 찾을 수 없음 |
| INTERNAL_CODE_WEBVIEW_NOT_INITIALIZED | 90007 | WebView 객체 초기화 시에 오류 발셍 |
| INTERNAL_CODE_NETWORK_FAILED | 90008 | 네트워크 문제가 발생 |
| INTERNAL_CODE_RESPONSE_NONE | 90009 | 광고 요청에 대한 Response가 없음 |
| INTERNAL_CODE_ALREADY_FAILED_AD | 90010 | 이미 송출에 실패한 광고 데이터인 경우 |
| INTERNAL_CODE_AD_IS_UNAVAILABLE | 90011 | 송출할 수 없는 광고 데이터인 경우 |
| INTERNAL_CODE_CONTEXT_IS_NULL | 90012 | Context 객체가 NULL인 경우 |
| INTERNAL_CODE_ACTIVITY_IS_NULL | 90013 | Activity 객체가 NULL인 경우 |
| INTERNAL_CODE_FULLSCREEN_AD_ADAPTER_IS_NULL | 90014 | 내부 로직에서 어뎁터를 찾을 수 없는 경우 |
| INTERNAL_CODE_CONFIGURATION_IS_NOT_READY | 90015 | 내부 로직에서 광고 송출을 위한 객체들을 찾을 수 없는 경우 |
| INTERNAL_CODE_FULLSCREEN_AD_PLAY_ERROR | 90016 | 비디오 광고 송출에 오류가 발생한 경우 |
| INTERNAL_CODE_REWARDED_DATA_IS_NULL | 90017 | 리워드 데이터가 NULL인 경우 |
| INTERNAL_CODE_RENDER_PROCESS_GONE_WITH_CRASH | 90018 | MRAID 광고 송출에 Crash 문제가 생긴 경우 |
| INTERNAL_CODE_RENDER_PROCESS_GONE_WITH_UNSPECIFIED | 90019 | MRAID 광고 송출에 기타 이유로 문제가 생긴 경우 |
| INTERNAL_CODE_MRAID_LOAD_ERROR | 90020 | MRAID 광고 송출을 실패한 경우 |
| INTERNAL_CODE_REWARD_FAILED | 90021 | Callback Url을 통한 리워드 제공에 실패한 경우 |

## FAQ
### INTERNAL_CODE_NO_GOOGLE_AD_ID 가 발생합니다.
구글광고아이디 (ADID) 를 수집하기 위해 별도로 아래의 퍼미션을 추가해주셔야 합니다.
```XML
<manifest>
...
    <uses-permission android:name="com.google.android.gms.permission.AD_ID" /> 
...
</manifest>
```

## Ads.txt, App-ads.txt 적용하기
Ads.txt, App-ads.txt를 적용해주시면 광고 수익 향상에 도움이 될 수 있습니다. <br></br>
Ads.txt, App-ads.txt에 대해서, 그리고 적용하는 방법은 [Ads.txt, App-ads.txt 가이드](adstxt.md)를 통해 확인할 수 있습니다. <br></br>


## 타사 미디에이션 SDK를 활용하고, Momento Android 어댑터 적용
Admob을 미디에이션 SDK로 활용하시면서 Momento의 광고를 받으려면 [Admob 어댑터](momentoadmob.md)를 확인해주세요. <br></br>
Applovin을 미디에이션 SDK로 활용하시면서 Momento의 광고를 받으려면 [Applovin 어댑터](momentoapplovin.md)를 확인해주세요. <br></br>
Ironsource를 미디에이션 SDK로 활용하시면서 Momento의 광고를 받으려면 [Ironsource 어댑터](momentois.md)
를 확인해주세요. <br></br>
