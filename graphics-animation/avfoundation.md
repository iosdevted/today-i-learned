# [HowTo] Video as Background with AVFoundation

AVPlayer and AVPlayerLayer to create a video background

AVFoundation is the full featured framework for working with time-based audiovisual media on iOS, macOS, watchOS and tvOS. Using AVFoundation, you can easily play, create, and edit QuickTime movies and MPEG-4 files, play HLS streams, and build powerful media functionality into your apps.

<p>
  <img src="https://user-images.githubusercontent.com/50784573/111467185-7dfc4c80-8767-11eb-9e20-3de258da7d8c.gif" Height=350/>
</p>


```swift
import AVFoundation
import Combine

...

//MARK: - Properties

private var player: AVPlayer?
private var playerLayer: AVPlayerLayer?
private let notificationCenter = NotificationCenter.default
private var appEventSubsribers = [AnyCancellable]()

...

//MARK: - Life Cycle

override func viewWillAppear(_ animated: Bool) {
    observeAppEvents()
    setupPlayerIfNeeded()
    restartVideo()
}

override func viewWillDisappear(_ animated: Bool) {
    super.viewWillDisappear(animated)
    removeAppEventsSubscribers()
    removePlayer()
}

override func viewDidLayoutSubviews() {
    super.viewDidLayoutSubviews()
    playerLayer?.frame = view.bounds
}

...

//MARK: - Helpers

private func buildPlayer() -> AVPlayer? {
    guard let filePath = Bundle.main.path(forResource: "bg_video", ofType: "mp4") else { return nil }
    let url = URL(fileURLWithPath: filePath)
    let player = AVPlayer(url: url)
    player.actionAtItemEnd = .none
    player.isMuted = true
    return player
}

private func buildPlayerLayer() -> AVPlayerLayer? {
    let layer = AVPlayerLayer(player: player)
    layer.videoGravity = .resizeAspectFill
    return layer
}

private func playVideo() {
    player?.play()
}

private func restartVideo() {
    player?.seek(to: .zero)
    playVideo()
}

private func pauseVideo() {
    player?.pause()
}

private func setupPlayerIfNeeded() {
    player = buildPlayer()
    playerLayer = buildPlayerLayer()
    
    if let layer = self.playerLayer,
        view.layer.sublayers?.contains(layer) == false {
            view.layer.insertSublayer(layer, at: 0)
        }
        
}

private func observeAppEvents() {
    
    notificationCenter.publisher(for: .AVPlayerItemDidPlayToEndTime).sink { [weak self] _ in
        self?.restartVideo()
    }.store(in: &appEventSubsribers)
    
    notificationCenter.publisher(for: UIApplication.willResignActiveNotification).sink { [weak self] _ in
        self?.pauseVideo()
    }.store(in: &appEventSubsribers)
    
    notificationCenter.publisher(for: UIApplication.didBecomeActiveNotification).sink { [weak self] _ in
        self?.playVideo()
    }.store(in: &appEventSubsribers)
}

private func removeAppEventsSubscribers() {
    appEventSubsribers.forEach { subscriber in
        subscriber.cancel()
    }
}

private func removePlayer() {
    player?.pause()
    player = nil
    
    playerLayer?.removeFromSuperlayer()
    playerLayer = nil
}

```

## References

[Apple Developer Document](https://developer.apple.com/documentation/avfoundation/avplayer)