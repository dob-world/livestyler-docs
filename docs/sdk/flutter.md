# LiveStyler SDK for Flutter(Android, iOS)

Flutter CrossPlatfom 에 LiveStyler 기능을 적용하기 위한 SDK입니다.
SDK는 카메라를 초기화하여 촬영된 영상을 전송하고 영상처리가 완료된 영상을 수신하여 보여주는 기능을 제공합니다.

## 시작하기

### 요구사항

- Flutter 3.24.5 이상
- Android 9.0 이상
    - AGP 8.0 이상
    - Kotlin 1.7.21 이상
- iOS 12.0 이상
    - Xcode 12.0 이상
    - Swift 5.0 이상

### 주요기능

- 시그널 채널을 통한 영상 변환을 제어
- 카메라의 영상을 WebRTC 서버로 전송
- 필터가 적용된 영상을 수신

### 설치

#### Gradle

```yaml
// pubspec.yml

dependencies:
    livestayler_sdk_flutter:
        git: https://github.com/dob-world/LiveStylerSDKFlutter.git
        ref: 0.0.1
```

그리고 다음 명령을 실행합니다:

```bash
$ flutter pub get
```

## 사용방법

### 쉬운 사용

기능이 사전에 구현된 `StreamPage` 사용할 수 있습니다.

```dart
// 환경 초기화
AppEnv.setEnv(
    '{credential}',
    '{apiEndpoint}',
    '{signalEndpoint}',
    '{iceServers}',
    '{onTrialStarted}',
    '{onTrialEnded}',
    '{language}',
);

// 화면 이동
Navigator.of(context).push(
    MaterialPageRoute(
        builder: (context) {
            return StreamPage();
        },
    ),
);
```

### 직접 개발

쉬운 사용에서는 제공하지 않는 추가적인 기능, UI/UX의 임의 구현을 위해서는 직접 구현하는 것이 좋습니다.

사용 방법은 후술할 [주요 기능 명세](#주요-기능-명세)를 참고하여 주시기 바랍니다.

화면의 디자인과 기능을 변경하고자 하면 [`stream_page.dart` 파일](flutter-streampagedart-web.md)을 참고하여 사용하세요.

세부적인 API 명세는 [Flutter Web APIs](reference-flutter-web.md)를 참고하여 주시기 바랍니다.

## 주요 기능 명세

API를 사용하면 화면의 기능을 직접 만들어 구현할 수 있습니다.

### LiveStylerManager