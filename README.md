# Watson Assistant Web Experience

## Contents

- [Introduction](#introduction)
- [Configuration Options](#configuration-options)
- [Initializing your Web Chat](#initializing-your-web-chat)
- [Instance API](#instance-api)
- [Events](#events)
- [Event Callbacks](#event-callbacks)
- [Event Details](#event-details)

## Introduction

Welcome to the [Watson Assistant](https://www.ibm.com/cloud/watson-assistant/) Web Chat. Web Chat is an embedded chat experience you can put on your website with just a few lines of code that takes advantage of all the best and newest that Watson Assistant has to offer.

This repository is meant for developers that have deployed a Web Chat from Watson Assistant and are looking to embed, configure, customize and extend their Web Chat instance. Web Chat is only available to Plus or Dedicated Watson Assistant plans.

 When we refer to Web Chat in this documentation, we are referring to the "widget" code here. When we refer to Watson Assistant, we are referring to the Watson Assistant instance you have created in your Watson Assistant account.

## Configuration Options

When you create your Web Chat inside your Watson Assistant UI, you will be given a small embed code to put on your website that will look similar to...

```html
<script src="https://assistant-web.watsonplatform.net/loadWWX.js"></script>
<script>
  var options = {
    integrationID: 'YOUR_INTEGRATION_ID', // A UUID like '1d7e34d5-3952-4b86-90eb-7c7232b9b540'
    region: 'YOUR_REGION' // 'us-south', 'us-east', 'jp-tok' 'au-syd', 'eu-gb', 'eu-de', etc
  };
  window.loadWWX(options);
</script>
```

There are additional configuration options you can use to control how Web Chat behaves including setting language strings and enabling custom styling. Most users do not use optional configuration, those that do rarely go beyond `options.language`.

| Param | Type | Required | Default | Description |
| --- | --- | --- | --- | --- |
| options | <code>Object</code> | Required |  | Options. |
| options.integrationID | <code>string</code> | Required |  |The integration ID of your Web Chat integration. This is exposed as a UUID. e.g. '1d7e34d5-3952-4b86-90eb-7c7232b9b540' |
| options.region | <code>string</code> | Required |  |Which data center your integration was created in. e.g. 'us-south', 'us-east', 'jp-tok' 'au-syd', 'eu-gb', 'eu-de', etc |
| options.userID | <code>string</code> | Optional |  | An id to uniquely identify the user. This can be used for GDPR purposes to delete the user's data on request.
| options.subscriptionID | <code>string</code> | Optional | | If you have a premium account, this is ID of your subscription and it is required. If you need this, it will be provided in the snippet for you to copy and paste. If you don't need this, you won't see it.
| options.showLauncher | <code>boolean</code> | Optional | <code>true</code> | Render the chat launcher element used to open and close the chat window. If you elect to not show our built in chat launcher, you will be responsible for firing the [launcher:toggle](#launchertoggle-event), [launcher:open](#launcheropen-event) or [launcher:close](#launcherclose-event) events from your own chat launcher. Or, you can use `options.openChatByDefault` to just have the chat interface be open at initialization. |
| options.openChatByDefault | <code>boolean</code> | Optional | <code>false</code> | By default, the chat window will be rendered in a "closed" state. |
| options.languagePack | <code>Object</code> | Optional |  | An object with strings in the format of the `.json` files in [languages](languages). See [languages/README.md](languages/README.md) for more details. This setting will replace all of the strings normally provided by default based on your `options.locale`. It must contain a full set of strings since this is a replacement and not a merge.
| options.locale | <code>string</code> | Optional |  | Locale to use for UI strings and date string formatting. See [languages/README.md](languages/README.md) for available locales. If not provided, this will be auto detected by browser preference. Defaults to English US (en) if not provided or detected language is not an accepted language. |
| options.element | <code>Element</code> | Optional |  | The DOM element to render Web Chat to. By default, Web Chat will generate its own element to house the chat window and chat launcher. |
| options.debug | <code>boolean</code> |  Optional | <code>false</code> | Automatically adds a listener that will output a console message for each event that is fired.

## Initializing your Web Chat

`loadWWX` returns a promise that resolves when Web Chat has instantiated. This promise resolves with an instance with an API to subscribe to events and issue actions.

```html
<script src="https://assistant-web.watsonplatform.net/loadWWX.js"></script>
<script>
  var options = {
    integrationID: 'YOUR_INTEGRATION_ID',
    region: 'YOUR_REGION'
  };
  window.loadWWX(options).then(function(instance) {
    // Your handler
    function handler(obj) {
      console.log(obj.type, obj.data);
    }
    console.log('instance', instance);

    // console.log out details of any "receive" event
    instance.on({ type: "receive", handler: handler });
    // console.log out details of any "send" event
    instance.on({ type: "send", handler: handler });

    // 30 seconds later, unsubscribe from listening to "send" events
    setTimeout(function(){
      instance.off({ type: "send", handler: handler});
    }, 30000);
  });
</script>
```

## Instance API

The returned instance has an API to allow you to [manage events](#events) going to/coming from Web Chat. This instance also allows
you to ask the widget to perform certain actions. The event system is key to extending and manipulating Web Chat on your own webpage.

You can subscribe to events using the [`on`](#instance.on) and [`once`](#instance.once) methods. Events handler are called in the order in which they were registered.

* [instance](#instance) : <code>Object</code>
    * [.on(options)](#instance.on) ⇒ <code>instance</code>
    * [.off(options)](#instance.off) ⇒ <code>instance</code>
    * [.once(options)](#instance.once) ⇒ <code>instance</code>
    * [.send()](#instance.send)
    * [.updateLanguagePack()](#instance.updateLanguagePack)
    * [.updateUserID()](#instance.updateUserID)
    * [.updateLocale()](#instance.updateLocale) => <code>Promise</code>
    * [.getLocale()](#instance.getLocale) => <code>string</code>
    * [.toggleOpen()](#instance.toggleOpen)
    * [.openWindow()](#instance.openWindow)
    * [.destroy()](#instance.destroy)

<a name="instance.on"></a>
### instance.on(options) ⇒ <code>instance</code>
Subscribe to an event with a callback. You can register as many subscriptions to an event as you would like. They will be called in order starting from oldest registered.
 
**Returns**: <code>instance</code> - returns itself for chaining  

| Param | Type | Required | Default | Description |
| --- | --- | --- | --- | --- |
| options | <code>Object</code> | Required | | Options. |
| options.type | <code>string</code> | Required | | Event to listen to. e.g. '*', 'send', 'receive', 'error'. See [Events](#events). |
| options.handler | <code>function</code> | Required | | The callback that handles the response. See [Events](#events) for more details. |

**Example**
```js
  window.loadWWX(options).then(function(instance) {
    // Your handler
    function handler(obj) {
      console.log(obj.type, obj.data);
    }

    instance.on({ type: "receive", handler: handler });
    instance.on({ type: "send", handler: handler });

    // You can also pass AN ARRAY of objects to the method

    instance.on([
      { type: "receive", handler: handler },
      { type: "send", handler: handler }
    ]);

  });
```

<a name="instance.off"></a>
### instance.off(options) ⇒ <code>instance</code>
Remove subscriptions to events that have been added with [`on`](#instance.on). Once you remove a subscription, the callback handler will no longer
be called.

 
**Returns**: <code>instance</code> - returns itself for chaining  

| Param | Type | Required | Default | Description |
| --- | --- | --- | --- | --- |
| options | <code>Object</code> | Required |  | Options. |
| options.type | <code>string</code> | Required |  | Event to no longer listen to. e.g. '*', 'send', 'receive', 'error'. See [Events](#events). |
| options.handler | <code>function</code> | Optional |  | A reference to the callback that handles the response. We use this to remove the correct subscription. If not provided, all subscriptions you have registered to the event will be removed. |

**Example**
```js
  window.loadWWX(options).then(function(instance) {
    // Your handler
    function handler(obj) {
      console.log(obj.type, obj.data);
    }

    instance.on({ type: "receive", handler: handler });
    instance.on({ type: "send", handler: handler });

    instance.off({ type: "receive", handler: handler });
    instance.off({ type: "send", handler: handler });

    // You can also pass AN ARRAY of objects to the method

    instance.on([
      { type: "receive", handler: handler },
      { type: "send", handler: handler }
    ]);

    instance.off([
      { type: "receive", handler: handler },
      { type: "send", handler: handler }
    ]);

    // You can also just "unsubscribe from all" by not passing a handler
    instance.on({ type: "send", handler: handler });
    instance.off({ type: "send" });

  });
```

<a name="instance.once"></a>
### instance.once(options) ⇒ <code>instance</code>
Adds the given event handler as a listener for events of the given type. After the first event is handled, this
handler will automatically be removed.
 
**Returns**: <code>instance</code> - returns itself for chaining  

| Param | Type | Required | Default | Description |
| --- | --- | --- | --- | --- |
| options | <code>Object</code> | Required |  | Options. |
| options.type | <code>string</code> | Required |  | Event to subscribe to. e.g. '*', 'send', 'receive', 'error'. See [Events](#events). |
| options.handler | <code>function</code> | Required |  | The callback that handles the response. See [Events](#events). |

**Example**
```js
  window.loadWWX(options).then(function(instance) {
    // Your handler
    function handler(obj) {
      console.log(obj.type, obj.data);
    }

    var mockSendObject = {};

    instance.once({ type: "send", handler: handler });

    instance.send(mockSendObject);

    // The subscription is now removed.

    // You can also pass AN ARRAY of objects to the method

    instance.once([
      { type: "receive", handler: handler },
      { type: "send", handler: handler }
    ]);

  });
```

<a name="instance.send"></a>
### instance.send()
Sends the given message to the assistant on the remote server. This will result in a [`pre:send`](#event-presend) and [`send`](#event-send) event
being fired on the event bus.

**Returns**: <code>instance</code> - returns itself for chaining  

| Param | Type | Required | Default | Description |
| --- | --- | --- | --- | --- |
| message | <code>Object</code> | Required |  | A v2 message request object. |
| options | <code>Object</code> |  |  | Options. |
| options.silent | <code>Boolean</code> |  |  | If true, the message will be sent to your Assistant but will not display in the UI. |
 
**Example**
```js
var sendObject = {
  "input": {
    "message_type": "text",
    "text": "get human agent"
  }
};
var sendOptions = {
  "silent": true
}
instance.send(mockSendObject, sendOptions);
```

<a name="instance.updateLanguagePack"></a>
### instance.updateLanguagePack()
Updates the current language pack using the values from the provided language pack. This language pack does
not need to be complete; only the strings contained in it will be updated. Any strings that are missing will be
ignored and the current values will remain unchanged. You can use [getLocale](#instance.getLocale) to identify the
current locale if you need to make an update based on the user's locale.
 
**Example**
```js
var languagePack = {};
instance.updateLanguagePack(languagePack);
```

<a name="instance.updateLocale"></a>
### instance.updateLocale()
Updates the locale currently in use by the widget. If no language pack was originally provided, then a new one
that matches the given locale will also be loaded. If an unsupported locale is provided, the closest supported
locale will be chosen. For example, if the language requested is supported but not the region, that language
without a region will be selected. If the language is not supported, then the application will default to English.

Note: this function is asynchronous and the new language pack may require an asynchronous load to fetch.
 
**Returns**: <code>Promise</code> - returns a Promise that will resolve to the supported locale that was chosen.

**Example**
```js
instance.updateLocale('en-US');
```

<a name="instance.getLocale"></a>
### instance.getLocale()
Returns the current locale in use by the widget. This may not match the locale that was provided in the
original public configuration if that value was invalid or if the locale has been changed since then.

**Returns**: <code>string</code> - returns a string containing the language and possibly a region code. Example 
values include: 'en' and 'en-us'.  

**Example**
```js
console.log(instance.getLocale());
```

<a name="instance.updateUserID"></a>
### instance.updateUserID()
Sets the ID of the current user in use by the widget. This may only be used to set the ID if one is not already
set. It may not be used to change the ID from one user to another user.
 
**Example**
```js
instance.updateUserID('some-user-id');
```

<a name="instance.toggleOpen"></a>
### instance.toggleOpen()
Toggles the current open or closed state of the window.
 
**Example**
```js
instance.toggleOpen();
```

<a name="instance.openWindow"></a>
### instance.openWindow()
Opens the chat window if it is currently closed. This will fire the [`window:open`](#event-windowopen) event.
 
**Example**
```js
instance.openWindow();
```

<a name="instance.closeWindow"></a>
### instance.closeWindow()
Closes the chat window if it is currently open. This will fire the [`window:close`](#event-windowclose) event.
 
**Example**
```js
instance.closeWindow();
```

<a name="instance.destroy"></a>
### instance.destroy()
Destroy the Web Chat and return initial content to the DOM. The chat window and chat launcher will both be destroyed. All subscriptions to events will be removed.
 
**Example**
```js
instance.destroy();
```

## Events

The Web Chat uses an event system to speak with your website. With these events you can build your own
custom UI responses, send messages to your Assistant from your website code, or even have your website react to changes
of state within Web Chat. See [examples for how to use events in a variety of manners](examples/README.md).

Below is a list of the current list of events that are fired by Web Chat.

| Event | Fired When |
| --- | --- |
| [*](#event-wildcard) | The wildcard event (`*`) is fired with every single event. Callbacks subscribed to the wildcard event are called AFTER subscriptions to the actual event.
| [pre:send](#event-presend) | `pre:send` is fired when we send a message to your Assistant from user input in the chat window, and BEFORE we fire the `send` event. This is where you can manipulate the object to send to your Assistant.
| [send](#event-send) | `send` is fired after a message has been sent to your Assistant.
| [pre:receive](#event-prereceive) | `pre:receive` is fired when we receive a response from your Assistant and BEFORE we call `receive`. This is where you can manipulate the object we receive before processing.
| [receive](#event-receive) | `receive` is fired when we receive a response from your Assistant. | The response is rendered in your chat window if the `response_type` is recognized.
| [error](#event-error) | Anytime we encounter an error, including network, response format, or uncaught code errors, we fire the `error` event. |  We display an error where appropriate.
| [customResponse](#event-customResponse) | Whenever a `response_type` is encountered that is not recognized, this event is fired with the data from your Assistant.
| [window:open](#event-windowopen) | Fired whenever the chat window is opened.
| [window:close](#event-windowclose) | Fired whenever the chat window is closed.

## Event Callbacks

Callbacks are called in order of registration via the `on` or `once` method. With the exception of `pre:send` and `pre:receive` all data sent as params to your callbacks are deep clones. We do this to prevent accidental re-assignment of code in use by other methods, since objects are passed by reference in JavaScript.

The exception is the `pre:send` and `pre:receive` events DO provide you with a reference to the actual objects so you can manipulate them before they are passed on to the `send` or `receive` events. You might use the `pre:send` and `pre:receive` events to manage context, for instance.

Callbacks receive an object. See each individual event for what data is passed as part of `event`.

```javascript
{
  type: 'string',
  data: {} //object specific to event type
}
```

## Event Details

<a name="event-presend"></a>
### pre:send Event

*Description:* When sending a message from Web Chat, before we call the `send` event we call the `pre:send` event. The purpose of the `pre:send` event is to allow you to synchronously manipulate `event.data` before we pass on the object reference to `event.data` to the `send` event.

| Param | Type | Description |
| --- | --- | --- |
| event | <code>Object</code> | The event. |
| event.type | <code>String</code> | Event Type |
| event.data | <code>Object</code> | A v2 message API Request object. See [Docs](https://cloud.ibm.com/apidocs/assistant-v2#send-user-input-to-assistant). |

**Example**

```js
/**
 * Add "return_context" to all messages sent by Web Chat to your Assistant.
 */
function handler(event) {
  event.data.options = event.data.options || {}
  event.data.options.return_context = true;
}
instance.on({ type: "pre:send", handler: handler });
```

<a name="event-send"></a>
### send Event

*Description:* Fired after a message has been sent to your assistant.

| Param | Type | Description |
| --- | --- | --- |
| event | <code>Object</code> | The event. |
| event.type | <code>String</code> | Event Type |
| event.data | <code>Object</code> | A v2 message API Request object. See [Docs](https://cloud.ibm.com/apidocs/assistant-v2#send-user-input-to-assistant). |

**Example**

```js
function handler(obj) {
  console.log(obj.type, obj.data);
}
instance.on({ type: "send", handler: handler });
```

<a name="event-prereceive"></a>
### pre:receive Event

*Description:* When receiving a message from Watson Assistant, before we call the `receive` event we call the `pre:receive` event. The purpose of the `pre:receive` event is to allow you to synchronously manipulate `event.data` before we pass on the object reference to `event.data` to the `receive` event.

| Param | Type | Description |
| --- | --- | --- |
| event | <code>Object</code> | The event. |
| event.type | <code>String</code> | Event Type |
| event.data | <code>Object</code> | A v2 message API Response object. See [Docs](https://cloud.ibm.com/apidocs/assistant-v2#send-user-input-to-assistant). |

**Example**

```js
/**
 * Override our response_type for options with one of your own.
 */
function handler(event) {
  var generic = event.data.output.generic;
  for (var i = 0; i < generic.length; i++) {
    var item = generic[i];
    if (item.response_type === "options") {
      item.response_type = "my_custom_options_override";
    }
    if (item.response_type === "connect_to_agent") {
      item.response_type = "text";
      item.text = "All our agents are busy, please call us at 555-555-5555."
    }
  }
}
instance.on({ type: "pre:receive", handler: handler });
```

<a name="event-receive"></a>
### receive Event

*Description:* Used when we receive messages from your Assistant.

| Param | Type | Description |
| --- | --- | --- |
| event | <code>Object</code> | The event. |
| event.type | <code>String</code> | Event Type |
| event.data | <code>Object</code> | A v2 message API Response object. See [Docs](https://cloud.ibm.com/apidocs/assistant-v2#send-user-input-to-assistant). |

**Example**

```js
/**
 * Console.log out intents on every message you receive from your Assistant.
 */
function handler(event) {
  console.log('intents:', event.data.output.intents);
}
instance.on({ type: "receive", handler: handler });
var myMessage = {
  output: {
    generic: [
      { response_type: 'text', text: 'test' }
    ]
  }
};
instance.fire({ type: "receive", data: myMessage });
```

<a name="event-error"></a>
### error Event

*Description:*

| Param | Type | Description |
| --- | --- | --- |
| event | <code>Object</code> | The event. |
| event.type | <code>String</code> | Event Type |
| event.data | <code>Object</code> | Error Data |

<a name="event-customResponse"></a>
### customResponse Event

*Description:* When Web Chat receives a response_type from Watson Assistant, it fires this event and returns the message and a DOM element. You can choose to render a custom view or you can perform an action on your website or both.

| Param | Type | Description |
| --- | --- | --- |
| event | <code>Object</code> | The event. |
| event.type | <code>String</code> | Event Type |
| event.data | <code>Object</code> | Event Data |
| event.data.message | <code>Object</code> | A v2 message API Response object. See [Docs](https://cloud.ibm.com/apidocs/assistant-v2#send-user-input-to-assistant). |
| event.data.element | <code>Element</code> | A DOM element inside the chat window for you to render content to. |

**Example**

```js
function handler(event) {
  var type = event.type;
  var message = JSON.stringify(event.data.message);
  var element = event.data.element;
  if (type === "my_silly_response_type") {
    element.innerHTML = message;
  }
}
instance.on({ type: "customResponse", handler: handler });
```

<a name="event-windowopen"></a>
### window:open Event

*Description:*

| Param | Type | Description |
| --- | --- | --- |
| event | <code>Object</code> | The event. |
| event.type | <code>String</code> | Event Type |

<a name="event-windowclose"></a>
### window:close Event

*Description:*

| Param | Type | Description |
| --- | --- | --- |
| event | <code>Object</code> | The event. |
| event.type | <code>String</code> | Event Type |

<a name="event-wildcard"></a>
### Wildcard (*) Event

*Description:*

| Param | Type | Description |
| --- | --- | --- |
| event | <code>Object</code> | The event. |
| event.type | <code>String</code> | Event Type |
| event.data | <code>Object</code> | Event Data of whatever event above was fired |

**Example**

```js
function handler(event) {
  console.log(event.type, event.data);
}
instance.on({ type: "*", handler: handler });
```


## Examples

For a full list of examples on why and how to use these events, see the [Examples](examples/README.md).
