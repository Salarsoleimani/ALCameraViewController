# ALCameraViewController - Forked and Modified

A camera view controller with custom image picker and image cropping. See bottom of the page for 
a summary of changes for this fork. For detailed changes see notes in source files.

![camera](https://cloud.githubusercontent.com/assets/932822/8455694/c61de812-2006-11e5-85c0-a57e3d980561.jpg)
![cropper](https://cloud.githubusercontent.com/assets/932822/8455697/c627ac44-2006-11e5-82be-7f96e73d9b1f.jpg)
![library](https://cloud.githubusercontent.com/assets/932822/8455695/c620ebb6-2006-11e5-9c61-75a81870c9de.jpg)
![permissions](https://cloud.githubusercontent.com/assets/932822/8455696/c62157fe-2006-11e5-958f-849cabf541ca.jpg)

### Features

- [x] Front facing and rear facing camera
- [x] Simple and clean look
- [x] Custom image picker with permission checking
- [x] Image cropping
- [x] Flash light
- [x] Zoom
- [x] Tap to focus

### Installation & Requirements
This project requires Xcode 9 to run and compiles with swift 4
> Note: This library makes use of the AVFoundation camera API's which are unavailable on the iOS simulator. You'll need a real device to run it.

CocoaPods:
Add the following to your Podfile:

```ruby
pod 'ALCameraViewController'
```
> For swift 3.2 support

```ruby
pod 'ALCameraViewController', '~> 2.0.3'
```

Carthage:

```ruby
github "alexlittlejohn/ALCameraViewController"
```


### Privacy (iOS 10) ###
If you are building your app with iOS 10 or newer, you need to add two privacy keys to your app to allow the usage of the camera and photo library, or your app will crash.

Add the keys below to your `Info.plist`, adding strings with the description you want to provide when prompting the user.

```
    NSPhotoLibraryUsageDescription
    NSCameraUsageDescription
```

### Usage

To use this component couldn't be simpler.
Add `import ALCameraViewController` to the top of your controller file.

In the viewController
```swift

let cameraViewController = CameraViewController { [weak self] image, asset in
	// Do something with your image here.
	self?.dismiss(animated: true, completion: nil)
}

present(cameraViewController, animated: true, completion: nil)
```

### Parameters

There are a number of configurable options available for `CameraViewController`

```swift
init(croppingParameters: CroppingParameters = CroppingParameters(),
     allowsLibraryAccess: Bool = true,
     allowsSwapCameraOrientation: Bool = true,
     allowVolumeButtonCapture: Bool = true,
     completion: @escaping CameraViewCompletion)
```

The Cropping Parameters struct accepts the following parameters

```swift
init(isEnabled: Bool = false,
     allowResizing: Bool = true,
     allowMoving: Bool = true,
     minimumSize: CGSize = CGSize(width: 60, height: 60))
```

The success parameter returns a `UIImage?` and a `PHAsset?` for more advanced use cases.
If the user canceled photo capture then both of these options will be `nil`

```swift
typealias CameraViewCompletion = (UIImage?, PHAsset?) -> Void
```
> Note: To prevent retain cycles, it is best to use a `[weak self]` reference within the success parameter

### Other usage options

You can also instantiate the image picker component by itself as well.
```swift

let croppingEnabled = true

/// Provides an image picker wrapped inside a UINavigationController instance
let imagePickerViewController = CameraViewController.imagePickerViewController(croppingEnabled: croppingEnabled) { [weak self] image, asset in
		// Do something with your image here.
	 	// If cropping is enabled this image will be the cropped version

    self?.dismiss(animated: true, completion: nil)
}

present(imagePickerViewController, animated: true, completion: nil)

```

For more control you can create it directly.
> Note: This approach requires some familiarity with the PhotoKit library provided by apple

```swift
import Photos

let imagePickerViewController = PhotoLibraryViewController()
imagePickerViewController.onSelectionComplete = { asset in

		// The asset could be nil if the user doesn't select anything
		guard let asset = asset else {
			return
		}

    // Provides a PHAsset object
		// Retrieve a UIImage from a PHAsset using
		let options = PHImageRequestOptions()
    options.deliveryMode = .highQualityFormat
    options.isNetworkAccessAllowed = true

		PHImageManager.default().requestImage(for: asset, targetSize: PHImageManagerMaximumSize, contentMode: .aspectFill, options: options) { image, _ in
        if let image = image {
						// Do something with your image here
        }
    }
}

present(imagePickerViewController, animated: true, completion: nil)

```


## License
ALCameraViewController is available under the MIT license. See the LICENSE file for more info.



# Changes in this fork

## Compiling

* Updated to Swift 5

## Cropping

* Image cropping now uses a rectangle with a specified aspect ratio rather than a square.  

* Added a center point circle in the middle of the crop rectangle that shows a touch point for moving the crop overlay. Previously, the user could move the crop overlay by dragging anywhere in the crop bounds, but this prevents pinch-resizing of the image except at the very edge. The resize corners still work as before.

## Camera

* Cleaned up the constraints for the flash, swap, and library buttons to work properly in both landscape orientations
* Update the overlay constraints when rotating, so the overlay is properly positioned and sized
* If the camera is not available (ie. on simulator), pop up an error rather than crashing


## Library Picker

* Adding a pinch gesture to increase or decrease the number of columns shown, up to a min or max value


## Confirm / Cropping View

* Significant updates to the presentation of the image, scrolling, and cropping to fix inconsistencies especially when switching between portrait and landscape.  See source file for full details.


## Example 

* Added a tap gesture on the displayed image to allow re-cropping the image (illustrates bringing up the crop view directly)
* The example was updated to show a crop aspect ratio of 1.2 


