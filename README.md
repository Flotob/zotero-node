Zotero-Node
===========
[![Build Status](https://travis-ci.org/inukshuk/zotero-node.svg?branch=master)](https://travis-ci.org/inukshuk/zotero-node)
[![Coverage Status](https://img.shields.io/coveralls/inukshuk/zotero-node.svg)](https://coveralls.io/r/inukshuk/zotero-node?branch=master)

A Zotero API client package for Node.js. This package tries to make it
as easy as possible to bootstrap a Zotero client application in Node.js;
it comes with hardly any runtime dependencies and provides three simple
abstractions to interact with Zotero: `Client`, `Library`, and `Message`.

Clients handle the HTTPS connection to a Zotero data server, observing
any rate-limiting directives issued by the server; you can configure
settings (like API versions, default headers etc.) for each Client.
Each Library represents a Zotero user or group library and is associated
with a Client instance; a Library offers many convenience methods to
make it easy to construct Zotero API requests. Each request and the
corresponding response are then encapsulated in a Message instance, wich
provides accessors and an extendable body parser collection to handle
the various formats supported by Zotero.

Quickstart
----------
Install the NPM package:

    $ npm install zotero

And:

    var zotero = require('zotero');

Now you can use the `zotero.Client` and `zotero.Library` constructors;
the latter is also aliased to the `zotero` namespace. Let's access the
Library of Zotero's public user:

    var lib = zotero({ user: '475425' });

When creating a Library you can pass-in a Client instance using the
`client` property; if you don't, a default Client will be created for
you. You can access the client through the library:

    > // Make the client use version 2 of the Zotero API
    > lib.client.version = 2;

    > // The default HTTP headers used by the client
    > lib.client.options.headers;
    { 'Zotero-API-Version': '2', 'User-Agent': 'zotero-node/0.0.1' }

    > // Let's make the client re-use the TCP connection to the server
    > lib.client.persist = true;
    > lib.client.options.headers['Connection'];
    'keep-alive'

To send requests to Zotero you can now use `Library#get(path, options, callback)`,
or use the convenience methods baked into Zotero-Node:

    > lib.items();
    // Will call /users/475425/items

    > lib.items.top({ limit: 3 })
    // Will call /users/475425/items/top?limit=3

    > lib.items('KWENT2ZM', { format: 'csljson' });
    // Will call /users/475425/items/KWENT2ZM?format=csljson

And so on; all valid Zotero API paths can be created this way (but you can also
use the `#get` method with any path yourself). All of these path methods allow
you to pass options which will be added as URL parameters. The methods will return
a Message instance; at that moment, the Message will only contain the HTTP request
which has not been sent yet, allowing you to make alterations, set event handlers
and so on. You can also pass a callback function to the request methods, which will
receive the Message instance when the response has been received and parsed.

If you just want to quickly print information about a message, pass-in
`zotero.print` as the callback. It will print out information like this:

    > lib.items('KWENT2ZM', { format: 'csljson' }, zotero.print);

    zotero:node Path:     /users/475425/items/KWENT2ZM?format=csljson +4m
    zotero:node Status:   200 +1ms
    zotero:node Type:     json +0ms
    zotero:node Headers:  {"zotero-api-version":"1","content-length":"148","connection":"close","content-type":"application/vnd.citationstyles.csl+json"} +0ms
    zotero:node Content:  {"items":[{"id":"392648/KWENT2ZM","type":"webpage","title":"Zotero | Home","URL":"http://staging.zotero.net/","accessed":{"raw":"2011-06-28"}}]} +0ms


What about promises?
--------------------
Zotero-Node uses standard Node.js style callbacks out of the box, but you can
easily promisify the API. Simply call `zotero.promisify` passing in the Promise
implementation of your choice.

License
-------
Copyright 2014 Sylvester Keil. All rights reserved.

Zotero-Node is licensed under the AGPL3 license. See LICENSE for details.
