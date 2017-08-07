# Chaperone
Chaperone (or Ada Chaperone) is a small JavaScript application that assists embedding your Ada chat bot into a web page you have control of. If you're trying to include an Ada chat bot as part of a web page, these instructions will get you started quickly.

Feel free to [contact me](mailto:david@ada.support) to ask questions about anything you see here.

## Set Up
The first step towards adding your Ada chat bot to your web page is to turn on the Web Chat integration in your settings (if you haven't done so already). The second step is to add the scripts to your page inside of your `<head>...</head>` block:

```html
<script src="https://static.ada.support/embed.465ddc36.min.js" charset="utf-8"></script>
```

The last step is to instantiate the Ada Chaperone client application with the script below. It is important that you add the script to the bottom of your document (after the `</body>` tag).

```html
</body>
<script type="text/javascript">
    var acmeAdaBot = new AdaChaperone('acme');
</script>
</html>
```

That's it! If you've turned on Web Chat in your settings, you should see a small question-mark button on the bottom right corner of your page which will toggle the Web Chat into and out of view. If not, please check your browsers console for any errors or [contact me](mailto:david@ada.support) for assistance.

## Advanced Set Up

Understandably, you may not want a Web Chat view taking over your application's window every time a user chats with Ada. In this case, you can inject the Web Chat `<iframe>` view into a parent element you control the style of. To do this, pass the element (`HTMLNode`) or the `id` attribute of the element you want to be the parent of the `AdaChaperone` `<iframe>` element as the second parameter in your instantiation call.

```html
<div id="myElement"><!-- Ada Web Chat <iframe> will go here --></div>
</body>
<script type="text/javascript">
    var acmeAdaBot = new AdaChaperone('acme', {
        "parentElement": "myElement",
        "customStyles": "*{color:yellow !important;}",
        "private": false
    });
</script>
</html>
```

## Methods
At any time in your application you can tell your `AdaChaperone` instance to do a handful of things. The available methods depend on how you instantiated the `AdaChaperone` instance.

### constructor(`clientHandle`, `options`, `callback`)
As described as above, you can specify three parameters which will dictate how your instance of `AdaChaperone` is created. More information in the table below:

Parameter | Types | Description
--- | --- | ---
`clientHandle` | `String` | This should be your bot's handle (your handle is **this**[.ada.support/app])
`options` | `Object` | Has three optional properties, `parentElement`, `customStyles`, and `private`. `parentElement` species where to build the `<iframe>` if the default side window is not desired. `customStyles` takes a string value which can be used to override default styles in the Chat iFrame. `private`, if set to `true`, will create a new private instance of the Chat iFrame (history will be empty).
`callback` | `Function` | Specifies a function to be called after the `<iframe>` is finished being set up.

### show()
If you didn't specify a custom element to be the parent element of the `<iframe>`, then this method will show the Web Chat view.

### hide()
If you didn't specify a custom element to be the parent element of the `<iframe>`, then this method will hide the Web Chat view.

### tellFrameTo(`message`)
Use this method to manipulate the chat `<iframe>` inside of the parent element on your page. Use the table below to see what you can tell the frame to do. All `message` values should be of type `String`.

`message` | Description
--- | --- | ---
`focus` | Give window focus to the message input in the `<iframe>`
`blur` | Take window focus away from the message input in the `<iframe>`
`reset` | Deletes chat history from local storage

<!-- `reset` | `String` | Reset the chat window's message history -->

### setMetaField(`str`, `object`)
You can use this method to set locally stored meta properties for the chatter. This is useful in combination with your Handoff Form as any meta data fields you set with this method will be carried through in the email attachment from the form submission so you could see, for example, a `user_id` of the person who created the ticket. See your Settings > Integrations view in your bot manager for more information on Handoff forms.

### destroy()
Tears down your Chaperone instance. You must do this if you wish to create a new Chaperone instance.

_Code Example:_

```js
// Instantiate our bot, as usual...
var acmeAdaBot = new AdaChaperone('acme');

// Let's imagine this is a function that is called when a user signs in to your site:
function acmeSignInFunction(email) {
    acmeAPI.signIn({
        email : email,
    }, function(authenticatedUser) { // Called back when the authentication happens
        // Once the sign in has happened, we can tell our Ada bot about it:
        acmeAdaBot.setMetaField('acme_user_id', authenticatedUser.id); // Now our bot knows the user_id of the person who signed in! This will be sent back to you if they make support tickets with our Handoff form
    });
}
```
