# LiveStyler SDK for iOS

## 시작하기

### 요구 사항

*   iOS 12.0 이상
*   Xcode 12.0 이상
*   Swift 5.0 이상

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

### 사용방법

#### 쉬운 사용

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

#### 직접 개발

쉬운 사용에서는 제공하지 않는 추가적인 기능, UI/UX의 임의 구현을 위해서는 직접 구현하는 것이 좋습니다.

사용 방법은 후술할 [주요 기능 명세](#주요-기능-명세)를 참고하여 주시기 바랍니다.

예제는 SDK로 제공되는 [StreamViewController의 소스코드](ios-streamviewcontroller.md)를 참고하여 주시기 바랍니다.


세부적인 API 명세는 [iOS APIs](reference-swift.md)를 참고하여 주시기 바랍니다.


#### 주요 기능 명세

##### LiveStylerManager

###### initialize() ...