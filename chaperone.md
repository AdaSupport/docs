# Ada Chaperone

> Chaperone is a small JavaScript application that embeds your **Ada** Chat bot into your web page.

![ada animated gif](https://user-images.githubusercontent.com/4740147/47372740-5b5dca80-d6b8-11e8-87e7-1b76d48370d8.gif "Ada Animated Gif")

## Table of Contents
1. [Prerequisites](#prerequisites)
2. [Getting Started](#getting-started)
    - [Turn on your bot](#1-turn-on-your-bot)
    - [Embed Chaperone script](#2-embed-chaperone-script)
    - [Instantiate Chaperone](#3-instantiate-chaperone)
3. [Configuring Your Bot](#configuring-your-bot)
    - [Options](#options)
    - [Methods](#methods)
4. [FAQ](#faq)
5. [Versioning](#versioning)
6. [Questions](#questions)

## Prerequisites
This document is intended for bot specialists and developers with working knowledge of HTML. For some of the advanced setup, basic knowledge of JavaScript is required. The document also assumes you have a hosted web page that you have write access to.

## Getting Started
Getting started with Ada is easy, just follow the steps below.

### 1. Turn on your bot
The first step towards adding your Ada Chat Bot to your web page is to turn on the Web Chat integration in your **Settings > Platforms** page.

<img width="600" alt="Ada Dashboard Chat Settings" src="https://user-images.githubusercontent.com/9045634/49764964-f2013d80-fc9e-11e8-8e8e-52ed7774b3bf.png">

### 2. Embed Chaperone script
Once you have your website all ready-to-go, find the page where you'd like to embed the Ada Chat bot. This will be a `.html` file (or equivalent). Here you will need to paste the following into the `<head>...</head>` block:

```html
<script
  src="https://static.ada.support/embed.f8b282eb.min.js"
  integrity="sha384-A0EOe/GwB+wqNjGniuVFup2J8YuMTrTibM487C3K8qHcQkY4J5Ib2PcV9MIXbnxl"
  crossorigin="anonymous"
  charset="utf-8"
></script>
```

This snippet handles retrieving the Chaperone script from our CDN.

### 3. Instantiate Chaperone
The last step is to create a new Chaperone instance with the script below. You should add the script to the bottom of your document (after the `</body>` tag).

```html
...
</body>
<script type="text/javascript">
  const adaBot = new AdaChaperone('client-handle'); // Replace with your bot handle
</script>
</html>
```

That's it! You should now see a small question-mark button on the bottom right corner of your page. Clicking the button will toggle the Web Chat in and out of view.

[**You can see a working example here.**](https://jsfiddle.net/c8m8u2y4/175/)

## Configuring Your Bot
Chaperone supports numerous [options](#options) and [methods](#methods) to help you customize the look and behaviour of your bot. Options are set once when you [instantiate Chaperone](#3-instantiate-chaperone), and determine intrinsic properties like bot style and behaviour. Methods are actions that are called on the Chaperone instance.

#### `constructor(clientHandle, options, callback)`
You can specify three parameters which will dictate how your instance of `AdaChaperone` is created.

Parameter | Type | Description
--- | --- | ---
`clientHandle` | `String` | Your bot's handle<sup>1</sup>
`options` | `Object` | Options to customize the Ada bot. See [Options](#options) below
`callback` | `Function` | Specifies a function to be called after the `<iframe>` is finished being set up

<sup>1</sup> The handle of your bot is generally the name of your company. This will be provided to you by your account manager.

**Example:**
```html
<script type="text/javascript">
  const adaBot = new AdaChaperone('client-handle', {
    "parentElement": "myElement",
    "customStyles": "*{color:blue !important;}",
    "private": true
  }, () => {
    console.log("Callback triggered!");
  });
</script>
</html>
```
---

### Options
Options are passed to Chaperone as an object in the second parameter of your instantiation call. A full list of available options is provided below:

#### `chatterTokenCallback` `@type {Function}`
Specifies a callback function to be called when the Chatter has been set. The Chatter token is passed to the callback as an argument.

**Example:**
```html
<script type="text/javascript">
  const adaBot = new AdaChaperone('client-handle', {
    "chatterTokenCallback": (chatter) => {
      console.log("Do something with chatter token: ", chatter);
    }
  });
</script>
```

#### `cluster` `@type {String}`
Specifies the Kubernetes cluster to be used. Unless directed by an Ada team member, you will not need to change this value.


#### `customStyles` `@type {String}`
The `customStyles` option can be used to override default styles inside the Web Chat iFrame. The value of the string should be the CSS rule-set you wish to apply inside the iFrame. A list of CSS selectors available for targetting can be found in the table below.

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
```html
<script type="text/javascript">
  const adaBot = new AdaChaperone('client-handle', {
    "customStyles": "*{font-size: 14px !important;}#message-input{background-color: white; border: 1px solid #c1c1c1;}"
  });
</script>
```

#### `greetingHandle` `@type {String}`
This can be used to customize the greeting messages that new users see. This is useful for setting page-specific greetings across your app. The `greetingHandle` should correspond to the title of the Answer you would like to use.

**Example:**
```html
<script type="text/javascript">
  const adaBot = new AdaChaperone('client-handle', {
    "greetingHandle": "My Answer"
  });
</script>
```

#### `language` `@type {String}`
Takes in a language code to programatically set the bot language. Languages must first be turned on in the **Settings > Multilingual** page of your Ada dashboard. Language codes follow the [ISO 639-1 language format](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes).

**Example:**
```html
<script type="text/javascript">
  const adaBot = new AdaChaperone('client-handle', {
    "language": "fr" // Set bot to french
  });
</script>
```

#### `mobileOverlay` `@type {Boolean}`
By default, the Web Chat will open in a new window on mobile devices. If you'd prefer to have it open as an overlay overtop the current window, set this option to `true`.

#### `parentElement` `@type {String|Object}`
Specifies where to mount the `<iframe>` if the default side drawer is not desired. Accepts the `HTMLNode` or `id` of the desired parent element.

**Example:**
```html
<body>
  ...
  <div id="custom-iframe"></div>
  ...
</body>
<script type="text/javascript">
  const adaBot = new AdaChaperone('client-handle', {
    "parentElement": document.getElementById("custom-iframe")
  });
</script>
```

#### `private` `@type {Boolean}`
If set to `true`, this will put Web Chat into "Private" mode. This will cause Web Chat to forget conversation history on refresh. This is effectively the same as setting your Web Chat platform persistence to "Forget After Reload" in the **Settings > Platforms** page of your Ada dashboard. More information on persistence can be found [here](https://ada.support/articles/chatter-persistence/).

---

### Methods
At any time you can tell your `AdaChaperone` instance to do a handful of things. The available methods depend on how you instantiated the `AdaChaperone` instance.

#### `destroy()`
Tears down your Chaperone instance. You must do this if you wish to create a new Chaperone instance.

#### `hide()`
Can be used to programatically close the Web Chat view. This method cannot be used with the `parentElement` option.


#### `setMetaField(fieldName, value)` `@param {String}` `@param {String}`
You can use this method to set meta properties for the Chatter. This can be useful for tracking information about your end users, as well as for personalizing their experience. For example, you may wish to track the `email` and `name` for conversation attribution. Once set, this information can be accessed in the email attachment from Handoff Form submissions, or via the Chatter modal in the **Conversations** page of your Ada dashboard. Additionally, the bot can be configured to call the user by name. You can learn more about personalization [here](https://ada.support/articles/personalization/).

**Example:**
```javascript
adaBot.setMetaField('email', 'joe-schmoe123@gmail.com');
adaBot.setMetaField('name', 'Joe Schmoe');
```

#### `show()`
Can be used to programatically open the Web Chat view. This is useful if you would like to open the Web Chat automatically when the page loads, or when clicking a custom button.

This method cannot be used with the `parentElement` option.

**Example:**
```html
<button onclick="adaBot.show()">Open Chat</button>
```

#### `tellFrameTo(message)` `@param {String}`
Use this method to manipulate the Web Chat `<iframe>`. Use the table below to see what you can tell the frame to do.

`message` | Description
--- | ---
`focus` | Give window focus to the message input in the `<iframe>`
`blur` | Take window focus away from the message input in the `<iframe>`
`reset` | Resets Chat UI with a new Chatter and history
`deleteHistory` | Clears storage so that a new Chatter will be created upon page refresh

## FAQ
#### Q: How do I set up Ada Chat in mobile?
**A:** Chaperone is only intended to be used in web apps, and is not suitable for native mobile applications. Instead, we recommend you include the URL of your Ada Chat bot [inside a Webview](https://github.com/AdaSupport/docs/blob/nh-chaperone-rewrite/embed-mobile.md). By not using Chaperone you will lose access to the Chaperone options and methods listed above. Fortunately, we have made many of these features available to you via query parameters in your Chat URL.

See the table below for a list of available parameters.

Key | Value | Type | Description
--- | --- | --- | ---
`greeting` | The greeting Answer ID | Query String | Specify a custom greeting
`language` | A supported ISO 639-1 language | Query String | Set the Web Chat language
`*` | Any other key is treated as a meta field | Query String | Used to pass meta informaton about an end user
`private` | `true` or 1 | Query String | Used to forget the Chat session on refresh.

#### Q: How do I customize the look of the Ada Chat button?
**A:** There are many ways to do this, and ultimately this will be up to your team's developers. That being said, we recommend targetting the `button.ada-chat-button` element in your CSS and overriding existing styles.

Please note that we cannot guarantee custom changes will work with future versions of Chaperone.

**Example:**

```
// This line in your CSS
button.ada-chat-button {
  left: 24px; // This will position the Chat button on the left side of the screen
}
```
[**You can experiment with a working example here.**](https://jsfiddle.net/2jh94oru/3/)

#### Q: How do I have the bot only appear during certain hours?
**A:** If the bot should only appear during certain times, you will need to wrap you Ada scripts in a condition to check if the user is within scheduled operation hours. If you only require that certain messages be within a scheduled time window, we recommend that you make use of Scheduled Blocks in the **Answer** page of your Ada dashboard.

#### Q: How do I customize the look and feel of Ada Chat?
**A:** Basic customization, such as client tint colour, can be modified from the **Settings > General** page of your Ada dashboard. If you require additional customization, you can make use of the [customStyles](#customstyles-type-string) Chaperone option above.

## Versioning
Currently, the embed script for Ada Chaperone is versioned. This means that in order to receive new Chaperone updates you will need to upgrade to the latest version. The latest embed script can always be found within this document, or within the Chat modal of the **Settings > Platforms** page in your Ada dashboard.

In the (near) future we intend to release a versionless Chaperone embed script. Stay tuned!

## Questions
Need some help? Get in touch with us at [help@ada.support](mailto:help@ada.support).
