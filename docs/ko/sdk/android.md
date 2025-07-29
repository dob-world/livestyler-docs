# LiveStyler SDK for Android

Android용 애플리케이션에 LiveStyler 기능을 적용하기 위한 SDK입니다.
SDK는 카메라를 초기화하여 촬영된 영상을 전송하고 영상처리가 완료된 영상을 수신하여 보여주는 기능을 제공합니다.

## 시작하기

### 요구사항

- Android 9.0 이상
- AGP 8.0 이상
- Kotlin 1.7.21 이상

### 주요기능

- 시그널 채널을 통한 영상 변환을 제어
- 카메라의 영상을 WebRTC 서버로 전송
- 필터가 적용된 영상을 수신

### 설치

#### Gradle

```groovy
// root/build.gradle
allprojects {
    repositories {
        ...
        // 저장소 준비 중입니다.
        maven { url "https://repository.livestyler.io/..." }
        ...
    }
}
```

```groovy
// app/build.gradle
dependencies {
    // 저장소 준비 중입니다.
    implementation "ai.livestyler:LiveStylerSDKAndroid:latest.release"
}
```

그리고 다음 명령을 실행합니다:

```bash
$ ./gradlew build --refresh-dependencies
```

## 사용방법

### 쉬운 사용

기능이 사전에 구현된 `StreamFragment` 사용할 수 있습니다.

```kotlin
// 초기화
val args = Bundle().apply {
    putString("credential", "{credential}")                             // 관리자 페이지를 통해 발급 받은 인증 토큰
    putString("apiEndpoint", "{apiEndpoint}")                           // 서비스의 정보를 얻을 수 있는 API 서버
    putString("signalEndpoint", "{signalEndpoint}")                     // 백엔드와 인증 정보를 주고 받는 시그널 채널 엔드포인트 주소
    putParcelableArrayList(
        "serverEndpoints",                                              // STUN 서버와 TURN 서버를 설정, STUN 서버는 제공된 구글 STUN 사용 권장
        arrayListOf(
            Bundle().apply {
                putString("type", "stun")
                putString("endpoint", "stun:stun.l.google.com:19302")   // STUN 서버 주소
            },
            Bundle().apply {
                putString("type", "turn")
                putString("endpoint", "{turnEndpoint}")                 // TURN 서버 주소
                putString("username", "{username}")                     // TURN 서버 인증 정보
                putString("password", "{password}")                     // TURN 서버 인증 정보
            },
            Bundle().apply {
                putString("type", "turn")
                putString("endpoint", "{turnEndpoint}")                 // TURN 서버 주소
                putString("username", "{username}")                     // TURN 서버 인증 정보
                putString("secret", "{secret}")                         // TURN 서버 인증 정보
            }
        )
    )
    putString("iceTransportsType", "{iceTransportsType}")           // All, NoHost, Relay 중 하나의 값을 사용하여 Peep-to-peer 연결 방식을 지정
}

findNavController().navigate(R.id.action_HostFragment_to_StreamFragment, args = args)
```

### 직접 개발

쉬운 사용에서는 제공하지 않는 추가적인 기능, UI/UX의 임의 구현을 위해서는 직접 구현하는 것이 좋습니다.

