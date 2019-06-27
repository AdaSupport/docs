# Ada iOS SDK

> The Ada iOS SDK is a small framework that is used to embed your **Ada** Chat bot into your native iOS application.

## Table of Contents
1. [Prerequisites](#prerequisites)
2. [Installation](#installation)
    - [Manual Integration](#option-1-manual-integration)
    - [CocoaPods](#option-2-cocoapods)
3. [Quick Start](#quick-start)
4. [API](#api)
    - [Settings](#settings)
    - [Methods](#methods)
5. [Questions](#questions)

## Prerequisites
This document is intended for bot specialists and developers with working knowledge of iOS development. It also assumes you have a native iOS app into which you plan to integrate the Ada iOS SDK.

## Installation
The Ada iOS SDK can be installed manually or using CocoaPods. The SDK supports iOS 10.x and up.

### Option 1: Manual Integration
1. Download the Ada iOS SDK framework [here](https://github.com/AdaSupport/EmbedFramework/releases/download/1.0.0/AdaEmbedFramework.framework.zip).
2. Right click on the project file in XCode, then click "Add Files to *MyProjectName*". Ensure that the *Copy groups* option is selected.
3. Ensure your Deployment Target is set to 10.0, which is the minimum iOS version that the Ada iOS SDK is compatible with.
4. In the project settings under General, link the `AdaEmbedFramework.framework` under the Embedded Binaries section. You can do this by dragging the framework from the left side list in the Embedded Binaries list.

### Option 2: CocoaPods
> The AdaEmbedFramework CocoaPod is private. In order to use it you must first be added as a collaborator to the EmbedFrameworkSpec repository. If you would like access please reach out to an Ada Bot Specialist.

1. Add the private EmbedFrameworkSpec repo to your local Cocoapods installation with the command:
```
pod repo add EmbedFrameworkSpec https://github.com/AdaSupport/EmbedFrameworkSpec.git
```
2. Add the AdaEmbedFramework to your Podfile. You will also need to include the source URL of the spec repo.

**Example:**
```
platform :ios, '10.0'

target 'CocoaTest' do
  use_frameworks!

  source "https://github.com/AdaSupport/EmbedFrameworkSpec.git"

  # Pods for CocoaTest
  pod "AdaEmbedFramework", "~>1.0.0"

end
```

3. Install the pod using: 
```
pod install
```

## Quick Start
Once you have installed the Ada iOS SDK, you are ready to use it in your app! To start, import AdaEmbedFramework into your controller:

```swift
import AdaEmbedFramework
```
You can then create an instance of the AdaWebHost like so:
```swift
var adaFramework = AdaWebHost(handle: “ada-example”)
```
Please note "ada-example" is being used for demonstration purposes. Be sure to sure modify `handle`, as well as any other values as needed for your bot.

Finally, launch Ada using any of the 3 opening methods: [launchModalWebSupport](#launchmodalwebsupportfrom-viewcontroller-uiviewcontroller), [launchNavWebSupport](#launchinjectingwebsupportinto-view-uiview), or [launchInjectingWebSupport](#launchinjectingwebsupportinto-view-uiview).

## API
### Settings
`AdaWebHost` can take various input parameters to customize the behaviour of your Chat bot.

#### `cluster: String = ""`
Specifies the Kubernetes cluster to be used. Unless directed by an Ada team member, you will not need to change this value.

**Example:**
```swift
var adaFramework = AdaWebHost(handle: “ada-example”, cluster: "ca")
```

#### `greeting: String = ""`
This can be used to customize the greeting messages that new users see. This is useful for setting view-specific greetings across your app. The greeting should correspond to the ID of the Answer you would like to use. The ID can be found in the URL of the corresponding Answer in the dashboard.

**Example:**
```swift
var adaFramework = AdaWebHost(handle: “ada-example”, greeting: "5c59aaabd8269e0339979014")
```

#### `handle: String`
The handle for your bot. This is a required field.

#### `language: String = ""`
Takes in a language code to programatically set the bot language. Languages must first be turned on in the Settings > Multilingual page of your Ada dashboard. Language codes follow the [ISO 639-1 language format](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes).

**Example:**
```swift
var adaFramework = AdaWebHost(handle: “ada-example”, language: "fr")
```

#### `metafields: [String: String]? = [:]`
Used to pass meta information about a Chatter. This can be useful for tracking information about your end users, as well as for personalizing their experience. For example, you may wish to track the phone_number and name for conversation attribution. Once set, this information can be accessed in the email attachment from Handoff Form submissions, or via the Chatter modal in the Conversations page of your Ada dashboard. Should you need to programatically change these values after bot setup, you can make use of the [setMetaFields](#setmetafields_-fields-string-any) method below.

**Example:**
```swift
var adaFramework = AdaWebHost(handle: “ada-example”, metafields: ["tier": "pro"])
```

#### `styles: String = ""`
The `styles` setting can be used to override default styles inside the Chat bot. The value of the string should be the CSS rule-set you wish to apply inside the Chat UI. A list of CSS selectors available for targetting can be found in the table below.

| WARNING: We do not recommend assigning styles to classes you inspect in the DOM. Class naming is subject to change, and can cause your custom styles to break. |
| --- |

Selector | Description
--- | ---
`#message-container` | The outer wrapper, containing the top bar, message list, and input bar
`#ada-close-button` | The button used to close the Web Chat window
`#input-bar` | The bottom wrapper, containg the textarea element, send button, and bottom text
`#message-input` | The textarea inside the input bar, used for user input
`#clear-message` | The button used to clear text from the message input
`#send-button ` | The button for submitting the user input
`#status-bar` | The bottom text inside the input bar
`#close-info-button` | The button to close the settings modal
`#language-selector` | The language select container
`#language-picker` | The language select element
`#terms-of-service` | The terms of service link
`#privacy` | The privacy link
`#messages-list` | The messages container
`#topBar` | The top bar container above the message list
`#info-button` | The settings modal button
`.g-message` | The base message selector
`.g-message--is-owned-by-user` | The selector for messages from the end user

**Example:**
```swift
var adaFramework = AdaWebHost(handle: “ada-example”, styles: "*{font-size: 14px !important;}")
```

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
