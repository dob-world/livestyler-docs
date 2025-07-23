# StreamViewController의 소스코드

```swift
import AVFoundation
import UIKit
import WebRTC

public class StreamViewController: UIViewController {
    private let manager: LiveStylerManager
    private let label = UILabel()
    private var appConstants: AppConstants

    public override init(nibName nibNameOrNil: String?, bundle nibBundleOrNil: Bundle?) {
        appConstants = AppConstants()
        manager = LiveStylerManager(appConstants: appConstants)
        super.init(nibName: nibNameOrNil, bundle: nibBundleOrNil)
    }

    public required init?(coder: NSCoder) {
        appConstants = AppConstants()
        manager = LiveStylerManager(appConstants: appConstants)
        super.init(coder: coder)
    }

    public init(appConstants: AppConstants) {
        self.appConstants = appConstants
        manager = LiveStylerManager(appConstants: appConstants)
        super.init(nibName: nil, bundle: nil)
    }

    public override func viewDidLoad() {
        super.viewDidLoad()
        setupUI()
        manager.initialize()
    }

    public override func viewWillAppear(_ animated: Bool) {
        super.viewWillAppear(animated)
        manager.activate()
    }

    public override func viewDidDisappear(_ animated: Bool) {
        super.viewDidDisappear(animated)
        manager.deactivate()
    }

    deinit {
        manager.cleanup()
    }

    public func setAppConstants(appConstants: AppConstants) {
        self.appConstants = appConstants
        setupUI()
        view.setNeedsLayout()
    }

    private func setupUI() {
        title = "WebRTC Test"
        view.backgroundColor = appConstants.colors.background

        setupLabel()
        setupVideoView()
        setupLocalVideoView()
    }

    private func setupLabel() {
        label.translatesAutoresizingMaskIntoConstraints = false
        label.text = "영상"
        label.font = UIFont.systemFont(
            ofSize: appConstants.ui.titleFontSize,
            weight: .bold
        )
        label.textAlignment = .center
        label.textColor = appConstants.colors.text
        view.addSubview(label)

        NSLayoutConstraint.activate([
            label.topAnchor.constraint(
                equalTo: view.safeAreaLayoutGuide.topAnchor,
                constant: appConstants.ui.defaultMargin
            ),
            label.leadingAnchor.constraint(equalTo: view.leadingAnchor),
            label.trailingAnchor.constraint(equalTo: view.trailingAnchor),
            label.heightAnchor.constraint(equalToConstant: appConstants.ui.largeMargin),
        ])
    }

    private func setupVideoView() {
        let videoView = manager.videoView
        videoView.translatesAutoresizingMaskIntoConstraints = false
        view.addSubview(videoView)

        NSLayoutConstraint.activate([
            videoView.topAnchor.constraint(
                equalTo: label.bottomAnchor, constant: appConstants.ui.defaultMargin),
            videoView.leadingAnchor.constraint(
                equalTo: view.leadingAnchor, constant: appConstants.ui.defaultMargin),
            videoView.trailingAnchor.constraint(
                equalTo: view.trailingAnchor, constant: -appConstants.ui.defaultMargin),
            videoView.bottomAnchor.constraint(
                equalTo: view.bottomAnchor, constant: -appConstants.ui.defaultMargin),
        ])
    }

    private func setupLocalVideoView() {
        let localVideoView = manager.localVideoView
        localVideoView.translatesAutoresizingMaskIntoConstraints = false
        view.addSubview(localVideoView)

        NSLayoutConstraint.activate([
            localVideoView.widthAnchor.constraint(
                equalToConstant: appConstants.stream.localVideoSize.width),
            localVideoView.heightAnchor.constraint(
                equalToConstant: appConstants.stream.localVideoSize.height),
            localVideoView.trailingAnchor.constraint(
                equalTo: view.trailingAnchor, constant: -appConstants.ui.defaultMargin),
            localVideoView.bottomAnchor.constraint(
                equalTo: view.safeAreaLayoutGuide.bottomAnchor,
                constant: -appConstants.ui.defaultMargin),
        ])
    }
}
```