사용 방법은 후술할 [주요 기능 명세](#주요-기능-명세)를 참고하여 주시기 바랍니다.

화면의 디자인과 기능을 변경하고자 하면 [`StreamFragment.kt` 파일](android-streamfragmentkt.md)과 [`fragment_stream.xml` 파일](android-fragmentstreamxml.md)을 참고하여 사용하세요.

세부적인 API 명세는 [Android APIs](reference-kotlin.md)를 참고하여 주시기 바랍니다.

## 주요 기능 명세

API를 사용하면 화면의 기능을 직접 만들어 구현할 수 있습니다.

### LiveStylerManager

```kotlin
// 초기화
// accessKey, signalEndpoint, serverEndpoints는 args를 만들때 사용한 값과 같은 값을 사용
val liveStylerManager: LiveStylerManager = LiveStylerManager(
    "{credential}",
    "{apiEndpoint}",
    "{signalEndpoint}",
    listOf( {serverEndpoints} ),
    "{iceTransportsType}"
)
```

#### onCreate()

초기화가 될 때 필요한 작업이 이루어집니다.

```kotlin
override fun onCreate(saveInstance?: Bundle) {
    super.onCreate(saveInstance)
    liveStyleManager.onCreate(requrieContext(), this)
}
```

#### onCreateView()

뷰가 생성되기 전에 필요한 작업이 이루어집니다.

```kotlin
override fun onCreateView(
    inflater: LayoutInflater, container: ViewGroup?,
    savedInstanceState: Bundle?
): View {
    _binding = FragmentStreamBinding.inflate(inflater, container, false)
    liveStylerManager.onCreateView(this, this)
    return binding.root
}
```

#### onViewCreated()

만들어진 뷰를 매니저로 전달합니다.
카메라 프리뷰 또는 렌더 프리뷰를 선택적으로 넣을 수 있습니다.

```kotlin
override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
    super.onViewCreated(view, savedInstanceState)
    liveStylerManager.onViewCreated(binding.preview, binding.renderView)
}
```

#### onPause()

카메라 캡처를 중단합니다.

```kotlin
override fun onPause() {
    super.onPause()
    liveStylerManager.onPause()
}
```

#### onResume()

카메라 캡처를 다시 시작합니다.
시그널 서버 연결 및 WebRTC 연결을 다시 설정합니다.

```kotlin
override fun onResume() {
    super.onResume()
    liveStylerManager.onResume()
}
```

#### onStop()

카메라 리소스를 릴리즈 반환합니다.
시그널 서버와 WebRTC 연결을 끊고 대기합니다.

```kotlin
override fun onStop() {
    liveStylerManager.onStop()
    super.onStop()
}
```

#### onDestroy()

시그널 서버 연결과 WebRTC 연결을 완전히 종료합니다.

```kotlin
override fun onDestroyView() {
    liveStylerManager.onDestroy()
    _binding = null
    super.onDestroyView()
}
```

#### changeFilter(String)

WebRTC 서버에 설정된 필터를 변경합니다.

```kotlin
liveStylerManager.changeFilter("{filter_id}")
```

- filter_id: API를 통해 전달 받은 필터 리스트 중에 변경을 원하는 필터의 ID

#### switchCamera(String)

전달 받은 카메라 ID로 전환합니다.
카메라 ID는 CameraManager를 통해 얻을 수 있습니다.

```kotlin
liveStylerManager.switchCamera("{camera_id}")
```

- camera_id: CamaraManager(안드로이드 시스템 도구)를 통해 얻은 카메라의 ID

#### updateFilterCategory()

API 서버에서 필터, 카테고리 리스트를 새로 받아 업데이트합니다.
필터 리스트는 시그널 서버에 접속하면 자동으로 업데이트됩니다.
갱신된 리스트는 SignalStateListener를 통해 전달됩니다.

```kotlin
liveStylerManager.updateFilterCategory()
```

#### changeModel(String)

전달 받은 모델 이름으로 필터 모델을 변경합니다.
모델 이름은 onReceivedFilterList() 콜백을 통해 얻은 필터의 정보(FilterCategoryData)에서 모델 이름을 찾아서 사용합니다.

```kotlin
liveStylerManager.changeModel("{model_name}")
```

- model_name: FilterCategoryData의 모델 이름

> Android SDK에는 API를 통해 모델 리스트를 가져오는 처리가 포함되어 있습니다.<br/>
> 하지만, SDK에 반영된 API는 LiveStyler 제공 서비스이므로 실제 서비스와 차이가 있을 수 있습니다.<br/>
> API는 서비스의 구성에 따라 달라질 수 있으므로 API 가이드를 참고하여 모델 리스트를 확인하여 사용하시기 바랍니다.