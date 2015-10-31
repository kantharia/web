* Repository: [https://github.com/emitter-io/javascript](https://github.com/emitter-io/javascript)
* Maintainer: [Misakai Ltd.](http://misakai.com)
* Distribution: [https://s3.amazonaws.com/js.emitter.io/1.0.0/emitter.js](https://s3.amazonaws.com/js.emitter.io/1.0.0/emitter.js)
* Minified: [https://s3.amazonaws.com/js.emitter.io/1.0.0/emitter.min.js](https://s3.amazonaws.com/js.emitter.io/1.0.0/emitter.min.js)

## Usage

```javascript
// once we're connected, subscribe to the 'chat' channel
emitter.on('connect', function(){
	emitter.subscribe({
		key: "<channel key>",
		channel: "chat"
	});
});

// on every message, print it out
emitter.on('message', function(msg){
	console.log( msg.asString() );
});

// publish a message to the chat channel
emitter.publish({
	key: "<channel key>",
	channel: "chat/my_name",
	message: "hello, emitter!"
});

// actually connect to emitter service
emitter.connect();
```
