---
layout: post
title: "Deploying NodeJS using Express with NginX and Let's Encrypt"
published: true
---

As my New Years resolution, I decided to get back to work on my side project, [Find My Bus NJ](https://findmybusnj.com). As part of the 2.0 revision that I have been working on, getting a server up and running to handle my REST requests has been the top blocker. As I mentioned in an [earlier post](http://aghassi.github.io/ssl-using-express-4/), I had been playing around with SSL on a local server. Going into this though, I knew it would take some more effort that the `localhost` setup. After doing much research these were the tools I picked out going forward.

* DigitalOcean -> For hosting my content and acting as my server endpoint
* Let's Encrypt -> Allows me to get a free SSL Certificate for 3 months
* NodeJS + Express -> Easy to set up with rest endpoints and SSL
* Redis -> A NoSQL database that is extremely fast and lightweight
* Nginx -> Acts as a proxy for NodeJS to bind to the web address

-----

### Setting up a Droplet
Once you log into your DigitalOcean account, (remember, if you are a student, you can get $50 free through the GitHub Education Pack) you can deploy your first droplet. I used the NodeJS droplet because it comes with Node pre-installed. Honestly, you will have to do a lot of work anyway to get things working, so you might as well make your life a little easier. You can start with fresh Ubuntu too, that would work fine. Once you are logged in, you are ready to start setting up your server! That's it, droplet deployed. I highly recommend accessing it through `SSH` via a terminal app of your choosing instead of through their online command line.

-----

### Owning a Domain
If you are a student, you can acquire a free domain through NameCheap (even though it is limited to a `.me` domain). However, I already owned the domain I was using, but it was hosted over on Bluehost. It is a shared hosting website, so I had to change the DNS records to point at DigitalOcean. I found a great article on how to do that [here](https://www.digitalocean.com/community/tutorials/how-to-set-up-a-host-name-with-digitalocean). All I needed to do was add my domain for the A Record, and that was it. The domain became immediately associated, and was ready to use.

-----

### Setting up NodeJS
Node is pretty straight forward to use, and npm makes everything a breeze. These are the packages I used in my setup:

* [forever](https://www.npmjs.com/package/forever) - This allows you to run your node server in the background, and will restart it if the task stops working. Very handy. I installed this using `npm install forever -g` so it is available globally.
* [express](https://www.npmjs.com/package/express) - A lightweight web framework that provides an easy means of routing
* http - allows you to run an http server
* https - allows for a secure http server
* fs - fs (which I assume is short for file system) allows you to read to and from the file system. This will be used with our SSL cert.
* [body-parser](https://www.npmjs.com/package/body-parser) - allows you to parse the body of requests. Useful for sending variables to your REST endpoints
* [redis](https://www.npmjs.com/package/redis) - the client that will connect to your Redis DB and interact with it
* [xml2json](https://www.npmjs.com/package/xml2json) - allows me to convert XML I receive into JSON easily. *Note: you may have to `apt-get install g++` on your droplet for this to install properly*
* [serve-static](https://www.npmjs.com/package/serve-static) - a nice way to augment your server to be able to serve up a static site 

So we have all these dependencies, which sucks a bit, but worth it in the end. Starting with the top of my `app.js` was importing all these dependencies. I'm going to avoid covering the Redis stuff as well as the xml2json stuff because I don't feel that is necessary for this article. I may write one in the future on it, but for right now I think this is more important to share.

```javascript
// imports
var express = require('express');
var request = require('request');
var http = require('http');
var https = require('https');
var staticServe = require('serve-static');
var fs = require('fs');
var bodyParser = require('body-parser');
var xml2json = require('xml2json');
var redis = require('redis');
var redisClient = redis.createClient(6379);

// server
var app = express();

app.use(bodyParser.json()); // used for parsing application/json
app.use(bodyParser.urlencoded({ extended: true }));     // for parsing application/x-www-form-unlencoded

// Used for setting up your static site
app.use(staticServe('_domain', {'index': ['index.html', 'index.html']}));

// Read the link below about express behind a proxy
app.set('trust proxy', true);
app.set('trust proxy', 'loopback');
```

Good rule of thumb is to name all your variables the same or similar to the thing you are using. This way you can refer to it easily later. `var app = express()` starts a new express instance, which we can use for routing. We tell this instance that it has to handle `json` and `x-www-form-unlencoding`. We need that so we can send data to our server and get it back. We use `serve-static` to serve up a static website from a local folder named `_domain/`. For the index file, we tell it to use `index.html` or `index.htm`.
The last thing is prepping for when we setup Nginx. Read more about putting [Express Behind a Proxy](http://expressjs.com/en/guide/behind-proxies.html). This will explain why those variables are set.

----

### Pause to setup Nginx and Let's Encrypt
Alright, quick stop before going forward so we can prep for SSL. You will want to go get nginx. Since we are on an Ubuntu VM, we can `apt-get` it.

`sudo apt-get update`

`sudo apt-get install nginx`

Once we have done that, Nginx should start right away. To verify your site is running properly, you can open up [Postman](https://www.getpostman.com) and send a get request to your server's IP, or the address we setup earlier with DigitalOcean. You should get a 200 OK response and some basic HTML back that says "Welcome to nginx!" or something like that. Once we have that done, we can create our nginx `.conf` file that we can use to manage our node instances.

At this point, go to `cd ~` and clone [Let's Encrypt](https://letsencrypt.org). Once you have cloned it run the following in the `letsencrypt` folder:

`./letsencrypt-auto certonly`

This will walk you through the steps for registering your domain with a certificate authority. It may ask you to stop your nginx server because it needs to port temporarily. If this is the case, just run `service nginx stop`, run the above line again, then run `service nginx start`. Once you have completed this, it will tell you something like `Your certificate has been generated and is good until April 4th of 2016. You can find your certificate at /etc/letsencrypt/live/yourdomainname.com/`. Remember or write down that folder address, we will need it in a minute

The best way of thinking about this setup is that nginx gets all the hits, and then can manage multiple node instances in the background which each do their own thing. Sort of like a Master -> Worker model. Except each node instance is highly specialized for one/many things. Below you can see my server config file:

```nginx
upstream rest_node_js {
    server  127.0.0.1:8000;
}

server {
    listen 443 ssl;
    server_name findmybusnj.com;

    ssl on;
    gzip on;

    ssl_certificate /etc/letsencrypt/live/findmybusnj.com/cert.pem;
    ssl_certificate_key /etc/letsencrypt/live/findmybusnj.com/privkey.pem;

    ssl_stapling on;
    ssl_stapling_verify on;
    ssl_trusted_certificate /etc/letsencrypt/live/findmybusnj.com/fullchain.pem;

    ssl_session_timeout 5m;

    location / {
            proxy_pass http://rest_node_js;
            proxy_redirect off;
    }
}

server {
    listen 80;
    server_name findmybusnj.com;
    return 301 https://findmybusnj.com$request_uri;
}

server {
    listen 80;
    server_name www.findmybusnj.com;
    rewrite ^/(.*) https://findmybusnj.com/$1 permanent;
}

server {
    listen 443;
    server_name www.findmybusnj.com;
    rewrite ^/(.*) https://findmybusnj.com/$1 permanent;
}
```

Wow that seems like a lot! Let's break it down:

* **upstream** - This is telling the nginx server that there is a point that we want to proxy the data to upstream. `127.0.0.1` is the same as saying `localhost`, meaning this computer/VM we are working on. `:8000` tells it what port to look for locally for the service.
* **server with ssl** - We start off by telling nginx we want it to listen for a server on 443 (default port for HTTPS) and that it is ssl enabled. `server_name` is what address someone would type into the address bar to access this site. `ssl on` enables HTTPS on the server, and `gzip on` enables gzip compression on the server. Remember that SSL location I told you we needed? Now we need it! Specify `ssl_certificate` with the location of your `cert.pem`. Then provide the key for the certificate using `ssl_certificate_key`, which is your `privkey.pem`. We also need to enable stapling so that our client can verify our certificate is valid, as well as provide the trusted cert we generated. Lastly, we set the proxy for `location /`, meaning all requests that come to `/` get passed to node. This will also cover the rest endpoints.
* **the other servers** - These server definitions set the site to always redirect to the https version of the site. Even though we run the http version, we always want our users to be secure.

----

### Back to NodeJS
Alright we have our Nginx instance setup with our certificates, great! We want to make sure our node instance is running so we can actually use what we setup. Open up our `app.js` that we were working on earlier and we have to add the following lines:

```javascript
var ssl = {
    key: fs.readFileSync('/etc/letsencrypt/live/findmybusnj.com/privkey.pem'),
    cert: fs.readFileSync('/etc/letsencrypt/live/findmybusnj.com/fullchain.pem'),
    ca: fs.readFileSync('/etc/letsencrypt/live/findmybusnj.com/chain.pem')
}

http.createServer(app).listen(process.env.PORT || 8000);
https.createServer(ssl, app).listen(process.env.PORT || 8443);
```

Let's break this down and see what we are doing. Like before, in the Nginx config file, we have to point our instance to the SSL options we have. We do syncronous reads to the disk to get this data, which isn't huge because they are small files. We follow this by setting up our servers. We setup an `http` and `https` server. Both take the express settings we have setup, as well as the process port we are using (or a fallback port which we specify). For the `https` server we have to pass in the `ssl` options we provided earlier. 

For testing sake, you can add an endpoint like the following:
`app.get(/rest/test, function (req,res) { res.json("Hello World!"});`

That will allow you to send a request to you `yourdomain.com/rest/test` using Postman and see your JSON response "Hello World!". 

One you are done, save your file.

### Running your app, and final thoughts
Once your configs are all ready to go, you can run `npm install forever -g` if you haven't already so you can run `forever`. Once installed run `forever start app.js`. And that is it, you can see if your process is running by grepping for it using `ps aux | grep "node"`. `forever` will keep your process running and restart it if it crashes. 

Overall this took a lot of trial and error on my end, mostly because I wasn't expecting the use of Nginx. I was hoping NodeJS could handle some of the more intense stuff, but such is life. I learned a ton, and it put my Linux skills to the test (which is alwasy good). I'm always looking to improve, so if you find that there is something here you would have done differently, feel free to email me and let me know. Unfortunately, this stuff is never really documented anywhere in explicit steps, there is a lot to fill in for yourself, which I find dissapointing because I think the more documentation, the easier this type of thing would be. There are a lot of click bait titles, but not all of which actually give you any substantial to work with.