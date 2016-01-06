* Repository: [https://github.com/emitter-io/csharp](https://github.com/emitter-io/csharp)
* Maintainer: [Misakai Ltd.](http://misakai.com)

### Overview

![](https://s3.amazonaws.com/cdn.misakai.com/www-emitter/blog/net_micro.gif)

Netduino uses .Net Micro Framework and our C# library currently supports .Net Micro Framework 4.2, 4.3 and 4.4. This library provides a nicer MQTT interface fine-tuned and extended with specific features provided by [emitter.io](http://emitter.io). The library is based upon the [M2MQTT library](http://www.m2mqtt.net
) written by [Paolo Patierno](https://m2mqtt.wordpress.com/who/) and released under Eclipse Public License.

### Build Notes
* To build for .Net Micro Framework 4.2 or 4.3, you need to download .Net Micro Framework SDK from CodePlex: https://netmf.codeplex.com/
* To build for .Net Micro Framework 4.4, you need to download .Net Micro Framework SDK from GitHub: https://github.com/NETMF/netmf-interpreter/releases

### Usage

Below is a sample program written using this C# client library. This demonstrates a simple console chat app where the messages are published to `chat` channel and every connected client receives them in real-time.

```
// Creating a connection to emitter.io service.
var emitter    = new Emitter.Connection();
var channelKey = "<channel key for 'chat' channel>";

// Connect to emitter.io service
emitter.Connect();

// Handle chat messages
emitter.On(channelKey, "chat", (channel, msg) =>
{
    Console.WriteLine(Encoding.UTF8.GetString(msg));
});

// Publish messages to the 'chat' channel
string text = "";
Console.WriteLine("Type to chat or 'q' to exit...");
do
{
    text = Console.ReadLine();
    emitter.Publish(channelKey, "chat", text);
}
while (text != "q");

// Disconnect the client
emitter.Disconnect();
```

To generate a channel key using the secret key provided, the `GenerateKey` method can be used.

```
// Generate a read-write key for our channel
emitter.GenerateKey(
    "<secret key>", 
    "chat", 
    Messages.EmitterKeyType.ReadWrite, 
    (response) => Console.WriteLine("Generated Key: " + response.Key)
    );
```
