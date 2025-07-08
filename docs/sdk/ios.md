# LiveStyler SDK for iOS

LiveStyler iOS SDK를 사용하면 iOS 애플리케이션에 LiveStyler의 강력한 기능을 통합할 수 있습니다. 이 문서는 SDK를 시작하고, 주요 기능을 활용하며, 일반적인 문제를 해결하는 데 도움이 되는 포괄적인 가이드를 제공합니다.

## 시작하기

LiveStyler iOS SDK를 프로젝트에 추가하고 설정하는 방법에 대해 알아봅니다.

### 요구 사항

*   iOS 12.0 이상
*   Xcode 12.0 이상
*   Swift 5.0 이상

### 설치

CocoaPods 또는 Swift Package Manager를 사용하여 LiveStyler iOS SDK를 설치할 수 있습니다.

#### CocoaPods

`Podfile`에 다음 라인을 추가합니다:

```ruby
pod 'LiveStylerSDK'
```

그리고 다음 명령을 실행합니다:

```bash
pod install
```

#### Swift Package Manager

Xcode에서 다음 단계를 따릅니다:

1.  `File` > `Add Packages...`를 선택합니다.
2.  검색 바에 다음 URL을 입력합니다: `https://github.com/livestyler/livestyler-ios-sdk.git`
3.  `Add Package`를 클릭합니다.

### 초기화

SDK를 사용하기 전에 애플리케이션 델리게이트에서 LiveStyler SDK를 초기화해야 합니다.

```swift
import LiveStylerSDK
import UIKit

@UIApplicationMain
class AppDelegate: UIResponder, UIApplicationDelegate {

    func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
        LiveStyler.initialize(apiKey: "YOUR_API_KEY")
        return true
    }

    // MARK: UISceneSession Lifecycle

    func application(_ application: UIApplication, configurationForConnecting connectingSceneSession: UISceneSession, options: UIScene.ConnectionOptions) -> UISceneConfiguration {
        return UISceneConfiguration(name: "Default Configuration", sessionRole: connectingSceneSession.role)
    }

    func application(_ application: UISceneSession.ConnectionOptions, didDiscardSceneSessions sceneSessions: Set<UISceneSession>) {
    }
}
