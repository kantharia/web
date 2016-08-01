## Introduction

Before jumping into the technical details, first a basic explanation of Emitter:

Emitter is a scalable publish-subscribe system that makes use of the MQTT protocol. The MQTT protocol is very light weight, which makes sure battery usage is minimized and bandwidth usage stays very low. An advantage of publish-subscribe systems is that communication between clients is asynchronous. Clients do not need be online at the same time to communicate and clients do not need to know each other’s location (or IP-address). All clients can simply publish messages to a broker and can subscribe to topics to receive messages from that broker. In case of the Emitter cloud system the broker is scalable, so it is a mesh of servers that will always have overcapacity. In case you use the on-premise version of Emitter you can install the broker on your own server or server grid. 

Including the fact that Emitter was designed for low latency, this combination of properties makes Emitter very suitable for a wide range of applications, including IoT, online games, web applications and much more.

In the remainder of this documentation we will mainly focus on what developers need to know to work with Emitter. This means the main focus is on the client side and not on the broker side. The four main operations a client can do are:

* **Connect** to Emitter broker.
* **Disconnect** from Emitter broker.
* **Publish** messages and optionally store published messages.
* **Subscribe** to channels to receive messages and optionally retrieve stored messages.
* **Unsubscribe** from channels to stop receiving messages.
* **Generate Keys** with appropriate security permissions.

Developers who are familiar with message queueing systems might think we forgot about ‘creating a topic/channel’ but with Emitter it is not necessary to explicitly do that, you can just start publishing to a channel.

## Connecting to Emitter

Before being able to publish messages or to subscribe to channels, each client needs to connect to the Emitter broker. 

```javascript
// connect to emitter service
var connection = emitter.connect();
```

## Disconnecting from Emitter

Once a client is connected, it will stay connected until it sends a disconnect message, or until it has not been able to ping the Emitter broker for more than 5 minutes.   

The following code needs to be executed to disconnect immediately. 

```javascript
// Disconnect from the broker
connection.disconnect();
```

## Publishing Messages

When publishing a message, the client needs to provide at least:
* A channel key
* A channel name

Although it is not mandatory, a message usually also has a body. Finally, you can define how long a message needs to be stored (in seconds). If you omit this part, the message will not be stored. An example of publishing a message is given below.

```javascript
// publish a message to the chat channel
emitter.publish({
    key: "<channel key>",
    channel: "chat/my_name",
    ttl: 1200,
    message: "hello, emitter!"
});
```

Note that you do not explicitly create a channel before starting to publish. A channel is automatically created upon generating a channel key.

## Channel Security And Authorization

There are 2 types of keys; the channel key and the secret key:
* **Secret key**: This key is provided to you on the emitter.io website in your personal [console](/login). The secret key can be used to generate channel keys. This can be done in the personal console, or via an API call (see code below). You can at any time request a new secret key from emitter.
* **Channel key**: Each channel has its own key. Whenever you publish a message you need to provide the channel key. An advantage of having a separate key per channel is that consequences of a lost or stolen key are relatively small. In that case a malicious party can only access one channel. You can at any moment make all channel keys invalid by requesting a new secret key from emitter. With this new secret key you can then create new channel keys for all channels as well. In the future there will be possibilities to change the channel key of one specific channel.

```javascript
emitter.keygen({
	key: "<your secret key>",
	channel: "chat",
	type: "rwls",
	ttl: 600
}); 
```

When creating a channel key, you can define the following things:
* **Channel**: The channel for which the channel key should be valid. For limitations and possibilities of channel names.
* **Type**: The type of access. You are free to generate multiple keys for one channel with different access types. This can take several values or any combination:
    * `r` - requests a key that can be used for reading (subscribing) from the channel.
    * `w` - requests a key that can be used for writing (publishing) to the channel.
    * `l` - requests a key that can be used for loading message history from the channel storage, if this flag is specified, `r` should also be set.
    * `s` - requests a key that can be used for storing messages in the channel storage, if this flag is specified, `w` should also be set.
* **Ttl**: The time to live describes how long the channel key should be valid. The unit for this is seconds. When this time has passed the channel will still exist, but a new channel name needs to be generated to be able to publish or subscribe using this channel. A finite time to lives can be used to increase security. Ttl is optional, if you leave it empty the time to live is indefinitely. 

## Channel Structure

Section publish explained that a channel is automatically created if you create a channel key. You can define names for channels yourself. Examples of channel names are:
* `house/bedroom1/temperature`
* `game1/user5/zone3/x-coordinate`

A channel can consist of multiple levels with forward slashes between them. An advantage of using channels with multiple levels is that clients can use [filtering and wildcards to subscribe to parts of the complete channel](/develop/message-filtering). Channel names are case sensitive. Emitter supports the following characters:
* Digits: `0-9`
* Lowercase Characters: `a-z`
* Uppercase Characters: `A-Z`
* The following special characters `.`, `:`, `-`

## Messages and Limits

Emitter is a binary messaging system. This means you can send text, audio data, images, executables or any other message type you desire. The only limitation is the message size. By default this is limited to `64KB`. If you require a larger message size, please [contact us](/contact) so we can set up the system differently for you. If you try to send a message that is too large, the message will not be published and you will get an error message.

## Subscribing to Channels

When subscribing to a channel, the client needs to provide the key and the channel name. An example of subscribing to a message is given below.

```javascript
// once we're connected, subscribe to the 'chat' channel
emitter.on('connect', function(){
    emitter.subscribe({
        key: "<channel key>",
        channel: "chat",
        last: 5
    });
});
```

A client can connect to a complete channel, or single- or multi-level filtering can be used. The `last` argument is optional. It defines how many messages are retrieved from storage at the moment of connecting. If the argument is omitted, no messages are retrieved from history. This will only work if the channel key authorizations are defined to give access for retrieving from history. When retrieving from history, the last created messages are retrieved, they are retrieved in the same sequence at which they were stored.

## Unsubscribing from Channels

Finally, it is also possible to unsubscribe from the channel. 

```javascript
// unsubscribe from the 'chat' channel
emitter.unsubscribe({
     key: "<channel key>",
     channel: "chat"
});
```

The format of the request is similar to the subscription one, you'll need to provide the key with subscription permissions to the target channel and the channel name.
