* Repository: [https://github.com/emitter-io/javascript](https://github.com/emitter-io/javascript)
* Maintainer: [Misakai Ltd.](http://misakai.com)
* NPM: [https://www.npmjs.com/package/emitter-io](https://www.npmjs.com/package/emitter-io)

### Description

This is the JavaScript library for the browser, built and maintained by emitter.io team. The underlying communication layer uses [MQTT.js](https://github.com/mqttjs/MQTT.js) and released under MIT license. More detailed documentation can be found in the GitHub repository: [https://github.com/emitter-io/javascript](https://github.com/emitter-io/javascript).

[![NPM](https://nodei.co/npm/emitter-io.png)](https://nodei.co/npm/emitter-io/)

* [Installation](#install)
* [Usage Example](#example)
* [API](#api)

<a name="install"></a>
## Installation

Emitter for NodeJS:
```
npm install emitter-io --save
```

<a name="example"></a>
## Usage Example

```javascript
var client = require('emitter-io').connect(); 

// once we're connected, subscribe to the 'chat' channel
client.subscribe({
	key: "<channel key>",
	channel: "chat"
});
    
// on every message, print it out
client.on('message', function(msg){
	console.log( msg.asString() );
});

// publish a message to the chat channel
client.publish({
	key: "<channel key>",
	channel: "chat/my_name",
	message: "hello, emitter!"
});
```