# Ada iOS SDK

> The Ada iOS SDK is a small framework that is used to embed your **Ada** Chat bot into your native iOS application.

## Table of Contents
1. [Prerequisites](#prerequisites)
2. [Installation](#installation)
    - [Manual Integration](#option-1-manual-integration)
    - [CocoaPods](#option-2-cocoapods)
3. [Configuration](#configuration)
4. [API](#api)
5. [Questions](#questions)

## Prerequisites
This document is intended for bot specialists and developers with working knowledge of iOS development. It also assumes you have a native iOS app into which you plan to integrate the Ada iOS SDK.

## Installation
The Ada iOS SDK can be installed manually or using CocoaPods. The SDK supports iOS 10.x and up.

### Option 1: Manual Integration
1. Download the Ada iOS SDK framework here.
2. Using the Finder, drag the `AdaEmbed.framework` folder into your project. Ensure that the Copy groups options is selected.
3. Ensure your Deployment Target is set to 10.0, which is the minimum iOS version that the Ada iOS SDK is compatible with.
4. In the project settings under General, link the `AdaEmbed.framework` under the Embedded Binaries section.
5. Run script?

### Option 2: CocoaPods
> The Ada EmbedFramework CocoaPod is private. In order to use it you must first be added as a collaborator to the EmbedFrameworkSpec repository. If you would like access please reach out to an Ada Bot Specialist.

1. Add the private EmbedFrameworkSpec repo to your local Cocoapods installation with the command:
```
pod repo add EmbedFrameworkSpec https://github.com/AdaSupport/EmbedFrameworkSpec.git
```
2. Add the AdaEmbedFramework to your Podfile. You will also need to include the source URL of the spec repo at the top of your Podfile.

**Example:**
```
platform :ios, '10.0'

target 'CocoaTest' do
  use_frameworks!

  source "https://github.com/AdaSupport/EmbedFrameworkSpec.git"

  # Pods for CocoaTest
  pod "AdaEmbedFramework", "~>0.1.4"

end
```

3. Install the pod using: 
```
pod install
```

## Configuration
Once you have installed the Ada iOS SDK, you are ready to use it in your app! To start, import AdaEmbed into your controller:

```swift
`import EmbedFramework`
```
You can then create an instance of the AdaWebHost like so:
```swift
var adaFramework = AdaWebHost(handle: “ada-example”, cluster: "", language: "en", styles: "", greeting: "", metafields: [])
```
Be sure to sure modify `handle` and any other values as needed for your bot.

Finally, launch Ada using any of the 3 opening methods: launchModalWebSupport, launchNavWebSupport, or launchInjectingWebSupport.

## API
### Methods
#### `launchModalWebSupport(from viewController: UIViewController)`
Launches Ada Chat in a modal view overtop your current view.

**Example:**
```swift
adaFramework.launchModalWebSupport(from: self)
```

#### `launchNavWebSupport(from navController: UINavigationController)`
Pushes a view containing Ada Chat to the top of your navigational stack.

**Example:**
```swift
adaFramework.launchNavWebSupport(from: navigationController)
```

#### `launchInjectingWebSupport(into view: UIView)`
Launches Ada Chat into a specified subview.

**Example:**
```swift
adaFramework.launchInjectingWebSupport(into: injectingView)
```

#### `setMetaFields(_ fields: [String: Any])`
Used to set meta data for a chatter after instantiation. This is useful if you need to update user data after Ada Chat has already launched.

**Example:**
```swift
adaFramework.setMetaFields([
    "firstName": "Jane",
    "lastName": "Doe",
    "tier": "pro"
])
```

## Questions
Need some help? Get in touch with us at [help@ada.support](mailto:help@ada.support).