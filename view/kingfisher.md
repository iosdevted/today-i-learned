# [HowTo] Kingfisher

This guide covers the most useful part of Kingfisher components. If you want to know the detail of them, please also check the full [API Reference](https://github.com/onevcat/Kingfisher/wiki/Cheat-Sheet).

The view extension based APIs (for `UIImageView`, `NSImageView`, `UIButton` and `NSButton`) should be your first choice whenever possible. It keeps your code simple and elegant.

## Set image with a `URL`

```swift
let url = URL(string: "https://example.com/image.jpg")
imageView.kf.setImage(with: url)
```

This simple code:

1. Checks whether an image is cached under the key url.absoluteString.
2. If an image was found in the cache (either in memory or disk), sets it to imageView.image.
3. If not, creates a request and download it from url.
4. Converts the downloaded data to a UIImage object.
5. Caches the image to memory cache, and store the data to the disk cache.
6. Sets the imageView.image to display it.

## Showing a placeholder

```swift
let image = UIImage(named: "default_profile_icon")
imageView.kf.setImage(with: url, placeholder: image)
```

The `image` will show in the `imageView` while downloading from `url`.

## Showing a loading indicator while downloading

```swift
imageView.kf.indicatorType = .activity
imageView.kf.setImage(with: url)
```

Show a `UIActivityIndicatorView` in center of image view while downloading.

## Completion handler

```swift
imageView.kf.setImage(with: url) { result in
    // `result` is either a `.success(RetrieveImageResult)` or a `.failure(KingfisherError)`
    switch result {
    case .success(let value):
        // The image was set to image view:
        print(value.image)

        // From where the image was retrieved:
        // - .none - Just downloaded.
        // - .memory - Got from memory cache.
        // - .disk - Got from disk cache.
        print(value.cacheType)

        // The source object which contains information like `url`.
        print(value.source)

    case .failure(let error):
        print(error) // The error happens
    }
}
```

## Downloading an image manually

Usually, you may use Kingfisher's view extension methods or KingfisherManager to retrieve an image. They will try to search in the cache first to prevent unnecessary download task. In some cases, `if you only want to download a target image without caching it`:

```swift
let downloader = ImageDownloader.default
downloader.downloadImage(with: url) { result in
    switch result {
    case .success(let value):
        print(value.image)
    case .failure(let error):
        print(error)
    }
}
```

## Download timeout

By default, the download timeout for a request is 15 seconds. To set it for the downloader:

```swift
// Set timeout to 1 minute.
downloader.downloadTimeout = 60
```

## Processor

```swift
// Just without anything
imageView.kf.setImage(with: url)
// It equals to
imageView.kf.setImage(with: url, options: [.processor(DefaultImageProcessor.default)])
```

```swift
// Round corner
let processor = RoundCornerImageProcessor(cornerRadius: 20)

// Downsampling
let processor = DownsamplingImageProcessor(size: CGSize(width: 100, height: 100))

// Cropping
let processor = CroppingImageProcessor(size: CGSize(width: 100, height: 100), anchor: CGPoint(x: 0.5, y: 0.5))

// Blur
let processor = BlurImageProcessor(blurRadius: 5.0)

// Overlay with a color & fraction
let processor = OverlayImageProcessor(overlay: .red, fraction: 0.7)

// Tint with a color
let processor = TintImageProcessor(tint: .blue)

// Adjust color
let processor = ColorControlsProcessor(brightness: 1.0, contrast: 0.7, saturation: 1.1, inputEV: 0.7)

// Black & White
let processor = BlackWhiteProcessor()

// Blend (iOS)
let processor = BlendImageProcessor(blendMode: .darken, alpha: 1.0, backgroundColor: .lightGray)

// Compositing
let processor = CompositingImageProcessor(compositingOperation: .darken, alpha: 1.0, backgroundColor: .lightGray)

// Use the process in view extension methods.
imageView.kf.setImage(with: url, options: [.processor(processor)])
```

## For `UIButton` & `NSButton`

```swift
let uiButton: UIButton = //...
uiButton.kf.setImage(with: url, for: .normal)
uiButton.kf.setBackgroundImage(with: url, for: .normal)

let nsButton: NSButton = //...
nsButton.kf.setImage(with: url)
nsButton.kf.setAlternateImage(with: url)
```
