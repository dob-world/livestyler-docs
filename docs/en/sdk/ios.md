# LiveStyler SDK for iOS

This SDK is for applying LiveStyler features to iOS applications.
The SDK provides functionality to initialize the camera, send the captured video, and receive and display the video after processing is complete.

## Getting Started

### Requirements

- iOS 12.0 or higher
- Xcode 12.0 or higher
- Swift 5.0 or higher

### Key Features

- Control video conversion through a signaling channel
- Send camera video to a WebRTC server
- Receive video with filters applied

### Installation

#### CocoaPods

Add the following line to your `Podfile`:

```ruby
pod 'LiveStylerSDK', :git => 'https://{ghp_token}@github.com/dob-world/livestyler-sdk-ios.git', :tag => '0.0.1'
```

- `ghp_token`: This is a personal token used on GitHub. Use the personal token of an account that has access to the repository.

And then run the following command:

```bash
$ pod install
```

## How to Use

### Easy Usage

To test the functionality, you can easily apply and use the pre-implemented features.

```swift
import LiveStylerSDK

let contants = AppConstants(
    servers: Servers(
        credential: "{user_credential}",                // Access Token issued from the admin console
        webSocketURL: "{signaling_endpoint}",           // Signaling channel endpoint address for exchanging authentication information with the backend
        stunServer: "stun:stun.l.google.com:19302",     // STUN server address
        turnServer: "{turn_server_endpoint}",           // TURN server address
        turnUsername: "{username}",                     // TURN server authentication information
        turnPassword: "{password}"                      // TURN server authentication information
    )
)

StreamViewController(appConstants: contants)
```

### Custom Development

For additional features not provided in the easy usage, or for custom implementation of UI/UX, it is recommended to implement it yourself.

Please refer to the [Key Feature Specifications](#key-feature-specifications) described later for usage instructions.
For an example, please refer to the [source code of StreamViewController](ios-streamviewcontroller.md) provided with the SDK.

For detailed API specifications, please refer to [iOS APIs](reference-swift.md).


## Key Feature Specifications

### LiveStylerManager

```swift
/// Initialization

let contants = AppConstants(
    servers: Servers(
        credential: "{user_credential}",                // Access Token issued from the admin console
        webSocketURL: "{signaling_endpoint}",           // Signaling channel endpoint address for exchanging authentication information with the backend
        stunServer: "stun:stun.l.google.com:19302",     // STUN server address
        turnServer: "{turn_server_endpoint}",           // TURN server address
        turnUsername: "{username}",                     // TURN server authentication information
        turnPassword: "{password}"                      // TURN server authentication information
    )
)

let manager = LiveStylerManager(appConstants: contants)
```

#### initialize()

Performs necessary tasks during initialization.

```swift
public override func viewDidLoad() {
    super.viewDidLoad()
    manager.initialize()
}
```

#### activate()

Starts camera capture.

```swift
public override func viewWillAppear(_ animated: Bool) {
    super.viewWillAppear(animated)
    manager.activate()
}
```

####  deactivate()

Stops camera capture.

```swift
public override func viewDidDisappear(_ animated: Bool) {
    super.viewDidDisappear(animated)
    manager.deactivate()
}
```

#### cleanup()

Completely terminates the signal server and WebRTC connections and releases resources.

```swift
deinit {
    manager.cleanup()
}
```

#### switchCamera(cameraId: String)

Switches to the camera with the received camera ID.
The camera ID can be obtained through AVCaptureDevice.

```swift
manager.switchCamera("{camera_id}")
```

- camera_id: The ID of the camera obtained through AVCaptureDevice (iOS system tool).

#### changeModel(modelName: String)

Changes the filter model to the received model name.

```swift
manager.changeModel("{model_name}")
```

- model_name: The model name from FilterCategoryData.

> The iOS SDK does not include a process to fetch the model list via the API.<br/>
> Please refer to the API guide to check and use the model list.