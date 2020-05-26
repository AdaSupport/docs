# Android SDK

## Table of content

1. [Prerequisites](#prerequisites)
2. [Integration](#integration) 
3. [Chat Frame Creation](#chat-frame-creation) 
     - [XML](#xml) 
     - [Programmatically](#programmatically) 
     - [Dialog](#dialog) 
     - [Activity](#activity)
     - [Send files](#send-files)
4. [Settings](#settings) 
5. [Actions](#actions) 
6. [Questions](#questions)

## Prerequisites
This document is intended for bot specialists and developers with working knowledge of Android development. It also assumes you have a native Android app into which you plan to integrate the Ada Android SDK.

## Integration 
#### Manual Installation
The Ada Android SDK can be installed manually from the `.aar` file. To get a download link please reach out to an Ada Bot Specialist.

To install the library you need to save the `.aar` file under your app module `libs` folder, for example `MyAwesomeProject/app/libs`, and include `*.aar` in application level `build.gradle` file:
```groovy
dependencies {
    implementation fileTree(dir: 'libs', include: ['*.jar', '*.aar'])
}
```
After that synchronize project.

You can also import a file using the Android Studio functionality. To do this, go to `File`->`New`->`New module`. In the appeared window select `Import .JAR/.AAR Package` and click `Next`. After that, specify path to the `.aar` file and the module name, and click `Finish`. 
 
Finally, add this line to your app `build.gradle` file:
```groovy
    implementation project(':new_module_name')
```

#### Gradle Integration
The Ada Android SDK can be installed using Gradle from the Ada Support Bintray repository.

To integrate the SDK, first add the following code to the project level `build.gradle` file:
```groovy
allprojects {
    repositories {
        maven {
            url "https://adasupport.bintray.com/Android-SDK"
        }
    }
}        
```
Next, add the dependency to the application level `build.gradle`:
```groovy
//  if your project has artifacts within the androidx namespace
implementation 'support.ada.embed:android-sdk-appcompat:1.1.2'

//  if your project uses Android Support Library
implementation 'support.ada.embed:android-sdk-appcompat-legacy:1.1.2'
```

## Chat Frame Creation 
After installing the SDK, you can start Ada chat in several ways.

#### XML 
The simplest way to start Ada Chat is via XML:
```xml
<support.ada.embed.widget.AdaEmbedView
        android:id="@+id/ada_chat_frame"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:ada_handle="ada-example" />
```
To display the view, the `ada_handle` parameter is required. If you specify it as an attribute, the chat will be displayed immediately upon attachment to the parent view. If it is not specified, then you can do it later programmatically using the method: 
`initialize(settings: AdaEmbedView.Settings)`

```kotlin
val adaView = findViewById(R.id.ada_chat_frame)

adaSettings = AdaEmbedView.Settings.Builder("ada-example")
    .build()
adaView.initialize(adaSettings)
```

Please note "ada-example" is being used for demonstration purposes. Be sure to modify the handle, as well as any other values as needed for your bot.

#### Programmatically 
To programmatically create the chat frame, you need to create a view object and pass context to the constructor. 

```kotlin
val adaView = AdaEmbedView(getContext())
```
After this, the view will be created, but will not be initialized. To do this call `initialize(settings: AdaEmbedView.Settings)`, passing the settings object as an argument. For example:

```kotlin
val adaSettings = AdaEmbedView.Settings.Builder("ada-example")
    .cluster("ca")
    .language("en")
    .build()
adaView.initialize(adaSettings)
```

Finally, to display the newly created chat frame on screen, simply add the view to your container.

#### Dialog 
To display a separate chat window on top of the current window, you can use `AdaEmbedDialog`.

First, create a dialog object and pass the settings to its arguments using a constant `AdaEmbedDialog.ARGUMENT_SETTINGS`:
```kotlin
val dialog = AdaEmbedDialog()
 adaDialog.arguments = Bundle().apply { 
    putParcelable(AdaEmbedDialog.ARGUMENT_SETTINGS, settings) 
 }
```
Then show it using the native Android `show` method: 
```kotlin
dialog.show(supportFragmentManager, AdaEmbedDialog.TAG)
```
Note that TAG is a simple class name, you can use any value you want.

#### Activity 
The SDK allows you to open the chat window in a separate window attached to the app navigation. For this you need to use `AdaEmbedActivity`. 

Create an `Intent` and put the settings in it with the key `AdaEmbedActiviti.EXTRA_SETTINGS` and run the activity:
```kotlin
val intent = Intent(context, AdaEmbedActivity::class.java)
intent.putExtra(AdaEmbedActivity.EXTRA_SETTINGS, settings)
context.startActivity(intent)
```
Note that you can use `AdaEmbedActivity` as a regular Android activity. For example, flags and actions can be added.

#### Send files
When using Ada Glass (for Zendesk live agent support), end users need to occasionally upload files. `AdaEmbedActivity` and `AdaEmbedDialog` already have this functionality, but if you work directly with `AdaEmbedView` you have to handle it yourself.

To do this you need to setup a callback to `AdaEmbedView`. This callback will be fired when a user requests to attach a file. When you get the URI you can invoke `filePickerCallback.onFileTaken(someUri)`; it will signal `AdaEmbedView` that the file is ready for attach. It's also possible to pass `null, which will cancel the request.

This operation doesn't require you to pass the file URI to callback immediately. You can save `filePickerCallback` and invoke the callback later (eg. take URI via file picker).

To notify `AdaEmbedView` that you are going to handle file attach you should return `true`.

```kotlin
adaView.filePickerCallback = { filePickerCallback ->
    filePickerCallback.onFileTaken(someUri)
    true
}
```

## Settings
Using the SDK, you can configure the initial chat settings for greater customization.

#### Cluster
Specifies the Kubernetes cluster to be used. Unless directed by an Ada team member, you will not need to change this value.

```xml
app:ada_cluster="ca"
```

#### Greeting
This can be used to customize the greeting messages that new users see. This is useful for setting view-specific greetings across your app. The greeting should correspond to the ID of the Answer you would like to use. The ID can be found in the URL of the corresponding Answer in the dashboard.

```xml
app:ada_greetings="5c59aaabd8269e0339979014"
```

#### Handle
The handle for your bot. This is a required field.

```xml
app:ada_handle="ada-example"
```

#### Language
Takes in a language code to programmatically set the bot language. Languages must first be turned on in the Settings > Multilingual page of your Ada dashboard. Language codes follow the [ISO 639-1 language format](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes).

```xml
app:ada_language="en"
```

#### Metafields
Used to pass meta information about a Chatter. This can be useful for tracking information about your end users, as well as for personalizing their experience. For example, you may wish to track the phone_number and name for conversation attribution. Once set, this information can be accessed in the email attachment from Handoff Form submissions, or via the Chatter modal in the Conversations page of your Ada dashboard. 

You can also set "meta fields" using XML. For this, you need to create a JSON file in the res/raw directory, and set the reference to the view declaration:


***raw/metafields.json***
```json
{
  "id":12244,
  "name": "John",
  "authorized": true
}
```

```xml
app:ada_metaFields="@raw/metafields"
```

#### Styles
The `styles` setting can be used to override default styles inside the Chat bot. The value of the string should be the CSS rule-set you wish to apply inside the Chat UI. A list of CSS selectors available for 
targetting can be found in the table below.

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

```xml
app:ada_styles="*{font-size: 14px !important;}"
```

#### Third Party Cookies
The SDK allows you to use third-party cookies. **Won't affect [Build.VERSION_CODES.KITKAT](https://developer.android.com/reference/android/os/Build.VERSION_CODES#KITKAT) or below.**
Apps that target [Build.VERSION_CODES.KITKAT](https://developer.android.com/reference/android/os/Build.VERSION_CODES#KITKAT) default to allowing third party cookies.
Apps targeting [Build.VERSION_CODES.LOLLIPOP](https://developer.android.com/reference/android/os/Build.VERSION_CODES#LOLLIPOP) or later default to disallowing third party cookies.

```xml
app:ada_accept_third_party_cookies = "true"
```

#### Builder Configuration
You can also configure the Ada bot programmatically using the`AdaEmbedView.Settings` class.

```kotlin
val adaSettings = AdaEmbedView.Settings.Builder("ada-example")
    .cluster("ca")
    .greetings("5c59aaabd8269e0339979014")
    .styles("*{font-size: 14px !important;}")
    .language("en")
    .metaFields(metaFieldsMap)
    .acceptThirdPartyCookies(true)
    .build()
```
## Actions
#### Delete history
Deletes the chatter used to fetch conversation logs for an end-user from storage. When a user opens a new Chat window, a new chatter will be generated.

To do this you need to call `deleteHistory()` in your `AdaEmbedView` instance:
```kotlin
adaView.deleteHistory()
```

#### Reset
Creates a new chatter and refreshes the Chat window. Reset can take an optional params: `language`, `metaFields`, and `greeting` to be changed for the new chatter.

To prevent creating a new chatter (and maintain conversation history), param `resetChatHistory` can be passed with a value of `false`.

To do this you need to call `reset()` in your `AdaEmbedView` instance:
```kotlin
adaView.reset()
```
Or with optional params:

```kotlin
val metaFields = hashMapOf("name" to "John", "age" to "20")
adaView.reset(language = "fr", metaFields = metaFields)
```

#### Change MetaFields
The SDK allows you to change your meta fields while the chat frame is displayed.

To do this you need to call `setMetaFields()` in your `AdaEmbedView` instance and pass your new meta fields as an argument:
```kotlin
val metaFields = hashMapOf("name" to "John", "age" to "20")
adaView.setMetaFields(metaFields)
```

#### AdaEmbedActivity & AdaEmbedDialog
To call actions in the `AdaEmbedActivity`, you first need to create your own activity that inherits `AdaEmbedActivity`. Then using the `getAdaView()` you can obtain the `AdaEmbedView` instance:
```kotlin
class MyCustomActivity : AdaEmbedActivity(){
    override fun onResume() {
        super.onResume()
        val adaView = getAdaView()
        adaView.setMetaFields(metaFields)
        adaView.deleteHistory()
        adaView.reset()
    }
}
```
To call actions in the `AdaEmbedDialog`, you can obtain `AdaEmbedDialog` instance from FragmentManager:
```kotlin
val adaDialog = supportFragmentManager.findFragmentByTag(AdaEmbedDialog.TAG) as AdaEmbedDialog
adaDialog.setMetaFields(metaFields)
adaDialog.deleteHistory()
adaDialog.reset()
```

## Questions
Need some help? Get in touch with us at [help@ada.support](mailto:help@ada.support).





