## Storing Messages

While publishing a message, there is the option to also store the message. For each message you can define the total storage duration in seconds by using the ttl-argument. If you dont define a storage duration, the message is not stored. 


```javascript
// publish a message over the news/sports channel and store it
emitter.publish({
  key: "<channel key>",
  channel: "news/sports",
  ttl: 10080,
  message: "Messi scores another hattrick"
});
```

In the above example the message Messi scores another hattrick is stored in the Emitter broker for `10080` seconds (one week).

**Usecase example**: This could for example be used for a news website where users have an app to subscribe to topics (channels) to receive news feeds. In such a case published messages are immediately pushed to users who’s app has an internet connection. Users who’s app is not connected to the internet will receive the messages upon reconnecting to the internet (and to Emitter).

## Retrieving Messages

While subscribing to a channel, there is an option to also retrieve messages that have been stored. This is done by the optional last argument, where you define how many messages you want to retrieve. The messages are always retrieved in the chronological sequence at which they have been stored. When leaving out the last argument, no messages are retrieved from history, only a subscription to newly published messages is established. 

```javascript
// connect, retrieve messages that have been stored at news/sports 
// channel and subscribe to receive all newly created messages
emitter.on('connect', function(){
  emitter.subscribe({
    key: "<channel key>",
    channel: "news/sports",
    last: 5
  });
});
```

In the above example the last 5 messages that have been stored at channel `news/sports` are retrieved. As long as the connection is live, from that moment on all newly published messages over that channel will immediately be received.

**Usecase example**: This could for example be used in an app where users are subscribing to receive news feeds for the topics (channels) of their interest. The app could for example synchronize each time the phone reconnects to the internet, or it could synchronize periodically. Synchronization means executing the above code. In such a case the last 5 messages are received per topic (channel). While the phone stays connected to the internet, new news feeds are immediately received.


The channel can be defined in any way as defined in section about [message filtering](/develop/message-filtering), so even if wildcards are used, the last messages will be received chronologically (in the sequence at which order at which they were stored).

