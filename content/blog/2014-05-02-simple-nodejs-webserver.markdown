---
author: vishnur66
comments: false
date: 2014-05-02 18:49:56+00:00
layout: post
link: http://www.vishnu-tech.com/2014/05/simple-nodejs-webserver/
slug: simple-nodejs-webserver
title: Simple Nodejs WebServer
wordpress_id: 282
tags:
- forever
- nodejs
- npm
---

Node.js is built on Chrome's [V8 JavaScript Engine](http://code.google.com/p/V8). It's useful for a range of applications and it brings JavaScript to the server. Applications written in Node.js are event-driven. For example, if you wanted to create a file, you would provide a callback function to be called upon the file's successful creation. In this sort of model, your app doesn't become stuck waiting for events to finish executing.

I hope you have installed nodejs. Then you need to install "forever"


## Installing forever




forever, developed by nodejitsu, has functions to create applications that are always running—"forever" running. If the application dies, forever brings it back. It has built-in functionality to monitor processes. It also offers an API you can use to incorporate its features into your own code.




forever can be installed using npm. npm should have been installed as part of Node.js:




[![forever](https://www.vishnu-tech.com/wp-content/uploads/2014/05/forever.png)](https://www.vishnu-tech.com/wp-content/uploads/2014/05/forever.png)





## Running the app


Create the file index.js with the following contents:

[![index](https://www.vishnu-tech.com/wp-content/uploads/2014/05/index.png)](https://www.vishnu-tech.com/wp-content/uploads/2014/05/index.png)

Then run:

[![indexx](https://www.vishnu-tech.com/wp-content/uploads/2014/05/indexx1.png)](https://www.vishnu-tech.com/wp-content/uploads/2014/05/indexx1.png)

Point your browser to [http://127.0.0.1](http://127.0.0.1:8124/) or http://localhost. It should display the index.html" 

Now we can use _**forever **_to manage our application:, instead of running the above command.

[![foreverr](https://www.vishnu-tech.com/wp-content/uploads/2014/05/foreverr1.png)](https://www.vishnu-tech.com/wp-content/uploads/2014/05/foreverr1.png)

To check, open the browser and point it to the [http://127.0.0.1](http://127.0.0.1:8124/) or http://localhost. It should display the index.html


Stopping the app is similar:




[![stop](https://www.vishnu-tech.com/wp-content/uploads/2014/05/stop.png)](https://www.vishnu-tech.com/wp-content/uploads/2014/05/stop.png)





