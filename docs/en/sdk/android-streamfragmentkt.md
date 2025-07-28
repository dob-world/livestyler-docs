# StreamFragment.kt Source Code

```kotlin
class StreamFragment : Fragment(), SignalStateListener, RendererStateListener {

    // Layout Binding
    private var _binding: FragmentStreamBinding? = null

    // This property is only valid between onCreateView and
    // onDestroyView.
    private val binding get() = _binding!!

    private lateinit var liveStylerManager: LiveStylerManager

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        liveStylerManager = LiveStylerManager(
            {credential},
            {apiEndpoint},
            {signalEndpoint},
            {servers},
        )

        liveStylerManager.onCreate(requireContext(), this)
    }

    override fun onCreateView(
        inflater: LayoutInflater, container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View {
        _binding = FragmentStreamBinding.inflate(inflater, container, false)
        liveStylerManager.onCreateView(this, this)
        return binding.root
    }

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)
        liveStylerManager.onViewCreated(binding.preview, binding.renderView)

        // 카메라 퍼미션 체크 및 요청
        when {
            ContextCompat.checkSelfPermission(
                requireContext(),
                Manifest.permission.CAMERA
            ) == PackageManager.PERMISSION_GRANTED -> {
                // You can use the API that requires the permission.
            }
            shouldShowRequestPermissionRationale(Manifest.permission.CAMERA) -> {
                // In an educational UI, explain to the user why your app requires this
                // permission for a specific feature to behave as expected, and what
                // features are disabled if it's declined. In this UI, include a
                // "cancel" or "no thanks" button that allows the user to continue
                // using your app without granting the permission.
                // showInContextUI(...)
            }
            else -> {
                // You can directly ask for the permission.
                requestPermissionLauncher.launch(Manifest.permission.CAMERA)
            }
        }
    }

    @RequiresPermission(Manifest.permission.CAMERA)
    override fun onResume() {
        super.onResume()
        liveStylerManager.onResume()
    }

    override fun onPause() {
        super.onPause()
        liveStylerManager.onPause()
    }

    override fun onStop() {
        liveStylerManager.onStop()
        super.onStop()
    }

    override fun onDestroyView() {
        liveStylerManager.onDestroy()
        _binding = null
        super.onDestroyView()
    }

    /* SignalStateListener Implements Methods */
    override fun onServerPreparing() {
        TODO("Not yet implemented")
    }

    override fun onServerReady() {
        TODO("Not yet implemented")
    }

    override fun onReceivedFilterList(filterList: List<FilterCategoryData>?) {
        TODO("Not yet implemented")
    }

    override fun onErrorFilterList(error: String?) {
        TODO("Not yet implemented")
    }

    /* RendererStateListener Implements Methods */
    override fun onLocalPreparing() {
        TODO("Not yet implemented")
    }

    override fun onLocalFirstFrameRendered() {
        TODO("Not yet implemented")
    }

    override fun onLocalStateChanged(state: RendererState) {
        TODO("Not yet implemented")
    }

    override fun onRemotePreparing() {
        TODO("Not yet implemented")
    }

    override fun onRemoteFirstFrameRendered() {
        TODO("Not yet implemented")
    }

    override fun onRemoteStateChanged(state: RendererState) {
        TODO("Not yet implemented")
    }
}
```