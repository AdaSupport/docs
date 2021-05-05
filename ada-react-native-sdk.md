# React Native SDK

## Table of content
1. [Prerequisites](#prerequisites)
2. [Integration](#integration) 
3. [Usage](#usage) 
4. [Configuring Your Bot](#configuring-your-bot)
   - [Props](#props)
   - [Actions](#actions)
   - [Send files](#send-files)
7. [Questions](#questions)

## Prerequisites
This document is intended for bot specialists and developers with working knowledge of React Native development. It also assumes you have a React Native app into which you plan to integrate the Ada React Native SDK.

## Integration 
1. Ada React Native SDK is based on [react-native-webview](https://github.com/react-native-community/react-native-webview) so first, follow its instructions to add it to the project.
2. Then you can install Ada React Native SDK via NPM:
   
```
npm install --save @ada-support/react-native-sdk
```

## Usage
Import the `AdaEmbedView` component from `@ada-support/react-native-sdk` and use it like so:
```
// ...
import AdaEmbedView from '@ada-support/react-native-sdk';

// ...
class MyComponent extends React.Component {
  render() {
    return <AdaEmbedView handle="ada-example"/>;
  }
}
```

## Configuring Your Bot
Ada Embed supports numerous [props](#props) and [actions](#actions) to help you customize the look and behaviour of your bot.

### Props

#### Cluster `type: String`
Specifies the Kubernetes cluster to be used. Unless directed by an Ada team member, you will not need to change this value.

```
cluster="ca"
```

#### Greeting `type: String`
This can be used to customize the greeting messages that new users see. This is useful for setting view-specific greetings across your app. The greeting should correspond to the ID of the Answer you would like to use. The ID can be found in the URL of the corresponding Answer in the dashboard.

```
greetings="5c59aaabd8269e0339979014"
```

#### Handle `type: String`
The handle for your bot. This is a required field.

```
handle="ada-example"
```

#### Language `type: String`
Takes in a language code to programmatically set the bot language. Languages must first be turned on in the Settings > Multilingual page of your Ada dashboard. Language codes follow the [ISO 639-1 language format](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes).

```
language="en"
```

#### Metafields `type: Object`
Used to pass meta information about a Chatter. This can be useful for tracking information about your end users, as well as for personalizing their experience. For example, you may wish to track the phone_number and name for conversation attribution. Once set, this information can be accessed in the email attachment from Handoff Form submissions, or via the Chatter modal in the Conversations page of your Ada dashboard. 

```
metaFields={{
    name: "Some name",
    age: 30
}}
```

#### Third Party Cookies (Android only) `type: Boolen`
The SDK allows you to use third-party cookies. Use a boolean value to enable third party cookies in the AdaEmbedView. This feature need only be enabled for Android Lollipop and above, as third party cookies are enabled by default on Android Kitkat and below. The default value is `false`.

```
thirdPartyCookiesEnabled={true}
```

#### Auth Token Callback `type: Function`
The SDK allows you to periodically pass JWT tokens.
To do this you need to setup `zdChatterAuthCallback` on `AdaEmbedView`.
If the SDK requests a JWT token, this callback will be fired.
```
zdChatterAuthCallback={callback => {
    const token = getToken()
    callback(token)
}}
```

#### Event Callbacks `type: Object`
Used in conjunction with Custom JavaScript Event Blocks to trigger specified callbacks as part of a conversation.

The callback has type: `{ [eventName: string]: (jsEvent: object) => void }`. If you would like to catch any event, you can use ``` "*" ```.
```
eventCallbacks={{
    "*": (event) => {
        //This function will catch all events
        console.log(event.event_name)
    },
    my_event_name: (event) => {
        //This function will catch only events with specific name (my_event_name)
        console.log(event.event_name)
    }
}}
```

### Actions
Using a reference, actions can be called directly on a component without using state/props to trigger a re-render of the entire subtree. To create a reference:
```
// ...
import AdaEmbedView from '@ada-support/react-native-sdk';

// ...
class MyComponent extends React.Component {
  render() {
    return <AdaEmbedView 
            handle="ada-example"
            ref={ref => (this.adaEmbedView = ref)}/>;
  }
}
```

#### Delete history
Deletes the chatter used to fetch conversation logs for an end-user from storage. When a user opens a new Chat window, a new chatter will be generated.

```
this.adaEmbedView.deleteHistory();
```

#### Reset `type: Object`
Creates a new `chatter` and refreshes the Chat window. Reset can take an optional object allowing `language`, `metaFields`, and `greeting` to be changed for the new `chatter`.

To prevent creating a new `chatter` (and maintain conversation history), `resetChatHistory` can be passed to the object with a value of `false`.
```
this.adaEmbedView.reset();
```

```
this.adaEmbedView.reset({
    metaFields: {
        name: "Some name",
        age: 30
    },
    resetChatHistory: false
});
```

#### Change MetaFields `type: Object`
The SDK allows you to change your meta-fields while the chat frame is displayed.
```
this.adaEmbedView.setMetaFields({
    name: "Some name",
    age: 30
});
```

### Send files
IOS

For iOS, all you need to do is specify the permissions in your `ios/[project]/Info.plist` file:

Photo capture:

```
<key>NSCameraUsageDescription</key>
<string>Take pictures for certain activities</string>
```

Gallery selection:

```
<key>NSPhotoLibraryUsageDescription</key>
<string>Select pictures for certain activities</string>
```

Video recording:

```
<key>NSMicrophoneUsageDescription</key>
<string>Need microphone access for recording videos</string>
```

Android

Add permission in AndroidManifest.xml:

```
<manifest ...>
  ......

  <!-- this is required only for Android 4.1-5.1 (api 16-22)  -->
  <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />

  ......
</manifest>
```

## Questions
Need some help? Get in touch with us at [help@ada.support](mailto:help@ada.support).
