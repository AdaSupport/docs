# React Native SDK

## Table of content

1. [Prerequisites](#prerequisites)
2. [Integration](#integration) 
3. [Usage](#usage) 
4. [Props](#props) 
5. [Actions](#actions) 
6. [Questions](#questions)

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
    return <AdaEmbedView handle={`ada-example`}/>;
  }
}
```

## Props

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
metaFields={{name: "Some name", age: 30}}
```

#### Styles `type: String`
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

```
styles="*{font-size: 14px !important;}"
```

#### Third Party Cookies (Android only) `type: Boolen`
The SDK allows you to use third-party cookies.
Boolean value to enable third party cookies in the AdaEmbedView. Used on Android Lollipop and above only as third party cookies are enabled by default on Android Kitkat and below. The default value is `false`.

```
thirdPartyCookiesEnabled={true}
```

#### Auth Token Callback `type: Function`
The SDK allows you to periodically pass JWT tokens.
To do this you need to setup `zdChatterAuthCallback` to `AdaEmbedView`.
If SDK requests JWT token this callback will be fired.
```
zdChatterAuthCallback={callback => {
    const token = getToken()
    callback(token)
}}
```

#### Event Callback `type: Object`
Used in conjunction with Custom JavaScript Event Blocks to trigger specified callbacks as part of a conversation.

Callback has type: `{ [eventName: string]: (jsEvent: object) => void }`. You can use name as ``` "*" ``` in this case this function will catch all event.
```
eventCallbacks={{
    ["*"]: (event) => {console.log(event.event_name)},
    my_event_name: (event) => {console.log(event.event_name)}
}}
```

## Actions
Actions can be called directly to a component without using state/props to trigger a re-render of the entire subtree, using reference. To create reference:
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

## Questions
Need some help? Get in touch with us at [help@ada.support](mailto:help@ada.support).
