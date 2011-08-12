CaPRet
===========

OER reuse tracking and analytics storage

CaPRet is a derivative of Hummingbird (https://github.com/mnutt/Hummingbird)

Description
---------------

CaPRet provides a script that can be pasted into OER related sites which then serves a 1x1 tracking pixel to users. At the time of copy the script adds license information.
In the browser's GET request it sends back tracking data generated by javascript.


Requirements
-------------------

 * node.js v0.2.0
 * npm v0.2.4
 * mongodb


Installation
--------------

    git clone git@github.com:tatemae/CaPRet.git
    cd CaPRet

    # Use npm to install the dependencies
    npm install

    # Copy the default configuration file
    cp config/app.json.sample config/app.json

    # To use the map, download MaxMind's GeoIP database and extract to the root directory:
    wget http://geolite.maxmind.com/download/geoip/database/GeoLiteCity.dat.gz
    gunzip GeoLiteCity.dat.gz


Running CaPRet
------------------------------

To start the analytics server, run the following:

    mongod &   (or start mongo some other way)
    node server.js

By default a dashboard will be run on port 8080.  You can disable it for production use in
config/app.json.  The dashboard is just html served out of public/; you can serve it using
any webserver.


Deployment
----------

Make sure to properly secure the dashboard if you don't want outside people to see it. The dashboard
httpServer's 'listen' function takes a second argument that is the interface to bind; typically you
would choose "127.0.0.1" to only allow access from localhost, or "0.0.0.0" to listen on all
interfaces.  In production you should change the instances of "localhost:8000" in public/index.html
to point to the server where you're hosting the dashboard.


Architecture Overview
---------------------

CaPRet is organized into two parts: a node.js-based tracking server that records user
activity via a tracking pixel, and a collection of javascript-based widgets that display that
activity.  The server records all activity in MongoDB and broadcasts it to the clients using
WebSockets if possible, and falling back to Flash sockets if necessary.

The CaPRet.WebSocket object receives websocket events from the server in the form of JSON
objects.  Individual widgets subscribe to a property in the JSON tree and register handler
functions to be called whenever that property is present.


Logging Customization
---------------------

Metrics are stored in lib/metrics and auto-loaded. Each metric contains a handler function that is
called every time a new user event occurs.  Metrics store data in the `data` object property which
gets emitted to clients in intervals specified by the metric. A basic example can be found in
lib/metrics/total_views.js. An example of how a metric can filter based on urls is in
lib/metric/sales.js.


Display Customization
---------------------

CaPRet comes with some stock widgets (Counter, Logger, Graph) that demonstrate how to hook into
the data provided by the node.js server.  For the minimum amount required to create a widget, see
public/js/widgets/logger.js.  A widget is an object whose prototype extends CaPRet.Base and
implements onMessage.


Specs
--------

    sudo gem install jspec
    jspec run --node


Tips
-----

 * To run the UI locally but stream data from your production server, use the url http://localhost:8080/?ws_server=your-host.com&ws_port=12345


Contributors
------------

 * Michael Nutt <michael@nuttnet.net>
 * Benny Wong <benny@bwong.net>
 * Justin Ball <justinball@gmail.com>


License
-------

CaPRet is licensed under the MIT License. (See LICENSE)
