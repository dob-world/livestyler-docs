# LiveStyler SDK for Flutter (Android, iOS)

This SDK is for applying LiveStyler features to the Flutter Cross-Platform.
The SDK provides functionality to initialize the camera, send the captured video, and receive and display the video after processing is complete.

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

- Control video conversion through a signaling channel
- Send camera video to a WebRTC server
- Receive video with filters applied

### Installation

#### Pub

```yaml
// pubspec.yaml

dependencies:
  livestyler_sdk_flutter:
    git:
      url: https://github.com/dob-world/LiveStylerSDKFlutter.git
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
    '{credential}',
    '{apiEndpoint}',
    '{signalEndpoint}',
    '{iceServers}',
    '{onTrialStarted}',
    '{onTrialEnded}',
    '{language}',
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

For detailed API specifications, please refer to [Flutter APIs](reference-flutter.md).

## Key Feature Specifications

You can create and implement screen functions yourself using the API.

### LiveStylerManager
