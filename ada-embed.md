# Ada Embed ✨Beta✨

> Ada Embed is a small JavaScript application that embeds your **Ada** Chat bot into your web page.

![ada animated gif](https://user-images.githubusercontent.com/4740147/47372740-5b5dca80-d6b8-11e8-87e7-1b76d48370d8.gif "Ada Animated Gif")

## Table of Contents
1. [Prerequisites](#prerequisites)
2. [Quick Start](#quick-start)
    - [Turn on your bot](#1-turn-on-your-bot)
    - [Embed script](#2-embed-script)
3. [Configuring Your Bot](#configuring-your-bot)
    - [Settings](#settings)
    - [Actions](#actions)
4. [FAQ](#faq)
5. [Versioning](#versioning)
6. [Questions](#questions)

## Prerequisites
This document is intended for bot specialists and developers with working knowledge of HTML. For some of the advanced setup, basic knowledge of JavaScript is required. The document also assumes you have a hosted web page that you have write access to.

## Quick Start
Getting started with Ada is easy, just follow the steps below.

### 1. Turn on your bot
The first step towards adding your Ada Chat Bot to your web page is to turn on the Web Chat integration in your **Settings > Platforms** page.

<img width="600" alt="Ada Dashboard Chat Settings" src="https://user-images.githubusercontent.com/9045634/49764964-f2013d80-fc9e-11e8-8e8e-52ed7774b3bf.png">

### 2. Embed script
Once you have your website all ready-to-go, find the page where you'd like to embed the Ada Chat bot. This will be a `.html` file (or equivalent). Here you will need to paste the following into the `<head>...</head>` block. Be sure to replace the example data-handle with your own.

```html
<script 
  async 
  id="__ada" 
  data-handle="ada-example"
  src="https://static.ada.support/embed.js"
></script>
```

That's it! You should now see a small question mark button on the bottom right corner of your page. Clicking the button will toggle the Web Chat in and out of view.

[**You can see a working example here.**](https://jsfiddle.net/vr87utse/)

## Configuring Your Bot
Ada Embed supports numerous [settings](#settings) and [actions](#actions) to help you customize the look and behaviour of your bot. Settings are set in the `adaSettings` object when you [embed your script](#2-embed-script). They determine intrinsic properties like bot style and behaviour. Conversely, actions can be called at any point in time to toggle Chat, update user meta data, and more.

### Settings
Settings are set on the window object as `window.adaSettings = {}`. A full list of available settings is provided below:

#### `adaReadyCallback` `@type {Function}`
Specifies a callback function to be called when the Embed script has finished setting up. This is especially useful when Embed is loaded `asynchronously`. 

**Example:**
```html
<script type="text/javascript">
  window.adaSettings = {
    adaReadyCallback: () => {
      console.log("Ada Embed is done setting up. Chat support is now available.");
    },
    ... // The rest of your settings here
  }
</script>
```

#### `chatterTokenCallback` `@type {Function}`
Specifies a callback function to be called when the Chatter has been set. The Chatter token is passed to the callback as an argument.

**Example:**
```html
<script type="text/javascript">
  window.adaSettings = {
    chatterTokenCallback: (chatter) => {
      console.log("Do something with chatter token: ", chatter);
    },
    ... // The rest of your settings here
  }
</script>
```

#### `cluster` `@type {String}`
Specifies the Kubernetes cluster to be used. Unless directed by an Ada team member, you will not need to change this value.


#### `styles` `@type {String}`
The `styles` option can be used to override default styles inside the Web Chat iFrame. The value of the string should be the CSS rule-set you wish to apply inside the iFrame. A list of CSS selectors available for targetting can be found in the table below.

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
  window.adaSettings = {
    styles: "*{font-size: 14px !important;}#message-input{background-color: white; border: 1px solid #c1c1c1;}",
    ... // The rest of your settings here
  }
</script>
```

#### `greeting` `@type {String}`
This can be used to customize the greeting messages that new users see. This is useful for setting page-specific greetings across your app. The `greeting` should correspond to the ID of the Answer you would like to use. The ID can be found in the URL of the corresponding Answer in the dashboard.

**Example:**
```html
<script type="text/javascript">
  window.adaSettings = {
    greeting: "5c59aaabd8269e0339979014",
    ... // The rest of your settings here
  }
</script>
```

#### `hideMask` `@type {Boolean}`
When set to `true`, this will prevent the default mask from appearing over top of your site's content when opened on desktop.

**Example:**
```html
<script type="text/javascript">
  window.adaSettings = {
    hideMask: true,
    ... // The rest of your settings here
  }
</script>
```

#### `language` `@type {String}`
Takes in a language code to programatically set the bot language. Languages must first be turned on in the **Settings > Multilingual** page of your Ada dashboard. Language codes follow the [ISO 639-1 language format](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes).

**Example:**
```html
<script type="text/javascript">
  window.adaSettings = {
    language: "fr",
    ... // The rest of your settings here
  }
</script>
```

#### `metaFields` `@type {Object}`
Used to pass meta information about a Chatter. This can be useful for tracking information about your end users, as well as for personalizing their experience. For example, you may wish to track the `phone_number` and `name` for conversation attribution. Once set, this information can be accessed in the email attachment from Handoff Form submissions, or via the Chatter modal in the **Conversations** page of your Ada dashboard. Should you need to programatically change these values after bot setup, you can make use of the [setMetaFields](#setmetafieldsmetafields-param-object) action below.

**Example:**
```html
<script type="text/javascript">
  window.adaSettings = {
    metaFields: {
      phone_number: "(123) 456-7890",
      name: "Ada Lovelace"
    }
    ... // The rest of your settings here
  }
</script>
```

#### `mobileOverlay` `@type {Boolean}`
By default, the Web Chat will open as an overlay over your site on mobile devices. If you'd prefer to have it open in a new window, set this option to `false`. Note that setting to `false` will prevent the following features from working:
- [adaReadyCallback](#adareadycallback-type-function)
- [chatterTokenCallback](#chattertokencallback-type-function)
- [styles](#styles-type-string)
- [deleteHistory](#deleteHistory)
- [reset](#reset)
- [setMetaFields](#setmetafieldsmetafields-param-object)

#### `parentElement` `@type {String|Object}`
Specifies where to mount the `<iframe>` if the default side drawer is not desired. Accepts the `HTMLNode` or `id` of the desired parent element.

**Example:**
```html
<head>
  ...
  <script type="text/javascript">
    window.adaSettings = {
      parentElement: document.getElementById("custom-iframe"),
      ... // The rest of your settings here
    }
  </script>
</head>
<body>
  ...
  <div id="custom-iframe"></div>
  ...
</body>
```

#### `private` `@type {Boolean}`
If set to `true`, this will put Web Chat into "Private" mode. This will cause Web Chat to forget conversation history on refresh. This is effectively the same as setting your Web Chat platform persistence to "Forget After Reload" in the **Settings > Platforms** page of your Ada dashboard. More information on persistence can be found [here](https://adasupporthelp.zendesk.com/hc/en-us/articles/360018115813-Chatter-Persistence-Remembering-Conversations-).

---

### Actions
Actions can be called from the global `adaEmbed` object. A list of the available actions is available below:

#### `deleteHistory()`
Deletes the `chatter` used to fetch conversation logs for an end-user from storage. When a user opens a new Chat window a new `chatter` will be generated.

**Example:**
```javascript
adaEmbed.deleteHistory();
```

#### `reset()`
Creates a new `chatter` and refreshes the Chat window.

**Example:**
```javascript
adaEmbed.reset();
```

#### `setMetaFields(metaFields)` `@param {Object}`
Used to update `metaFields` after Chat has been opened. In most situations, the [metaFields](#metafields-type-object) settings object should be enough for user attribution. However, in cases where Ada Chat remains open while page changes occur (like in Single Page Applications), this methods may be useful.

**Example:**
```javascript
adaEmbed.setMetaFields({
  phone_number: "(123) 456-7890",
  name: "Ada Lovelace"
});
```

#### `start(adaSettings)` `@param {Object}`
Used to add the Ada Embed interface to your page. Takes in an optional object for setting customization.

**Example:**
```javascript
adaEmbed.start({
  language: "fr",
  mobileOverlay: true
});
```

#### `stop()`
Removes Ada Embed from your page.

**Example:**
```javascript
adaEmbed.stop();
```

#### `toggle()`
Can be used to programatically open / close the Chat window. This method cannot be used with the `parentElement` option.

**Example:**
```javascript
adaEmbed.toggle();
```

## FAQ
#### Q: How do I set up Ada Chat in mobile?
**A:** Ada Embed is currently only available as a Web App. For developers looking to integrate Ada into a mobile application, we recommend you include the URL of your Ada Chat bot directly [inside a Webview](https://github.com/AdaSupport/docs/blob/nh-chaperone-rewrite/embed-mobile.md). By not using Ada Embed you will lose access to the actions and settings interface detailed above. Fortunately, we have made many of these features available to you via query parameters in your Chat URL.

See the table below for a list of available parameters.

Key | Value | Description
--- | --- | ---
`greeting` | The greeting Answer ID |  Specify a custom greeting
`language` | A supported ISO 639-1 language |  Set the Web Chat language
`*` | Any other key is treated as a meta field | Used to pass meta informaton about an end user
`private` | `true` or 1 | Used to forget the Chat session on refresh.

**Example:**
```
https://ada-example.ada.support/chat/?language=fr&name=Ada
```

#### Q: How do I customize the look of the Ada Chat button?
**A:** There are many ways to do this, and ultimately this will be up to your team's developers. That being said, we recommend targetting the `button.ada-chat-button` element in your CSS and overriding existing styles.

Please note that we cannot guarantee custom changes will work with future versions of Ada Embed. In the near future we will support out-of-box client branding to simplify customization. 

**Example:**

```
// This line in your CSS
button.ada-chat-button {
  left: 24px; // This will position the Chat button on the left side of the screen
}
```
[**You can experiment with a working example here.**](https://jsfiddle.net/8t0wnpvL/)

#### Q: How do I have the bot only appear during certain hours?
**A:** If the bot should only appear during certain times, you will need to wrap your Ada scripts in a condition to check if the user is within scheduled operation hours. If you only require that certain messages be within a scheduled time window, we recommend that you make use of Scheduled Blocks in the **Answer** page of your Ada dashboard.

#### Q: How do I customize the look and feel of Ada Chat?
**A:** Basic customization, such as client tint colour, can be modified from the **Settings > General** page of your Ada dashboard. If you require additional customization, you can make use of the [styles](#styles-type-string) Embed option above.

#### Q: Ada Embed is rendered as soon as the page loads. How can I delay rendering?
**A:** The [Embed script](#2-embed-script) features a useful data attribute called `data-lazy`. When defined on your script, Ada Embed will not be triggered until you manually call [adaEmbed.start()](#startadasettings-param-object).

## Versioning
The Embed script found above is *versionless*. This means that the latest stable features will be made available to you without any changes to your code. Should you wish to test upcoming features before they are released to production, you may make use of `https://static.ada.support/embed.beta.js`. 

In rare situations you may wish to lock Embed to a specific version. You can find a list of available Embed releases [here](https://github.com/AdaSupport/embed/releases). Of course, using a static version means that new changes and improvements will not be available to you. Additionally, though we will make every effort to remain backward compatible, we at some point may require you to update your version. In such cases you will be notified by email *insert amount of time here* before we deprecate your version.

## Questions
Need some help? Get in touch with us at [help@ada.support](mailto:help@ada.support).
