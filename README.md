# fhem-connect.js

## About

In order to let cloud services (e.g. [IFTTT](http://IFTTT.com)) communicate with [FHEM](http://fhem.org), you have to open a port in you home router. 
There are configurations where this is not possible or not wanted.

In this case, you can use fhem-connect.js with a fork from [fhem.js](https://github.com/MarcProe/fhem.js/tree/websocket-client).

It works like this:

![Overview image][overview]

[overview]: overview.png "Overview image"

## Step-By-Step
**_This manual is for a linux environment._**
### Register on Heroku
Heroku is a cloud platform as a service (PaaS) supporting several programming languages that is used as a web application deployment model.

Heroku allows you to execute a server for 550 hours (22 days) per month (1000 hours (42 days) if you give them your credit card info) for free. 

* Click "sign up for free" on https://www.heroku.com/
* Enter the information asked for and choose "Node.js" as primary language
* Click "Create free Account"
* Wait for the confirmation email and click on the confirmation link
* choose a password and log in

### Register on Github
You need a github account to get fhem.connect.js to run on heroku. ([There are other ways to do this](https://devcenter.heroku.com/articles/heroku-cli).)
* Go to [github.com](https://www.github.com) and register for a free account
* Verify you emailaddress
* Go to `https://github.com/MarcProe/fhem-connect.js` and click "Fork" (upper right)

### Starting fhem-connect.js
* Click on "create new app"
* Choose a name and a location near you
* Click "create app"
* Go to "Settings" and enter the following config variable. Change the string "s3cr3t" to a long, unique string to let your local fhem.js authenticate against.
  * KEY: `auth`
  * VALUE: `{ "keys" : [ "s3cr3t" ] }`
* Click on "Deploy", then "Github", then "Connect to Github"
* Authorize heroku on github
* Click on "search", then "connect" next to the fhem-connect.js project
* On the bottom of the page, choose a release branch (or master), and then "Deploy Branch"
* On the top of the page, click "more", then "View logs" to see whats happening

### Start fhem.js
* Install node.js on your fhem server or a dedicated server in the same network
* Install git
* Checkout my fhem.js fork (https://github.com/MarcProe/fhem.js/tree/websocket-client)
  * `git clone https://github.com/MarcProe/fhem.js.git` 
  * `cd fhem.js`
  * `git checkout websocket-client`
* Copy the default config from `etc/fhem.js/params.js.dist` to the root directory of the application (where server.js is)
* Rename it to `params.js`
* Open it and change the values
  * Enter your fhem ip and telnet port
  * For the websocket settings (at the end of the file)
    * Activate the websocket client (`active : true`)
    * Enter your websocket url `url : "wss://your-app-name.herokuapp.com"`)
    * Enter your auth key (the key you put in the config variable on heroku (`auth:  "s3cr3t"`))
    * The key you want external services to use to access your service (`key : "something"`)
    * Some regular expressions to restrict access to your fhem (`cregex`, `dregex` and `eregex`)
    * The maximum requests you'll allow per minute (`reqpermin : 180`)
      * this is enforced per request, so if you put 180 in it, it will allow for one request per 333 milliseconds
* Start the server: `node server.js`
    
### Configure a service
* You can trigger your fhem-connect.js with the following url:
  * `https://your-app-name.herokueapp.com/api/v1/s3cr3t/set%20device%20on`
    * `%20` is the code for `space`
* Example: IFTTT

![IFTTT Recipe][ifttt]

[overview]: overview.png "Overview image"
[ifttt]: ifttt-recipe.png "IFTTT Recipe"