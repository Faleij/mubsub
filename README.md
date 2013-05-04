## mubsub

Mubsub is a pub/sub implementation for Node.js and MongoDB.  It utilizes Mongo's capped collections and tailable cursors to notify subscribers of inserted documents that match a given query.

## Example

```javascript
var mubsub = require('mubsub');

var client = mubsub('mongodb://localhost:27017/mubsub_example');
var channel = client.channel('test');

client.on('error', console.error);
channel.on('error', console.error);

channel.subscribe('bar', function(data) {
    console.log(data.foo); // => 'bar'
});

channel.subscribe('baz', function(doc) {
    console.log(doc.foo); // => 'baz'
});

channel.publish('bar', { foo: 'bar' });
channel.publish('baz', { foo: 'baz' });

```

## Usage

### Create a client

You can pass a Db instance of uri string. For more information about uri format visit http://mongodb.github.io/node-mongodb-native/driver-articles/mongoclient.html

```javascript
var mubsub = require('mubsub');

// Using uri
var client = mubsub('mongodb://localhost:27017/mubsub_example');

// Pass mongodb driver `Db` instance directly.
var client = mubsub(new Db(...));
```

### Channels

A channel maps one-to-one with a capped collection (Mubsub will create these if they do not already exist in the database).  Optionally specify the byte size of the collection or/and max number of documents in the collection when creating a channel:

```javascript
var channel = client.channel('foo', { size: 100000, max: 500 });
```

### Subscribe

```javascript
var subscription = channel.subscribe([event], callback);
```

Subscriptions register a callback to be called whenever a document matching the specified event is inserted (published) into the collection (channel).  You can omit the event to match all inserted documents. To later unsubscribe a particular callback, call `unsubscribe` on the returned subscription object:

```javascript
subscription.unsubscribe();
```

### Publish

```javascript
channel.publish(event, obj, [callback]);
```

Publishing a document simply inserts the document into the channel's capped collection. Callback is optional.

### Close

```javascript
client.close();
```

Closes the MongoDB connection.

### Events

```javascript

client.on('error', console.log);

channel.on('error', console.log);

```

Install
-------

    npm install mubsub

Tests
-----

    make test

You can optionally specify the MongoDB URI to be used for tests:

    MONGODB_URI=mongodb://localhost:27017/mubsub_tests make test
