# Android SDK

## Table of content

1. [Prerequisites](#prerequisites)
2. [Integration](#integration) 
3. [Chat Frame Creation](#chat-frame-creation) 
     - [XML](#xml) 
     - [Programmatically](#programmatically) 
     - [Dialog](#dialog) 
     - [Activity](#activity) 
4. [Settings](#settings)
5. [Questions](#questions)

## Prerequisites
This document is intended for bot specialists and developers with 
working knowledge of Android development. It also assumes you have a native 
Android app into which you plan to integrate the Ada Android SDK.

## Integration
The Ada Android SDK can be installed using Gradle from the Ada Support Bintray 
repository.

> The Android SDK repository is private. In order to use it you must 
first be authorized in the Bintray service, and then be granted access to the 
Android SDK repository. To do that please reach out to an Ada Bot Specialist.

To integrate the SDK, first add the following code to the project level 
`build.gradle`:
```groovy
allprojects {
    repositories {
        maven {
            url "https://adasupport.bintray.com/Android-SDK"
        }
    }
}
```
Note that you should use your account `ApiKey` in the password field. `ApiKey` 
you can find in your profile settings.  

Next add core dependency to application level `build.gradle`:
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
To display the view, the `ada_handle` parameter is required. If you specify 
it as an attribute, the chat will be displayed immediately upon 
attachment to the parent view. If it is not specified, then you 
can do it later programmatically using the method: 
`initialize(settings: AdaEmbedView.Settings)`

```kotlin
val adaView = findViewById(R.id.ada_chat_frame)

adaSettings = AdaEmbedView.Settings.Builder("ada-example")
    .build()
adaView.initialize(adaSettings)
```

Please note "ada-example" is being used for demonstration purposes. 
Be sure to modify the handle, as well as any other values as needed 
for your bot.

#### Programmatically 

To programmatically create the chat frame, you need to create a view 
object and pass context to the constructor. 

```kotlin
val adaView = AdaEmbedView(getContext())
```
After this, the view will be created, but will not be initialized. To do this call 
`initialize(settings: AdaEmbedView.Settings)`, passing the settings object as an argument. 
For example:

```kotlin
val adaSettings = AdaEmbedView.Settings.Builder("ada-example")
    .cluster("ca")
    .language("en")
    .build()
adaView.initialize(adaSettings)
```

Finally, to display the newly created chat frame on screen, simply add the view to your container.


#### Dialog 
To display a separate chat window on top of the current window, 
you can use `AdaEmbedDialog`.

First, create a dialog object and pass the settings to its arguments using 
a constant `AdaEmbedDialog.ARGUMENT_SETTINGS`:
```kotlin
val dialog = AdaEmbedDialog()
dialog.arguments = Bundle().putParcelable(AdaEmbedDialog.ARGUMENT_SETTINGS, settings)
```
Then show it using the native Android `show` method: 
```kotlin
dialog.show(supportFragmentManager, AdaEmbedDialog.TAG)
```
Note that TAG is a simple class name, you can use any value you want.

#### Activity 
The SDK allows you to open the chat window in a separate window 
attached to the app navigation. For this you need to use `AdaEmbedActivity`. 

Create an `Intent` and put the settings in it with the key 
`AdaEmbedActiviti.EXTRA_SETTINGS` and run the activity:
```kotlin
val intent = Intent(context, AdaEmbedActivity::class.java)
intent.putExtra(AdaEmbedActivity.EXTRA_SETTINGS, settings)
context.startActivity(intent)
```
Note that you can use `AdaEmbedActivity` as a regular Android activity. 
For example, flags and actions can be added.

## Settings
Using the SDK, you can configure the initial chat settings for greater 
customization.
#### Cluster
Specifies the Kubernetes cluster to be used. Unless directed by an Ada 
team member, you will not need to change this value.

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
Takes in a language code to programmatically set the bot language. 
Languages must first be turned on in the Settings > Multilingual page 
of your Ada dashboard. Language codes follow the 
[ISO 639-1 language format](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes).

```xml
app:ada_language="en"
```

#### Metafields
Used to pass meta information about a Chatter. This can be useful for 
tracking information about your end users, as well as for personalizing 
their experience. For example, you may wish to track the phone_number and 
name for conversation attribution. Once set, this information can be 
accessed in the email attachment from Handoff Form submissions, or via 
the Chatter modal in the Conversations page of your Ada dashboard. 

You can also set "meta fields" using XML. For this, you need to create a JSON file 
in the res/raw directory, and set the reference to the view declaration:


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
The `styles` setting can be used to override default styles inside the 
Chat bot. The value of the string should be the CSS rule-set you wish to 
apply inside the Chat UI. A list of CSS selectors available for 
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

#### Builder Configuration

You can also configure the Ada bot programmatically using the
`AdaEmbedView.Settings` class.

```kotlin
val adaSettings = AdaEmbedView.Settings.Builder("ada-example")
            .cluster("ca")
            .greetings("5c59aaabd8269e0339979014")
            .styles("*{font-size: 14px !important;}")
            .language("en")
            .metaFields(metaFieldsMap)
            .build()
```
#### Change MetaFields

The SDK allows you to change your meta fields while the chat frame is displayed.

To do this you need to call `setMetaFields()` in your `AdaEmbedView` 
instance and pass your new meta fields as an argument:
```kotlin
val metaFields = hashMapOf("name" to "John", "age" to "20")
adaView.setMetaFields(metaFields)
```

To change meta fields in `AdaEmbedActivity`, you first need to create your own 
activity that inherits `AdaEmbedActivity` and run it. Then using the `getAdaView()` 
you can get the `AdaEmbedView`:
```kotlin
class MyCustomActivity : AdaEmbedActivity(){
    override fun onResume() {
        super.onResume()
        val adaView = getAdaView()
        adaView.setMetaFields(metaFields)
    }
}
```
To change meta fields in a dialog, you need to get its instance and use 
the `setMetaFields()`:
```kotlin
val adaDialog = supportFragmentManager.findFragmentByTag(AdaEmbedDialog.TAG) as AdaEmbedDialog
adaDialog.setMetaFields(metaFields)
```

## Questions
Need some help? Get in touch with us at [help@ada.support](mailto:help@ada.support).





