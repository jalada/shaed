# Getting Started

## Requirements

 - Git
 - Ruby 1.9 (tested on 1.9.2)
 - Rubygems
 - [Bundler](http://gembundler.com/)
 - [Redis](http://redis.io/) (tested on 2.4.9)
 - [MongoDB](http://www.mongodb.org/) (tested on 2.0.4)
 - [Node.js](http://nodejs.org/) 0.6 or greater (tested on 0.6.13)
 - [Node Package Manager](http://npmjs.org/) 1.1 or greater (tested on 1.1.10)

If you are planning on deploying to Heroku without performing any development
yourself, you do not need these things set up locally.

## Getting the code

All the code is available on [Github][], you can download a copy using git:

    $ git clone git://github.com/newsinternational/shaed
    Cloning into 'shaed'...
    <snip>
    $ cd shaed/
    $

This repository contains two [git submodules][] called `shaed-admin` and
`shaed-router`. These are the web-based admin panel and router respectively. To
retrieve these submodules run these commands from within the `shaed` directory.

    $ git submodule init
    Submodule 'shaed-admin' (git@github.com:newsinternational/shaed-admin.git) registered for path 'shaed-admin'
    Submodule 'shaed-router' (git@github.com:newsinternational/shaed-router.git) registered for path 'shaed-router'
    $ git submodule update
    Cloning into 'shaed-admin'...
    <snip>
    Submodule path 'shaed-admin': checked out 'f4f9f6fdc29eb62fbe51a613c935d7735c11301b'
    Cloning into 'shaed-router'...
    <snip>
    Submodule path 'shaed-router': checked out '51f996196e7302f64d69e9031ec35666175a97e1'
    $

Any time you want to pull down new changes, run `git pull` and then `git
submodule update` from within the `shaed` directory.

There are **two separate** applications that make up Shaed, and they can be
run independently.

## Running manually

You can run Shaed manually for local development or a 'ghetto' deployment. For 
production deployment we recommend deploying to Heroku or using your own 
deployment method.

Ensure you have all the requirements listed above. Pay careful attention to
the versions of NodeJS and MongoDB that you have installed, ensuring they are
the same or close to the versions listed above. In addition you will also
need [foreman](https://github.com/ddollar/foreman) which you can install by
running `gem install foreman`.

### Shaed Admin

From the `shaed/` folder:

    $ cd shaed-admin/
    $ bundle
    Fetching gem metadata from http://rubygems.org/........
    <snip>
    Your bundle is complete! Use `bundle show [gemname]` to see where a bundled gem is installed.
    $

Check `.env` to make sure you won't have a port clash (foreman will start a
copy of Mongo specifically for Shaed to avoid clashing with any system-running
version). At the same time, set ADMIN\_EMAILS to your email address (if you
want to have multiple administrators, separate the emails with commas). You
must use a Google account (domain accounts are acceptable) as the app uses
Google OpenID to log you in.

    $ cat .env
    # Mongo configuration; used by both Mongo (if you're starting via Foreman) and
    # the admin app.
    MONGO_HOST=localhost
    MONGO_PORT=27019
    MONGO_DATABASE=shaed_development
    ADMIN_EMAILS=jalada@newsint.co.uk

The first time you want to run the app, Mongo may need to preallocate a journal
file, to give it chance to do this run `foreman start mongo` and wait until it is
ready, then use ctrl+c to kill it.

Once everything is set up, run `foreman start`:

    $ foreman start
    04:05:52 web.1     | started with pid 23526
    04:05:52 mongo.1   | started with pid 23529
    04:05:52 mongo.1   | Thu Mar 29 04:05:52 [initandlisten] MongoDB starting : pid=23529 port=27019 dbpath=db/data 64-bit host=ubuntu
    04:05:52 mongo.1   | Thu Mar 29 04:05:52 [initandlisten] db version v2.0.4, pdfile version 4.5
    04:05:52 mongo.1   | Thu Mar 29 04:05:52 [initandlisten] git version: 329f3c47fe8136c03392c8f0e548506cb21f8ebf
    04:05:52 mongo.1   | Thu Mar 29 04:05:52 [initandlisten] build info: Linux ip-10-110-9-236 2.6.21.7-2.ec2.v1.2.fc8xen #1 SMP Fri Nov 20 17:48:28 EST 2009 x86_64 BOOST_LIB_VERSION=1_41
    04:05:52 mongo.1   | Thu Mar 29 04:05:52 [initandlisten] options: { dbpath: "db/data", port: 27019 }
    04:05:52 mongo.1   | Thu Mar 29 04:05:52 [initandlisten] journal dir=db/data/journal
    04:05:52 mongo.1   | Thu Mar 29 04:05:52 [initandlisten] recover : no journal files present, no recovery needed
    04:05:52 mongo.1   | Thu Mar 29 04:05:52 [initandlisten] waiting for connections on port 27019
    04:05:52 mongo.1   | Thu Mar 29 04:05:52 [websvr] admin web console waiting for connections on port 28019
    04:05:56 web.1     | MONGODB [DEBUG] Logging level is currently :debug which could negatively impact client-side performance. You should set your logging level no lower than :info in production.
    04:05:56 mongo.1   | Thu Mar 29 04:05:56 [initandlisten] connection accepted from 127.0.0.1:41007 #1
    04:05:56 web.1     | MONGODB (9ms) admin['$cmd'].find({:ismaster=>1}).limit(-1)
    04:05:56 mongo.1   | Thu Mar 29 04:05:56 [conn1] end connection 127.0.0.1:41007
    04:05:57 web.1     | => Padrino/0.10.6 has taken the stage development at http://0.0.0.0:5000
    04:05:57 web.1     | >> Thin web server (v1.3.1 codename Triple Espresso)
    04:05:57 web.1     | >> Maximum connections set to 1024
    04:05:57 web.1     | >> Listening on 0.0.0.0:5000, CTRL+C to stop
    ...

You should now be able to access the Shaed Administration panel by visiting
[http://localhost:5000](http://localhost:5000). However you will be unable
to log in because no administrators will be set. To add some administrators,
open up another shell (you might want to use screen) in the `shaed/shaed-admin`
folder (leave foreman running) and run `foreman run padrino rake seed` (`foreman 
run` is necessary to ensure the app has access to environment variables).

    $ foreman run padrino rake seed
    => Executing Rake seed ...
    MONGODB [DEBUG] Logging level is currently :debug which could negatively impact client-side performance. You should set your logging level no lower than :info in production.
    MONGODB (0ms) admin['$cmd'].find({:ismaster=>1}).limit(-1)
    Creating admin users
    MONGODB (3ms) shaed_development['system.namespaces'].find({})
    MONGODB (0ms) shaed_development['accounts'].find({:email=>"jalada@newsint.co.uk"}).limit(-1).sort([[:_id, :asc]])
    MONGODB (1ms) shaed_development['$cmd'].find({"count"=>"accounts", "query"=>{:email=>/^jalada@newsint\.co\.uk$/i}, "fields"=>nil}).limit(-1)
    MONGODB (0ms) shaed_development['accounts'].insert([{"_id"=>BSON::ObjectId('4f7443a41d41c85d2f000001'), "email"=>"jalada@newsint.co.uk", "updated_at"=>2012-03-29 11:12:36 UTC, "created_at"=>2012-03-29 11:12:36 UTC}])
    $

Now you can login! Congratulations, you're half way there.

### Shaed Router

Set up Shaed admin first, and keep it running - the router will share the
MongoDB server.

From the `shaed/` folder:

    $ cd shaed-router/
    $ npm install
    npm http GET https://registry.npmjs.org/underscore
    npm http GET https://registry.npmjs.org/mongojs
    npm http GET https://registry.npmjs.org/underscore.string
    <snip>
    bufferjs@1.1.0 ./node_modules/bufferjs 
    nock@0.11.5 ./node_modules/nock 
    redis@0.7.1 ./node_modules/redis 
    underscore@1.3.1 ./node_modules/underscore 
    underscore.string@2.1.0 ./node_modules/underscore.string 
    mongojs@0.3.2 ./node_modules/mongojs 
    ├── common@0.2.1
    └── mongodb@0.9.9-7

Then start the router and Redis with foreman:

    $ foreman start
    07:12:37 redis.1   | started with pid 24241
    07:12:37 web.1     | started with pid 24244
    07:12:37 redis.1   | [24241] 29 Mar 07:12:37 # Warning: no config file specified, using the default config. In order to specify a config file use 'redis-server /path/to/redis.conf'
    07:12:37 redis.1   | [24241] 29 Mar 07:12:37 * Server started, Redis version 2.4.9
    07:12:37 redis.1   | [24241] 29 Mar 07:12:37 # WARNING overcommit_memory is set to 0! Background save may fail under low memory condition. To fix this issue add 'vm.overcommit_memory = 1' to /etc/sysctl.conf and then reboot or run the command 'sysctl vm.overcommit_memory=1' for this to take effect.
    07:12:37 redis.1   | [24241] 29 Mar 07:12:37 * The server is now ready to accept connections on port 6379
    07:12:37 web.1     | Router listening on 5100
    07:12:37 redis.1   | [24241] 29 Mar 07:12:37 - Accepted 127.0.0.1:34256
    ...

If you want to run tests, keep foreman running (for Redis, which currently
isn't stubbed in the tests) and then install [nodeunit][].

    $ sudo npm install -g nodeunit
    <snip>
    /usr/local/bin/nodeunit -> /usr/local/lib/node_modules/nodeunit/bin/nodeunit
    nodeunit@0.7.4 /usr/local/lib/node_modules/nodeunit 
    └── tap@0.2.4
    $ rake test
    nodeunit test/*_test.js

    app_test.js
    ✔ App - writeCachedResult - writes the status code and header
    <snip>
    OK: 41 assertions (4975ms)

For some reason the test framework hangs. Hit Ctrl+c when it's done.

You're ready to go!

## Deploying to Heroku

If you have never deployed to Heroku, output from some commands may differ
slightly from what is here. Also, be sure to install the [Heroku
toolbelt](https://toolbelt.heroku.com/) for your platform (otherwise `heroku` 
commands won't work).

### Shaed Admin

From the `shaed-admin` folder in `shaed/`, create an app on Heroku (replace
`my-shaed-admin` with something unique for your deployment). If you've never
used Heroku before you will be prompted for your credentials.

    $ cd shaed-admin/
    $ heroku apps:create my-shaed-admin --stack cedar
    Enter your Heroku credentials.
    Email: your@email.com
    Password (typing will be hidden):
    Uploading SSH public key /home/jalada/.ssh/id_rsa.pub
    Creating my-shaed-admin... done, stack is cedar
    http://my-shaed-admin.herokuapp.com/ | git@heroku.com:my-shaed-admin.git
    Git remote heroku added
    $ 

Add a MongoHQ addon. This is the free tier so it won't cost you any money. Note
down the `MONGOHQ_URL` environment variable because you will need to add it to
the router app later.

    $ heroku addons:add mongohq:free
    ----> Adding mongohq:free to my-shaed-admin... done, v2 (free)
    $ heroku config
    MONGOHQ_URL => mongodb://heroku:aecaf9edf9eac9@staff.mongohq.com:10066/app1231512
    $ 

Now push the app to Heroku to deploy it:

    $ git push heroku master                       
    Counting objects: 277, done.                                                    
    Compressing objects: 100% (126/126), done.                                      
    Writing objects: 100% (277/277), 48.75 KiB, done.                               
    Total 277 (delta 118), reused 277 (delta 118)                                   
                                                                                    
    -----> Heroku receiving push                                                    
    -----> Ruby/Rack app detected                                                   
    -----> Installing dependencies using Bundler version 1.1.2                      
           Running: bundle install --without development:test --path vendor/bundle --binstubs bin/ --deployment
    <snip>
    -----> Compiled slug size is 10.3MB
    -----> Launching... done, v4
           http://my-shaed-admin.herokuapp.com deployed to Heroku

    To git@heroku.com:my-shaed-admin.git
     * [new branch]      master -> master
    $  

You should now be able to visit
[http://my-shaed-admin.herokuapp.com](http://my-shaed-admin.herokuapp.com) but
you won't be able to log in because you need to seed the database with some
initial admin users.

To do this, add a configuration variable to the Heroku app called
`ADMIN_EMAILS` with a comma-separated list of email addresses and then run the
'seed' rake task within Heroku:

    $ heroku config:add ADMIN_EMAILS=jalada@newsint.co.uk,peter.macrobert@newsint.co.uk
    Adding config vars and restarting app... done, v5
      ADMIN_EMAILS => jalada@newsint.c...rt@newsint.co.uk
    $ heroku run padrino rake seed
    => Executing Rake seed ...
    <snip>
    $  

You're done! Now you can log in to the admin panel.

### Shaed Router

From the `shaed-router` folder in `shaed/` create another app on Heroku (again,
pick something unique for you).

    $ cd shaed-router/
    $ heroku apps:create my-shaed-router --stack cedar
    Creating my-shaed-router... done, stack is cedar
    http://my-shaed-router.herokuapp.com/ | git@heroku.com:my-shaed-router.git
    Git remote heroku added
    $ 

Add RedisToGo (again, this is the free tier. If you need more cache for
requests use a pay tier).

    $ heroku addons:add redistogo:nano
    ----> Adding redistogo:nano to my-shaed-router... done, v2 (free)
    $ 

Configure the router to use the same MongoDB instance as the admin app using
the `MONGOHQ_URL` variable you noted down when setting it up:

    $ heroku config:add MONGOHQ_URL=mongodb://heroku:aecaf9edf9eac9@staff.mongohq.com:10066/app1231512
    Adding config vars and restarting app... done, v3
      MONGOHQ_URL => mongodb://heroku...10066/app1231512
    $

Now push the code to Heroku.

    $ git push heroku master
    Counting objects: 124, done.
    Compressing objects: 100% (43/43), done.
    Writing objects: 100% (124/124), 24.12 KiB, done.
    Total 124 (delta 69), reused 124 (delta 69)

    -----> Heroku receiving push
    -----> Node.js app detected
    -----> Resolving engine versions
           Using Node.js version: 0.6.14
           Using npm version: 1.1.9
    <snip>
    -----> Compiled slug size is 4.3MB
    -----> Launching... done, v5
           http://my-shaed-router.herokuapp.com deployed to Heroku

    To git@heroku.com:my-shaed-router.git
     * [new branch]      master -> master
    $

You're done!

[nodeunit]: https://github.com/caolan/nodeunit
[git submodules]: http://book.git-scm.com/5_submodules.html
[github]: https://github.com/newsinternational/shaed
[redis to go]: https://addons.heroku.com/redistogo
