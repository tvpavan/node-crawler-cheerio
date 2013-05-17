node-crawler-cheerio
------------

node-crawler-cheerio forks from node-crawler that aims to be the best crawling/scraping package for Node. It replaces jQuery by using cheerio, a more fast, flexible one but with less memory, which will make the crawler work more efficiently.

It features:
 * A clean, simple API
 * server-side DOM & automatic jQuery insertion
 * Configurable pool size and retries
 * Priority of requests
 * forceUTF8 mode to let node-crawler deal for you with charset detection and conversion
 * A local cache

The argument for creating this package was made at ParisJS #2 in 2010 ( [lightning talk slides](http://www.slideshare.net/sylvinus/web-crawling-with-nodejs) )

Help & Forks welcomed!

How to install
--------------

    $ npm install crawler


Crash course
------------

    var Crawler = require("crawler").Crawler;
    
    var c = new Crawler({
        "maxConnections":10,

        // This will be called for each crawled page
        "callback":function(error,result,$) {

            // $ is a jQuery instance scoped to the server-side DOM of the page
            $("#content a").each(function(index,a) {
                c.queue(a.href);
            });
        }
    });
    
    // Queue just one URL, with default callback
    c.queue("http://joshfire.com");

    // Queue a list of URLs
    c.queue(["http://jamendo.com/","http://tedxparis.com"]);
    
	// Queue URLs with custom callbacks & parameters
    c.queue([{
        "uri":"http://parishackers.org/",
        "jQuery":false,

        // The global callback won't be called
        "callback":function(error,result) {
            console.log("Grabbed",result.body.length,"bytes");
        }
    }]);

    // Queue some HTML code directly without grabbing (mostly for tests)
    c.queue([{
        "html":"<p>This is a <strong>test</strong></p>"
    }]);


Options reference
-----------------

You can pass these options to the Crawler() constructor if you want them to be global or as 
items in the queue() calls if you want them to be specific to that item (overwriting global options)

This options list is a strict superset of [mikeal's request options](https://github.com/mikeal/request#requestoptions-callback) and will be directly passed to
the request() method.

Basic request options:

 * uri: String, the URL you want to crawl
 * timeout : Number, in milliseconds        (Default 60000)
 * method, xxx: [All mikeal's requests options are accepted](https://github.com/mikeal/request#requestoptions-callback)

Callbacks:

 * callback(error, result, $): A request was completed
 * onDrain(): There is no more queued requests

Pool options:

 * maxConnections: Number, Size of the worker pool (Default 10),
 * priorityRange: Number, Range of acceptable priorities starting from 0 (Default 10),
 * priority: Number, Priority of this request (Default 5), 

Retry options:

 * retries: Number of retries if the request fails (Default 3),
 * retryTimeout: Number of milliseconds to wait before retrying (Default 10000),

Server-side DOM options:

 * jQuery: Boolean, if true creates a server-side DOM and adds jQuery (Default true)
 * jQueryUrl: String, path to the jQuery file you want to insert (Defaults to bundled jquery-1.8.1.min.js)
 * autoWindowClose: Boolean, if false you will have to close the window yourself with result.window.close(). Useful when your callback needs to continue having the window open until some async code has ran. (Default true)

Charset encoding:

 * forceUTF8: Boolean, if true will try to detect the page charset and convert it to UTF8 if necessary. Never worry about encoding anymore! (Default false),

Cache:    

 * cache: Boolean, if true stores requests in memory (Default false)
 * skipDuplicates: Boolean, if true skips URIs that were already crawled, without even calling callback() (Default false)

Other:

 * userAgent: String, defaults to "node-crawler/[version]"


Memory leaks
------------

When using timeouts, to avoid triggering [Node #3076](https://github.com/joyent/node/pull/3076) you should use Node > 0.8.14

There is now a complete memory leak test for node-crawler :)


	
ChangeLog
---------
0.1
 - Use cheerio to replace jquery
