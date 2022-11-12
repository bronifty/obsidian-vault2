https://www.youtube.com/watch?v=As5FojxWViI

[Day 0](https://docs.synadia.com/guides/ngs-day-0)

- multiplayer (nats messaging)
- persistence (nats jetstream)
- nats websockets lib to tie it all together on frontend
- just write frontend code; use nats for all backend stuff

### Jetstream patterns

| Retention Policy | Pattern             | Description                                                  |   
|------------------|---------------------|--------------------------------------------------------------|
| Limits           | Stream              | N consumers can read (and re-read!) the stream independently |   |   |
| WorkQueue        | Queue               | Queue - Message consumption deletes messages                 |   |   |
| Interest         | Topic (Multi-Queue) | When all N consumers have read, messages are deleted         |   |   |

- With JetStream we temporally decouple publishers from subscribers enabling durable subscription and rich consumption patterns.
- You choose how a JetStream operates -- as a continuous and replayable stream, as a traditional queue, or as a multi- consumer topic!

```js
// nats kv add KV-STORE \

// --description 'KV store for key values' \

// --replicas 1 --storage file --history 10 --max-bucket-size 10485760 --max-value-size 131072

  

// initialize the kv store with user presence state

// nats kv put KV-STORE 'mic_on' \

// '{ "player1": "true", "player2": "false" }'

let mic_on = { player1: true, player2: false };

  

// add a new player; update key mic_on

// nats kv put KV-STORE 'mic_on' \

// '{ "player1": "true", "player2": "false", "player3": "false" }'

mic_on = { player1: true, player2: false, player3: false };

  

// update player2's mic state

// nats kv put KV-STORE 'mic_on' \

// '{ "player1": "true", "player2": "true", "player3": "false" }'

mic_on = { player1: true, player2: true, player3: false };

  

// get the current state of the key mic_on

// nats kv get KV-STORE mic_on

mic_on = { player1: 'true', player2: 'true', player3: 'false' };

  

// watch the key mic_on

// nats kv watch KV-STORE mic_on
```

- Instant Key-Value stores that you can place anywhere in the world, accessible from any application you connect to NGS. JetStream provides easy to use Key-Value access methods as an abstraction over message stream.

### Object Store
- Each Object store is a JetStream with ordered messages of a subject (taken together) materializing an _object_ (or blob). Object stores directly leverage blob-storage idioms like _add_, _get_, and _ls_. _Watches_ can be placed on an Object store so clients can be evented any object adds or deletes!
```shell
// create the object store

// nats object add OBJ-STORE \

// --description 'Object store for my files' \

// --max-bucket-size 10485760

  

// put a file into the object store

// nats object put OBJ-STORE './ad1234.jpg' --name 'ad1234.jpg'

  

// list the files in the object store

// nats object ls OBJ-STORE

  

// get a file from the object store

// nats object get OBJ-STORE ad1234.jpg

// nats object get OBJ-STORE 'ad1234.jpg' --output './tmp/edgecache-ad1234.jpg'

  

// watch object store for uploads or deletes

// nats object watch OBJ-STORE
```
- Instant blob storage that you can place anywhere in the world, accessible from any application you connect to NGS! JetStream provides easy-to-use access methods as an Object store abstraction over message stream.

[Day One](https://docs.synadia.com/guides/ngs-dayone-guide)

- context is a namespace for pub sub etc
```shell
nats context ls
```

- Known Contexts 
| Name | Description |                     
|-----------------------|---------------------------------------|
| cloudadmin            | synadia (Synadia Communications Inc.) |
| datascience                         | synadia (Synadia Communications Inc.) |
| ngsleafnode                         | synadia (Synadia Communications Inc.) |
| payauthorizer                       | synadia (Synadia Communications Inc.) |
| payworker                           | synadia (Synadia Communications Inc.) |
| synadia_optimistic_davinci_default* | synadia (Synadia Communications Inc.) |
| vehicle1                            | synadia (Synadia Communications Inc.) |
| vehicle2                            | synadia (Synadia Communications Inc.) |
| vehicle3                            | synadia (Synadia Communications Inc.) |

- Vehicle 1 publishes a message to subscribing vehicles by simple _subject_

```shell
nats sub vehicle --context vehicle2
```
12:57:31 Subscribing on vehicle

```shell
nats pub vehicle 'Stay in your lane please!' --context vehicle1
```
[#1] Received on "vehicle"
Stay in your lane please!

#### What Just Happened?
- vehicle 2 (context) subscribed to the "vehicle" topic/subject 

