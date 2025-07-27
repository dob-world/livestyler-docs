# LiveStyler SDK for Flutter(Android, iOS)

This SDK is for applying LiveStyler functionality to Flutter cross-platform applications.
The SDK provides functionality to initialize the camera, transmit captured video, and receive and display processed video.

## Getting Started

### Requirements

- Flutter 3.24.5 or higher
- Android 9.0 or higher
    - AGP 8.0 or higher
    - Kotlin 1.7.21 or higher
- iOS 12.0 or higher
    - Xcode 12.0 or higher
    - Swift 5.0 or higher

### Key Features

- Control video transformation through signal channels
- Transmit camera video to WebRTC server
- Receive filtered video

### Installation

#### Gradle

```yaml
// pubspec.yml

dependencies:
    livestayler_sdk_flutter:
        git: https://github.com/dob-world/LiveStylerSDKFlutter.git
        ref: 0.0.1
```

Then run the following command:

```bash
$ flutter pub get
```

## How to Use

### Easy Usage

You can use the pre-implemented `StreamPage`.

```dart
// Environment initialization
AppEnv.setEnv(
    '{credential}',
    '{apiEndpoint}',
    '{signalEndpoint}',
    '{iceServers}',
    '{onTrialStarted}',
    '{onTrialEnded}',
    '{language}',
);

// Navigate to screen
Navigator.of(context).push(
    MaterialPageRoute(
        builder: (context) {
            return StreamPage();
        },
    ),
);
```

### Custom Development

For additional features not provided in the easy usage and custom UI/UX implementation, it's better to implement directly.

Please refer to the [Key Feature Specifications](#key-feature-specifications) described below for usage instructions.

If you want to change the screen design and functionality, please refer to the [`stream_page.dart` file](flutter-streampagedart-web.md).

For detailed API specifications, please refer to [Flutter Web APIs](reference-flutter-web.md).

## Key Feature Specifications

You can directly create and implement screen functionality using the API.

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

- `credential`: Authentication token issued through the admin page
- `apiEndpoint`: API server where service information can be obtained
- `signalEndpoint`: Signal channel endpoint address for exchanging authentication information with the backend
- `iceServers`: Configure STUN and TURN servers; using the provided Google STUN server is recommended
- `iceTransportsType`: Specify peer-to-peer connection method using one of All, NoHost, or Relay values
- `localRenderer`: Renderer for rendering WebRTC streams (RTCVideoRenderer)
- `remoteRenderer`: Renderer for rendering WebRTC remote streams (RTCVideoRenderer)
- `signalStateListener`: Handle signal channel events
- `rendererStateListener`: Handle renderer events
- `dataChannelStateListener`: Handle data channel events
- `onReceiveStatsData`: Callback function that receives playback statistics

#### initialize()

Performs necessary tasks during initialization.

```dart
@override
void initState() {
    super.initState();
    _liveStylerManager.initialize();
}
```

#### release()

Completely terminates signal server connection and WebRTC connection and returns resources.

```dart
@override
void dispose() {
    _liveStylerManager.release();
    super.dispose();
}
```

#### updateFilterCategory()

Updates by receiving new filter and category lists from the API server.
The filter list is automatically updated when connecting to the signal server.
The updated list is delivered through SignalStateListener.

```dart
_liveStylerManager.updateFilterCategory();
```

#### switchCamera(String)

Switches to the received camera ID.
Camera ID can be obtained through CameraManager.

```dart
_liveStylerManager.switchCamera("{camera_id}")
```

- `camera_id`: Camera ID obtained through MediaDevices

#### changeModel(String)

Changes the filter model to the received model name.

```dart
_liveStylerManager.changeModel("{model_name}")
```

- `model_name`: Model name from FilterCategoryData

### AppEnv

The AppEnv class manages application environment settings.

```dart
// Environment configuration
AppEnv.setEnv(
    '{credential}',
    '{apiEndpoint}',
    '{signalEndpoint}',
    '{iceServers}',
    '{onTrialStarted}',
    '{onTrialEnded}',
    '{onChangeLanguage}',
    '{language}',
);
```

- `credential`: Authentication information
- `apiEndpoint`: API endpoint URL
- `signalEndpoint`: Signaling server endpoint URL
- `iceServers`: ICE server configuration
- `onTrialStarted`: Trial start callback
- `onTrialEnded`: Trial end callback
- `onChangeLanguage`: Language change callback
- `language`: Initial language setting

## Usage Example

```dart
void main() async {
  // Environment configuration
  AppEnv.setEnv(
    credential: 'your_credential',
    apiEndpoint: 'https://api.example.com',
    signalEndpoint: 'wss://signal.example.com',
    iceServers: [
      {'urls': 'stun:stun.example.com:19302'},
    ],
    language: 'en-US',
  );

  // LiveStylerManager initialization
  final manager = LiveStylerManager(
    credential: AppEnv.credential,
    apiEndpoint: AppEnv.apiEndpoint,
    signalEndpoint: AppEnv.signalEndpoint,
    iceServerList: AppEnv.iceServers.map((server) => StunTurnServer.fromJson(server)).toList(),
    localRenderer: RTCVideoRenderer(),
    remoteRenderer: RTCVideoRenderer(),
    signalStateListener: YourSignalStateListener(),
    rendererStateListener: YourRendererStateListener(),
    dataChannelStateListener: YourDataChannelStateListener(),
    onReceiveStatsData: (stats) {
      print('Received stats: $stats');
    },
  );

  await manager.initialize();
  await manager.start();

  // Change style model
  await manager.changeModel('romantic');

  // Switch camera
  await manager.switchCamera('front_camera_id');

  // Terminate connection
  await manager.stop();
}
```