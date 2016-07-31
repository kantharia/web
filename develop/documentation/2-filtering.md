## Message Filtering

Messages can be filtered by using wildcards in sub-channels or by subscribing to partial channels. These two filtering concepts will be explained through example: Suppose there is a house with one temperature sensor in each room of the house. Each sensor publishes its state once per minute. The inhabitants of the house have an app on their smart phones to check all temperatures, even if they are not at home.  Each sensor publishes its state in a fashion as shown below.

```javascript
// publish a message to a channel
emitter.publish({
  key: "<channel key>",
  channel: "house/firstfloor/bedroom1/temperature",
  message: "21 Celcius"
});
```

In this example the temperature sensor at bedroom 1 of the first floor has been published to the channel `house/firstfloor/bedroom1/temperature`. 

In order to receive the temperature at bedroom 1, one can simply subscribe to the channel of that particular sensor: 

```javascript
emitter.subscribe({
  key: "<channel key>",
  channel: "house/firstfloor/bedroom1/temperature"
});
```

## Filtering Using Wildcards

In order to receive all temperatures at the first floor, one should subscribe to a channel using the `+` as a wildcard:

```javascript
mitter.subscribe({
  key: "<channel key>",
  channel: "house/firstfloor/+/temperature"
}); 
```

In this fashion one could easily create a program to give the average temperature at the first floor. Even if an extra room would be added to the system no code needs to be changed on the subscriber side. In a similar way one can use multiple wildcards to receive all temperatures of the entire house:

```javascript
emitter.subscribe({
  key: "<channel key>",
  channel: "house/+/+/temperature"
});
```

The `+` serves as a wildcard and can be placed at any level of a channel name. There is no limitation in the number of wildcards used when subscribing, but it cannot be used to replace the root channel (`house` in this case).

## Filtering Using Partial Channels

Now suppose the user installs multiple temperature sensors in one room. In that case a publish statement could look as follows: 

```javascript
// publish a message to a channel
emitter.publish({
  key: "<channel key>",
  channel: "house/firstfloor/bedroom1/temperature/sensor2",
  message: "21 Celcius"
});
```

In that case the previously defined subscribe statements are all still valid. In the below example the temperature of all temperature sensors of bedroom 1 on the first floor will be received.  

```javascript
emitter.subscribe({
  key: "<channel key>",
  channel: "house/firstfloor/bedroom1/temperature"
});
```
By specifying only the first part of a complete channel, Emitter assumes you want to subscribe to all sub channels too. This also works in combination with wildcards. So you could subscribe to channel `house/+/+/temperature` in case you want to receive all temperature statuses.

If you simply want to receive all statuses of the first floor of the house, you could even simply subscribe to `house/firstfloor`. So by specifying only the first part of a complete channel,Emitter assumes you want to subscribe to all sub channels too (multi-level sub channels if applicable).


