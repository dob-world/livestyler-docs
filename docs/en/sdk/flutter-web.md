# LiveStyler SDK for Flutter Web, Desktop (macOS, Windows)

This SDK is for applying LiveStyler features to the Flutter Cross-Platform.
The SDK provides functionality to initialize the camera, send the captured video, and receive and display the video after processing is complete.

## Getting Started

### Requirements

- Web
    - Windows 10 or higher, macOS 13.0 or higher
        - Flutter 3.24.5 or higher
        - Chrome browser 100 or higher
- Desktop
    - Windows 10 or higher
        - Flutter 3.24.5 or higher
    - macOS 13.0 or higher
        - Xcode 12.0 or higher
        - Swift 5.0 or higher

### Key Features

- Control video conversion through a signaling channel
- Send camera video to a WebRTC server
- Receive video with filters applied

### Installation

#### Pub

```yaml
# pubspec.yaml

dependencies:
  # Preparing storage.
  livestyler_sdk_flutter_web:
    git:
      url: https://github.com/dob-world/LiveStylerSDKFlutterWebDesktop.git
      ref: 0.0.1
```

And then run the following command:

```bash
$ flutter pub get
```

## How to Use

### Easy Usage

You can use the pre-implemented `StreamPage`.

```dart
// Environment Initialization
AppEnv.setEnv(
    credential: '{credential}',
    apiEndpoint: '{apiEndpoint}',
    signalEndpoint: '{signalEndpoint}',
    iceServers: '{iceServers}',
    onTrialStarted: () {
      // Handle trial started
    },
    onTrialEnded: () {
      // Handle trial ended
    },
    language: '{language}',
);


// Navigate to the screen
Navigator.of(context).push(
    MaterialPageRoute(
        builder: (context) {
            return StreamPage();
        },
    ),
);
```

### Custom Development

For additional features not provided in the easy usage, or for custom implementation of UI/UX, it is recommended to implement it yourself.

Please refer to the [Key Feature Specifications](#key-feature-specifications) described later for usage instructions.

If you want to change the design and functionality of the screen, please refer to the [`stream_page.dart` file](flutter-streampagedart-web.md).

For detailed API specifications, please refer to [Flutter Web APIs](reference-flutter-web.md).

## Key Feature Specifications

You can create and implement screen functions yourself using the API.

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

- `credential`: Authentication token issued through the admin page.
- `apiEndpoint`: API server to get service information.
- `signalEndpoint`: Signaling channel endpoint address for exchanging authentication information with the backend.
- `iceServers`: Set STUN and TURN servers. It is recommended to use the provided Google STUN server.
- `iceTransportsType`: Specify the Peer-to-peer connection method using one of the values: All, NoHost, Relay.
- `localRenderer`: Renderer for the WebRTC local stream (RTCVideoRenderer).
- `remoteRenderer`: Renderer for the WebRTC remote stream (RTCVideoRenderer).
- `signalStateListener`: Handles events from the signaling channel.
- `rendererStateListener`: Handles events from the renderer.
- `dataChannelStateListener`: Handles events from the data channel.
- `onReceiveStatsData`: Callback function that receives playback statistics.

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

Completely terminates the signal server and WebRTC connections and releases resources.

```dart
@override
void dispose() {
    _liveStylerManager.release();
    super.dispose();
}
```

#### updateFilterCategory()

Updates the filter and category lists by fetching them from the API server.
The filter list is automatically updated upon connecting to the signal server.
The updated list is delivered through the SignalStateListener.

```dart
_liveStylerManager.updateFilterCategory();
```

#### switchCamera(String)

Switches to the camera with the received camera ID.
The camera ID can be obtained through MediaDevices.

```dart
_liveStylerManager.switchCamera("{camera_id}")
```

- `camera_id`: The ID of the camera obtained through MediaDevices.

#### changeModel(String)

Changes the filter model to the received model name.

```dart
_liveStylerManager.changeModel("{model_name}")
```

- `model_name`: The model name from FilterCategoryData.

### AppEnv

The AppEnv class manages application environment settings.

```dart
// Environment setup
AppEnv.setEnv(
    credential: '{credential}',
    apiEndpoint: '{apiEndpoint}',
    signalEndpoint: '{signalEndpoint}',
    iceServers: '{iceServers}',
    onTrialStarted: () {
      // Handle trial started
    },
    onTrialEnded: () {
      // Handle trial ended
    },
    onChangeLanguage: (lang) {
      // Handle language change
    },
    language: '{language}',
);
```

- `credential`: Authentication information.
- `apiEndpoint`: API endpoint URL.
- `signalEndpoint`: Signaling server endpoint URL.
- `iceServers`: ICE server settings.
- `onTrialStarted`: Trial start callback.
- `onTrialEnded`: Trial end callback.
- `onChangeLanguage`: Language change callback.
- `language`: Initial language setting.

## Usage Example

```dart
void main() async {
  // Environment setup
  AppEnv.setEnv(
    credential: 'your_credential',
    apiEndpoint: 'https://api.example.com',
    signalEndpoint: 'wss://signal.example.com',
    iceServers: [
      {'urls': 'stun:stun.example.com:19302'},
    ],
    language: 'en-US',
  );

  // Initialize LiveStylerManager
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

  // Stop connection
  await manager.stop();
}
```