# LiveStyler SDK for iOS

iOS용 애플리케이션에 LiveStyler 기능을 적용하기 위한 SDK입니다.
SDK는 카메라를 초기화하여 촬영된 영상을 전송하고 영상처리가 완료된 영상을 수신하여 보여주는 기능을 제공합니다.

## 시작하기

### 요구 사항

- iOS 12.0 이상
- Xcode 12.0 이상
- Swift 5.0 이상

### 주요기능

- 시그널 채널을 통한 영상 변환을 제어
- 카메라의 영상을 WebRTC 서버로 전송
- 필터가 적용된 영상을 수신

### 설치

#### CocoaPods

`Podfile`에 다음 라인을 추가합니다:

```ruby
pod 'LiveStylerSDK', :git => 'https://github.com/dob-world/LiveStylerSDKiOS.git', :tag => '0.0.1'
```

그리고 다음 명령을 실행합니다:

```bash
pod install
```

## 사용방법

### 쉬운 사용

기능을 테스트하기 위해 사전에 구현되어 있는 기능을 간단하게 적용하여 사용해 볼 수 있습니다.

```swift
import LiveStylerSDK

let contants = AppConstants(
    servers: Servers(
        credential: "{user_credential}",                // 관리자 콘솔에서 발급한 Access Token
        webSocketURL: "{signaling_endpoint}",           // 백엔드와 인증 정보를 주고 받는 시그널 채널 엔드포인트 주소
        stunServer: "stun:stun.l.google.com:19302",     // STUN 서버 주소
        turnServer: "{turn_server_endpoint}",           // TURN 서버 주소
        turnUsername: "{username}",                     // TURN 서버 인증 정보
        turnPassword: "{password}"                      // TURN 서버 인증 정보
    )
)

StreamViewController(appConstants: contants)
```

### 직접 개발

쉬운 사용에서는 제공하지 않는 추가적인 기능, UI/UX의 임의 구현을 위해서는 직접 구현하는 것이 좋습니다.

사용 방법은 후술할 [주요 기능 명세](#주요-기능-명세)를 참고하여 주시기 바랍니다.
예제는 SDK로 제공되는 [StreamViewController의 소스코드](ios-streamviewcontroller.md)를 참고하여 주시기 바랍니다.

세부적인 API 명세는 [iOS APIs](reference-swift.md)를 참고하여 주시기 바랍니다.


## 주요 기능 명세

### LiveStylerManager

```swift
/// 초기화

let contants = AppConstants(
    servers: Servers(
        credential: "{user_credential}",                // 관리자 콘솔에서 발급한 Access Token
        webSocketURL: "{signaling_endpoint}",           // 백엔드와 인증 정보를 주고 받는 시그널 채널 엔드포인트 주소
        stunServer: "stun:stun.l.google.com:19302",     // STUN 서버 주소
        turnServer: "{turn_server_endpoint}",           // TURN 서버 주소
        turnUsername: "{username}",                     // TURN 서버 인증 정보
        turnPassword: "{password}"                      // TURN 서버 인증 정보
    )
)

let manager = LiveStylerManager(appConstants: contants)
```

#### initialize()

초기화가 될 때 필요한 작업이 이루어집니다.

```swift
public override func viewDidLoad() {
    super.viewDidLoad()
    manager.initialize()
}
```

#### activate()

카메라 캡처를 시작합니다.

```swift
public override func viewWillAppear(_ animated: Bool) {
    super.viewWillAppear(animated)
    manager.activate()
}
```

####  deactivate()

카메라 캡처를 중단합니다.

```swift
public override func viewDidDisappear(_ animated: Bool) {
    super.viewDidDisappear(animated)
    manager.deactivate()
}
```

#### cleanup()

시그널 서버 연결과 WebRTC 연결을 완전히 종료하고 리소스를 반환합니다.

```swift
deinit {
    manager.cleanup()
}
```

#### switchCamera(cameraId: String)

전달 받은 카메라 ID로 전환합니다.
카메라 ID는 AVCaptureDevice를 통해 얻을 수 있습니다.

```swift
manager.switchCamera("{camera_id}")
```

- camera_id: AVCaptureDevice(iOS 시스템 도구)를 통해 얻은 카메라의 ID

#### changeModel(modelName: String)

전달 받은 모델 이름으로 필터 모델을 변경합니다.

```swift
manager.changeModel("{model_name}")
```

- model_name: FilterCategoryData의 모델 이름

> iOS SDK에는 API를 통해 모델 리스트를 가져오는 처리가 포함되어 있지 않습니다.<br/>
> API 가이드를 참고하여 모델 리스트를 확인하여 사용하시기 바랍니다.