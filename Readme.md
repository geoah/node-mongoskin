<a name='index'>

* [Goals](#goals)
* [Install](#install)
* [Quick Start](#quickstart)
    * [Connect easier](#quickstart-1)
    * [Server options and BSON options](#quickstart-2)
    * [Similar API with node-mongodb-native](#quickstart-3)
    * [Cursor easier](#quickstart-4)
    * [MVC helper](#quickstart-5)
* [Documentation](#documentation)
    * [Module](#module)
    * [SkinServer](#skinserver)
    * [SkinDb](#skindb)
    * [SkinCollection](#skincollection)
    * [SkinCursor](#skincursor)

<a name='goals'>

Goals
========

Mongoskin is an easy to use driver of mongodb for nodejs,
it support mongodb cluster,
database url connecting,
and additional javascript method binding.

It will provide full features of [node-mongodb-native](https://github.com/christkv/node-mongodb-native),
and make it [future](http://en.wikipedia.org/wiki/Future_%28programming%29).

[Back to index](#index)

<a name='install'></a>

Install
========

    npm install mongoskin

[Back to index](#index)


<a name='quickstart'></a>

Quick Start
========

 **Is mongoskin synchronized?**

Nop! It is asynchronized, it use [future](http://en.wikipedia.org/wiki/Future_%28programming%29).
**Mongoskin** is the future layer above [node-mongodb-native](https://github.com/christkv/node-mongodb-native)

<a name='quickstart-1'></a>

Connect easier
--------
You can connect to mongodb easier now.

    var mongo = require('mongoskin');
    mongo.db('localhost:27017/testdb').collection('blog').find().toArray(function(err, items){
        console.dir(items);
    })

<a name='quickstart-2'></a>

Server options and BSON options
--------
You can also set `auto_reconnect` options querystring.
And native_parser options will automatically set from wheather native_parser avariable.

    var mongo = require('mongoskin'),
        db = mongo.db('localhost:27017/test?auto_reconnect');

<a name='quickstart-3'></a>

Similar API with node-mongodb-native
--------
You can do everything that node-mongodb-native can do.

    db.createCollection(...);
    db.collection('user').ensureIndex([['username', 1]], true, function(err, replies){});
    db.collection('posts').hint = 'slug';
    db.collection('posts').findOne({slug: 'whats-up'}, function(err, post){
        // do something
    });

<a name='quickstart-4'></a>

Cursor easier
--------

    db.collection('posts').find().toArray(function(err, posts){
        // do something
    });

<a name='quickstart-5'></a>

MVC helper
--------

You can bind **additional methods** for collection.
It is very useful if you want to use MVC patterns with nodejs and mongodb.
You can also invoke collection by properties after bind,
it could simplfy your `require`.

    db.bind('posts', {
       findTop10 : function(fn){
         this.find({}, {limit:10, sort:[['views', -1]]}).toArray(fn);
       },
       removeTagWith : function(tag, fn){
         this.remove({tags:tag},fn);
       }
    });

    db.bind('comments');

    db.collection('posts').removeTagWith('delete', function(err, replies){
      //do something
    });

    db.posts.findTop10(function(err, topPosts){
      //do something
    });

    db.comments.find().toArray(function(err, comments){
      //do something
    });

[Back to index](#index)


<a name='documentation'>

Documentation
========

for more information, see the source.

[Back to index](#index)


<a name='module'>

Module
--------

### MongoSkin Url format

    [*://][username:password@]host[:port][/database][?auto_reconnect[=true|false]]`

e.g.

    localhost/blog
    mongo://admin:pass@127.0.0.1:27017/blog?auto_reconnect
    127.0.0.1?auto_reconnect=false


### bind(collectionName)

### bind(collectionName, SkinCollection)

### bind(collectionName, extendObject1, extendObject2 ...)

Bind [SkinCollection](#skincollection) to db properties. see [SkinDb.bind](#skindb-bind) for more information.

### db(databaseUrl)

Get or create instance of [SkinDb](#skindb).

### cluster(serverUrl1, serverUrl2, ...)

Create [SkinServer](#skinserver) of native [ServerCluster](https://github.com/christkv/node-mongodb-native/blob/master/lib/mongodb/connection.js#L221). e.g.

    var mongo = require('mongoskin');
    var cluster = mongo.cluster('192.168.0.1:27017', '192.168.0.2:27017', '192.168.0.3:27017')
    var db = cluster.db('dbname', 'admin', 'pass');

### pair(leftServerUrl, rightServerUrl)

Create [SkinServer](#skinserver) of native [ServerPair](https://github.com/christkv/node-mongodb-native/blob/master/lib/mongodb/connection.js#L190)

[Back to index](#index)

<a name='skinserver'>

SkinServer
--------

### SkinServer(server)

Construct SkinServer from native Server instance.

### db(dbname, username=null, password=null)

Construct [SkinDb](#skindb) from SkinServer.

[Back to index](#index)

<a name='skindb'>

SkinDb
--------

### SkinDb(db, username=null, password=null)

Construct SkinDb.

### open(callback)

Connect to database, retrieval native
[Db](https://github.com/christkv/node-mongodb-native/blob/master/lib/mongodb/db.js#L17)
instance, callback is function(err, db).

### collection(collectionName)

Retrieval [SkinCollection](#skincollection) instance of specified collection name.

<a name='skindb-bind'>

### bind(collectionName)

### bind(collectionName, SkinCollection)

### bind(collectionName, extendObject1, extendObject2 ...)

Bind [SkinCollection](#skincollection) to db properties as a shortcut to db.collection(name).
You can also bind additional methods to the SkinCollection, it is useful when
you want to reuse a complex operation. This will also affect
db.collection(name) method.

e.g.

    db.bind('book', {
        firstBook: function(fn){
            this.findOne(fn);
        }
    });
    db.book.firstBook(function(err, book){});

### all the methods from Db.prototype

See [Db](https://github.com/christkv/node-mongodb-native/blob/master/lib/mongodb/db.js#L17) of node-mongodb-native for more information.

[Back to index](#index)

<a name='skincollection'>

SkinCollection
--------

### open(callback)

Retrieval native
[Collection](https://github.com/christkv/node-mongodb-native/blob/master/lib/mongodb/collection.js#L45)
instance, callback is function(err, collection).

### id(hex)

Equivalent to

    db.bson_serilizer.ObjectID.createFromHexString(hex);

See [ObjectID.createFromHexString](https://github.com/christkv/node-mongodb-native/blob/master/lib/mongodb/bson/bson.js#L548)

### findItems(..., callback)

Equivalent to

    collection.find(..., function(err, cursor){
        cursor.toArray(callback);
    });

See [Collection.find](https://github.com/christkv/node-mongodb-native/blob/master/lib/mongodb/collection.js#L348)

### findEach(..., callback)

Equivalent to

    collection.find(..., function(err, cursor){
        cursor.each(callback);
    });

See [Collection.find](https://github.com/christkv/node-mongodb-native/blob/master/lib/mongodb/collection.js#L348)

### findById(id, ..., callback)

Equivalent to

    collection.findOne({_id, ObjectID.createFromHexString(id)}, ..., callback);

See [Collection.findOne](https://github.com/christkv/node-mongodb-native/blob/master/lib/mongodb/collection.js#L417)

### updateById(_id, ..., callback)

Equivalent to

    collection.update({_id, ObjectID.createFromHexString(id)}, ..., callback);

See [Collection.update](https://github.com/christkv/node-mongodb-native/blob/master/lib/mongodb/collection.js#L198)

### find(...)

If the last parameter is function, it is equivalent to native
[Collection.find](https://github.com/christkv/node-mongodb-native/blob/master/lib/mongodb/collection.js#L348)
method, else it will return a future [SkinCursor](#skincursor).

e.g.

    // callback
    db.book.find({}, function(err, cursor){/* do something */});
    // future SkinCursor
    db.book.find().toArray(function(err, books){/* do something */});

### all the methods from Collection.prototype

See [Collection](https://github.com/christkv/node-mongodb-native/blob/master/lib/mongodb/collection.js#L45) of node-mongodb-native for more information.

    checkCollectionName
    count
    createIndex
    distinct
    drop
    dropIndex
    dropIndexes
    ensureIndex
    find
    findAndModify
    findOne
    group
    indexInformation
    insert
    insertAll
    mapReduce
    normalizeHintField
    options
    remove
    rename
    save
    update

[Back to index](#index)

<a name='skincursor'>

SkinCursor
---------

See [Cursor](https://github.com/christkv/node-mongodb-native/blob/master/lib/mongodb/cursor.js#L1)
of node-mongodb-native for more information.

All these methods will return the SkinCursor itself.

    sort(keyOrList, [direction], [callback])
    limit(limit, [callback])
    skip(skip, [callback])
    batchSize(skip, [callback])

    toArray(callback)
    each(callback)
    count(callback)
    nextObject(callback)
    getMore(callback)
    explain(callback)


[Back to index](#index)
