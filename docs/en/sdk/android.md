# LiveStyler SDK for Android

This SDK is for applying LiveStyler features to Android applications.
The SDK provides functionality to initialize the camera, send the captured video, and receive and display the video after processing is complete.

## Getting Started

### Requirements

- Android 9.0 or higher
- AGP 8.0 or higher
- Kotlin 1.7.21 or higher

### Key Features

- Control video conversion through a signaling channel
- Send camera video to a WebRTC server
- Receive video with filters applied

### Installation

#### Gradle

```groovy
// root/build.gradle
allprojects {
    repositories {
        ...
        // Preparing storage.
        maven { url "https://repository.livestyler.io/..." }
        ...
    }
}
```

```groovy
// app/build.gradle
dependencies {
    // Preparing storage.
    implementation "ai.livestyler:LiveStylerSDKAndroid:latest.release"
}
```

Then execute the following command:

```bash
$ ./gradlew build --refresh-dependencies
```

## How to Use

### Easy Usage

You can use the pre-implemented `StreamFragment`.

```kotlin
// Initialization
val args = Bundle().apply {
    putString("credential", "{credential}")                             // Authentication token issued through the admin page
    putString("apiEndpoint", "{apiEndpoint}")                           // API server to get service information
    putString("signalEndpoint", "{signalEndpoint}")                     // Signaling channel endpoint address for exchanging authentication information with the backend
    putParcelableArrayList(
        "serverEndpoints",                                              // Set STUN and TURN servers, recommended to use the provided Google STUN server
        arrayListOf(
            Bundle().apply {
                putString("type", "stun")
                putString("endpoint", "stun:stun.l.google.com:19302")   // STUN server address
            },
            Bundle().apply {
                putString("type", "turn")
                putString("endpoint", "{turnEndpoint}")                 // TURN server address
                putString("username", "{username}")                     // TURN server authentication information
                putString("password", "{password}")                     // TURN server authentication information
            },
            Bundle().apply {
                putString("type", "turn")
                putString("endpoint", "{turnEndpoint}")                 // TURN server address
                putString("username", "{username}")                     // TURN server authentication information
                putString("secret", "{secret}")                         // TURN server authentication information
            }
        )
    )
    putString("iceTransportsType", "{iceTransportsType}")           // Specify the Peer-to-peer connection method using one of the values: All, NoHost, Relay
}

findNavController().navigate(R.id.action_HostFragment_to_StreamFragment, args = args)
```

### Custom Development

For additional features not provided in the easy usage, or for custom implementation of UI/UX, it is recommended to implement it yourself.

Please refer to the [Key Feature Specifications](#key-feature-specifications) described later for usage instructions.

If you want to change the design and functionality of the screen, please refer to the [`StreamFragment.kt` file](android-streamfragmentkt.md) and the [`fragment_stream.xml` file](android-fragmentstreamxml.md).

For detailed API specifications, please refer to [Android APIs](reference-kotlin.md).

## Key Feature Specifications

You can create and implement screen functions yourself using the API.

### LiveStylerManager

```kotlin
// Initialization
// Use the same values for accessKey, signalEndpoint, serverEndpoints as used when creating args
val liveStylerManager: LiveStylerManager = LiveStylerManager(
    "{credential}",
    "{apiEndpoint}",
    "{signalEndpoint}",
    listOf( {serverEndpoints} ),
    "{iceTransportsType}"
)
```

#### onCreate()

Performs necessary tasks during initialization.

```kotlin
override fun onCreate(saveInstance?: Bundle) {
    super.onCreate(saveInstance)
    liveStyleManager.onCreate(requireContext(), this)
}
```

#### onCreateView()

Performs necessary tasks before the view is created.

```kotlin
override fun onCreateView(
    inflater: LayoutInflater, container: ViewGroup?,
    savedInstanceState: Bundle?
): View {
    _binding = FragmentStreamBinding.inflate(inflater, container, false)
    liveStylerManager.onCreateView(this, this)
    return binding.root
}
```

#### onViewCreated()

Passes the created view to the manager.
You can selectively add a camera preview or a render preview.

```kotlin
override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
    super.onViewCreated(view, savedInstanceState)
    liveStylerManager.onViewCreated(binding.preview, binding.renderView)
}
```

#### onPause()

Stops camera capture.

```kotlin
override fun onPause() {
    super.onPause()
    liveStylerManager.onPause()
}
```

#### onResume()

Resumes camera capture.
Resets the signal server connection and WebRTC connection.

```kotlin
override fun onResume() {
    super.onResume()
    liveStylerManager.onResume()
}
```

#### onStop()

Releases camera resources.
Disconnects from the signal server and WebRTC and waits.

```kotlin
override fun onStop() {
    liveStylerManager.onStop()
    super.onStop()
}
```

#### onDestroy()

Completely terminates the signal server connection and WebRTC connection.

```kotlin
override fun onDestroyView() {
    liveStylerManager.onDestroy()
    _binding = null
    super.onDestroyView()
}
```

#### changeFilter(String)

Changes the filter set on the WebRTC server.

```kotlin
liveStylerManager.changeFilter("{filter_id}")
```

- filter_id: The ID of the filter you want to change from the filter list received via the API.

#### switchCamera(String)

Switches to the camera with the received camera ID.
The camera ID can be obtained through the CameraManager.

```kotlin
liveStylerManager.switchCamera("{camera_id}")
```

- camera_id: The ID of the camera obtained through CameraManager (Android system tool).

#### updateFilterCategory()

Updates the filter and category lists by fetching them from the API server.
The filter list is automatically updated upon connecting to the signal server.
The updated list is delivered through the SignalStateListener.

```kotlin
liveStylerManager.updateFilterCategory()
```

#### changeModel(String)

Changes the filter model to the received model name.
Use the model name found in the filter information (FilterCategoryData) obtained through the onReceivedFilterList() callback.

```kotlin
liveStylerManager.changeModel("{model_name}")
```

- model_name: The model name from FilterCategoryData.

> The Android SDK includes processing to fetch the model list via the API.<br/>
> However, the API reflected in the SDK is a service provided by LiveStyler, so there may be differences from the actual service.<br/>
> The API may vary depending on the service configuration, so please refer to the API guide to check and use the model list.
