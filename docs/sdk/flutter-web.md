# LiveStyler SDK for Flutter Web

Flutter CrossPlatfom 에 LiveStyler 기능을 적용하기 위한 SDK입니다.
SDK는 카메라를 초기화하여 촬영된 영상을 전송하고 영상처리가 완료된 영상을 수신하여 보여주는 기능을 제공합니다.

## 시작하기

### 요구사항

- Windows 10 이상, macOS 13.0 이상
    - Flutter 3.24.5 이상
    - Chrome 브라우저 100 이상

### 주요기능

- 시그널 채널을 통한 영상 변환을 제어
- 카메라의 영상을 WebRTC 서버로 전송
- 필터가 적용된 영상을 수신

### 설치

#### Gradle

```yaml
// pubspec.yml

dependencies:
    livestayler_sdk_flutter_Web:
        git: https://github.com/dob-world/LiveStylerSDKFlutterWeb.git
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

세부적인 API 명세는 [Flutter APIs](reference-flutter-web.md)를 참고하여 주시기 바랍니다.

## 주요 기능 명세

API를 사용하면 화면의 기능을 직접 만들어 구현할 수 있습니다.

### LiveStylerManager

```dart
_liveStylerManager = LiveStylerManager(
    credential: '{credential}',
    apiEndpoint: '{apiEndpoint}',
    signalEndpoint: '{signalEndpoint}',
    iceServerList: '{iceServers}',
    iceTransportsType: 'Relay',
    localRenderer: {localRenderer},
    remoteRenderer: {remoteRenderer},
    signalStateListener: {signalStateListener},
    rendererStateListener: {rendererStateListener},
    dataChannelStateListener: {dataChannelStateListener},
    onReceiveStatsData: {onReceiveStatsData},
);
```

- `creadential`: 관리자 페이지를 통해 발급 받은 인증 토큰
- `apiEndpoint`: 서비스의 정보를 얻을 수 있는 API 서버
- `signalEndpoint`: 백엔드와 인증 정보를 주고 받는 시그널 채널 엔드포인트 주소
- `iceServers`: STUN 서버와 TURN 서버를 설정, STUN 서버는 제공된 구글 STUN 사용 권장
- `iceTransportsType`: All, NoHost, Relay 중 하나의 값을 사용하여 Peep-to-peer 연결 방식을 지정
- `localRenderer`: WebRTC 스트림을 렌더링하는 렌더러(RTCVideoRenderer)
- `remoteRenderer`: WebRTC 리모트 스트림을 렌더링하는 렌더러(RTCVideoRenderer)
- `signalStateListener`: 시그널 채널의 이벤트를 처리
- `rendererStateListener`: 렌더러의 이벤트를 처리
- `dataChannelStateListener`: 데이터 채널의 이벤트를 처리
- `onReceiveStatsData`: 재생 통계가 수신되는 콜백 함수

#### initialize()

초기화가 될 때 필요한 작업이 이루어집니다.

```dart
@override
void initState() {
    super.initState();
    _liveStylerManager.initialize();
}
```

#### release()

시그널 서버 연결과 WebRTC 연결을 완전히 종료하고 리소스를 반환합니다.

```dart
@override
void dispose() {
    _liveStylerManager.release();
    super.dispose();
}
```

#### updateFilterCategory()

API 서버에서 필터, 카테고리 리스트를 새로 받아 업데이트합니다.
필터 리스트는 시그널 서버에 접속하면 자동으로 업데이트됩니다.
갱신된 리스트는 SignalStateListener를 통해 전달됩니다.

```dart
_liveStylerManager.updateFilterCategory();
```

#### switchCamera(String)

전달 받은 카메라 ID로 전환합니다.
카메라 ID는 CameraManager를 통해 얻을 수 있습니다.

```dart
_liveStylerManager.switchCamera("{camera_id}")
```

- `camera_id`: MediaDevices를 통해 얻은 카메라의 ID

#### changeModel(String)

전달 받은 모델 이름으로 필터 모델을 변경합니다.

```dart
_liveStylerManager.changeModel("{model_name}")
```

- `model_name`: FilterCategoryData의 모델 이름