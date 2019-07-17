## Classes

<dl>
<dt><a href="#WWX">WWX</a></dt>
<dd></dd>
</dl>

## Typedefs

<dl>
<dt><a href="#eventBusCallback">eventBusCallback</a> : <code>function</code></dt>
<dd><p>Callback for an event. If it is the &#39;*&#39; event, config param has two properties (type, event). If any other, takes type is optional.</p>
</dd>
</dl>

<a name="WWX"></a>

## WWX
**Kind**: global class  

* [WWX](#WWX)
    * [new WWX(config)](#new_WWX_new)
    * [.start()](#WWX+start)
    * [.on(config)](#WWX+on)
    * [.off(config)](#WWX+off)
    * [.publish(config)](#WWX+publish)
    * [.once(config)](#WWX+once)
    * [.updateConfigItem(config)](#WWX+updateConfigItem)
    * [.clear()](#WWX+clear)
    * [.destroy()](#WWX+destroy)

<a name="new_WWX_new"></a>

### new WWX(config)
Create new Watson Assistant Web Experience Instance


| Param | Type | Description |
| --- | --- | --- |
| config | <code>Object</code> | Options. |
| config.element | <code>Element</code> | The DOM element to render the chat to. |
| [config.language_pack] | <code>string</code> | Optional: One of "de", "en", "es", "fr", "it", "ja", or "pt-br". If not provided, his will be auto detected by browser preference. Defaults to English (en) if provided or detected language is not an accepted language. |
| [config.debug] | <code>Boolean</code> | Optional: Add a bunch of noisy console.log messages! Weee! |

<a name="WWX+start"></a>

### wwX.start()
Start the chat

**Kind**: instance method of [<code>WWX</code>](#WWX)  
<a name="WWX+on"></a>

### wwX.on(config)
Create new event listener

**Kind**: instance method of [<code>WWX</code>](#WWX)  

| Param | Type | Description |
| --- | --- | --- |
| config | <code>Object</code> | Options. |
| config.type | <code>string</code> | Event to listen to. e.g. '*', 'send', 'receive', 'error' See [./docs/EVENTS.md](./docs/EVENTS.md) |
| config.handler | [<code>eventBusCallback</code>](#eventBusCallback) | The callback that handles the response. |
| [config.bind] | <code>context</code> | Optional: A "this" value to bind `config.handler` to. |

<a name="WWX+off"></a>

### wwX.off(config)
Remove event listener

**Kind**: instance method of [<code>WWX</code>](#WWX)  

| Param | Type | Description |
| --- | --- | --- |
| config | <code>Object</code> | Options. |
| config.type | <code>string</code> | Event to no longer listen to. e.g. '*', 'send', 'receive', 'error' See [./docs/EVENTS.md](./docs/EVENTS.md) |
| config.handler | [<code>eventBusCallback</code>](#eventBusCallback) | A reference to the callback that handles the response. |

<a name="WWX+publish"></a>

### wwX.publish(config)
Publish an event

**Kind**: instance method of [<code>WWX</code>](#WWX)  

| Param | Type | Description |
| --- | --- | --- |
| config | <code>Object</code> | Options. |
| config.type | <code>string</code> | Event to publish. You can specify your own events. e.g. 'send', 'receive', 'error' See [./docs/EVENTS.md](./docs/EVENTS.md) |
| config.evt | <code>Object</code> | A data payload with the event. |

<a name="WWX+once"></a>

### wwX.once(config)
Create new event listener that turns 'off' when it is called.

**Kind**: instance method of [<code>WWX</code>](#WWX)  

| Param | Type | Description |
| --- | --- | --- |
| config | <code>Object</code> | Options. |
| config.type | <code>string</code> | Event to listen to. e.g. '*', 'send', 'receive', 'error' See [./docs/EVENTS.md](./docs/EVENTS.md)' |
| config.handler | [<code>eventBusCallback</code>](#eventBusCallback) | The callback that handles the response. |
| [config.bind] | <code>context</code> | Optional: A "this" value to bind `config.handler` to. |

<a name="WWX+updateConfigItem"></a>

### wwX.updateConfigItem(config)
Update an item in the base config.

**Kind**: instance method of [<code>WWX</code>](#WWX)  

| Param | Type | Description |
| --- | --- | --- |
| config | <code>Object</code> | Options. |
| config.key | <code>string</code> | Config option you are updating. |
| config.value | <code>any</code> | The new value |

<a name="WWX+clear"></a>

### wwX.clear()
Clear the UI and start a new conversation.

**Kind**: instance method of [<code>WWX</code>](#WWX)  
<a name="WWX+destroy"></a>

### wwX.destroy()
Destroy the chat widget and return initial content to the DOM.

**Kind**: instance method of [<code>WWX</code>](#WWX)  
<a name="eventBusCallback"></a>

## eventBusCallback : <code>function</code>
Callback for an event. If it is the '*' event, config param has two properties (type, event). If any other, takes type is optional.

**Kind**: global typedef  

| Param | Type | Description |
| --- | --- | --- |
| config | <code>Object</code> |  |
| config.evt | <code>Object</code> | event data |
| [config.type] | <code>string</code> | Optional: if '*' event, this is required. |

