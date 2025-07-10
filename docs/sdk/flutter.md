# LiveStyler SDK for Flutter(Android, iOS, macOS)

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
- macOS 13.0 이상
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

기능이 사전에 구현된 `StreamFragment` 사용할 수 있습니다.