
Sacred Quickstart
=================

[Sacred][] is "a tool to help you configure, organize, log and reproduce
experiments". It's a paranoid answer to the question of which experiment
you ran on any given day, what results you got and which version of your
code were you using. If you are using this tool in earnest this information
will always be _somewhere_ in your database.

It's a relatively active project, and [their GitHub repository][sacred] has many
badges showing bug tracking, versioning etc. So the developers care about
the project. And, it comes from [IDSIA][], the lab including Jurgen
Schmidhuber in Switzerland.

I found the [quickstart supplied with Sacred][qkstart] was not enough to
actually start using it, so I've written this guide. Hopefully, it'll save
you the time I've wasted configuring this.

How Does it Work?
-----------------

Sacred only requires that you wrap your experiment functions with
[decorators][] and somewhere to store the results (an __Observer__). Then
it will store everything about the experiment, including:

* Configuration variables, if you get Sacred to handle them
* Start time and stop time
* System information (OS, hardware, Python version, ...)
* Captured stdout
* The _entire script and dependencies_ used to run the experiment
* Any errors
* Random seeds

This means you don't have to write much code to make this work. If your
experiment is a script already you probably only need to add 3 lines. The
random seed logging particular is useful because it will set this random
seed to something and store it by default, so your experiments are always
repeatable.

On top of this functionality, it can do various other jobs you may need
when running experiments. The best documented extra feature is the ability
to deal with configuration. Using the `config` decorator it's possible to
pass global configuration variables around in your code.

Installation
------------

Can be installed from pypi using pip:

```
pip install sacred
```

Note that it is under active development and the _latest documentation is
incompatible_ with the pypi stable version. The documentation for the
stable version is [here][qkstart].

The latest version can be installed from GitHub directly:

```
pip install git+git://github.com/IDSIA/sacred.git
```

I wrote this guide working with the __stable version__.

Hello World?
------------

Now, you can already use Sacred:

```Python
from sacred import Experiment

ex = Experiment('hello_world')

@ex.automain
def my_main():
    print('Hello world!')
```

Using the `Experiment` class here, we've made an experiment called `ex` and
then wrapped our function with the decorator `ex.automain`. The
`ex.automain` decorator will then run the `my_main` function and log the
results when we execute the above as a script:

```
> python hello_world.py
INFO - hello_world - Running command 'my_main'
INFO - hello_world - Started
Hello world!
INFO - hello_world - Completed after 0:00:00
```

You could be forgiven for thinking that you just logged that experiment.
You didn't, _nothing got logged_, because there were no observers. So, as
far as a Hello World example for an experiment logging utility goes, we
don't have one yet. First we need an __Observer__.

Observer Setup
==============

All of the functionality of Sacred as a frontend relies on having a backend
to store the results of the experiments that we are trying to log. In the
stable version this means setting up a __MongoDB Observer__, but in the
latest version there are [other options][otherobs].

Adding a MongoObserver
----------------------

The crucial point that is skipped (rightly) in the [official
documentation][addobs] is how to set up a MongoDB instance for use with
Sacred. Of course, this is the same as setting up a MongoDB instance for
any use, but you may not know how to do that. Specifically, I'm writing
this part of the guide to cover _how to set up a MongoDB instance in
Edinburgh Informatics_.

If you're going to the trouble of setting up this database, you probably
want it to be accessible everywhere on the university intranet. There are
three options to make this possible:

* Custom configuration on a self-managed machine, if you have one.
* Run as a DICE user on an arbitrary server in scratch space. As long as
the MongoDB server only touches scratch space, it won't matter that it's
Kerberos ticket expires, and it will run until the server is restarted.
* Use a cloud service, such as [mLab][]. The free tier is probably large
enough for this, and this has the additional advantage of being available
everywhere without having to use the VPN.

The first two of these can be achieved, [once MongoDB is
installed][mongoinstall], by starting the MongoDB service on whichever
architecture you're on. Or, on DICE:

```
mongod --fork --logpath /disk/scratch/<somewhere> --dbpath /disk/scratch/<somewhere else>
```

If you've done this, then skip the next section on using docker.

### Docker

If you choose to run on a self-managed machine, you are free to use docker
to set up your MongoDB server. Docker supplies a [Mongo build][dockmongo]
specifically for this, and it'll save all sorts of installation and update
issues. This is what I did, using a [data volume container][dockdata] for
the database files themselves.

To create the data volume container:

```
docker create -v /data/db --name mongostore mongo:latest /bin/true
```

After doing this, when starting the MongoDB container, the flag
`--volumes-from mongostore` must be used. To start the MongoDB instance for
the first time, all we need to do is:

```
docker run --volumes-from mongostore --name my-mongodb -d mongo
```

But, this _is probably useless_, because it's not running on any visible
ports (inside the container it's running on 27017, but we can't access that
yet). We could expose the port with the flag `-p 27017:27017`, but that
would be accesible to _everyone on our local network_ and this MongoDB has
__no authorisation__. This is a massive security problem, even with the
container.

### Setting up authorisation

If you're using [mLab][] then you don't need to worry about this.
Authorisation is already ready to go, you only need to add users with
passwords through their web interface.

Otherwise, you're going to need to set up a user to administrate all other
users on this MongoDB server, and give him a password. Then you'll be able
to restart the server with authorisation enabled. To add a user, you need
to open a [mongo shell][mongoshell]. If you're using docker, you can do
this:

```
docker exec -it my-mongodb mongo admin
```

To make an administrator user:

```
> db.createUser({ user: 'jsmith', pwd: 'some-initial-password', roles: [ { role: "userAdminAnyDatabase", db: "admin" } ] });
Successfully added user: {
    "user" : "jsmith",
    "roles" : [
        {
            "role" : "userAdminAnyDatabase",
            "db" : "admin"
        }
    ]
}
```

Once you've done this, you can restart the database with the `--auth` flag.
But, you might as well make a user to write to your database of experiments
while you're in the shell. If your experiment database is going to be
called `experiments` then this is done as follows:

```
> use experiments
> db.createUser({ user: 'another-user', pwd: 'some-other-password', roles: [ { role: "readWrite", db: "experiments" } ] });
```

This is the user you're going to be using to write to this database from
other locations. It is important to note that your previous admin user
(`jsmith` above) _cannot read or write any database_; they are literally
only user admins.

If you used docker, then restarting the database requires starting a new
container.

```
docker stop my-mongodb
docker commit my-mongodb me/mongo
docker run -p 27017:27017 --volumes-from mongostore --name my-mongodb-auth -d me/mongo --auth
```

Now, this container __is exposed on `0.0.0.0:27017`__, but only users with
the passwords you set above will be able to access it. If you are running
this MongoDB on a DICE machine, you will have to kill the `mongod` process
and then restart it listening on all interfaces (__and authorisation__).

```
mongod --fork --auth --bind_ip 0.0.0.0 --logpath /disk/scratch/<somewhere> --dbpath /disk/scratch/<somewhere else>
```

### Storing your MongoDB URI

To add this MongoDB as an observer we need to supply a valid [MongoDB
URI][uri], which includes the login username and password. It'll look like
this:

```
mongodb://<username>:<password>@<url>/<database-name>?authMechanism=SCRAM-SHA-1"
```

The problem with this is you probably don't want to commit that information
in your experiment script.

The correct way to deal with this [can be complicated][userpass]. If you're
on DICE, the recommended solution of `ssh-agent` doesn't work. You may want
to encrypt the password, but you'll end up having to type a password every
time you decrpyt it. All I ended up doing was putting the entire URI
in my python path as `secrets.py` and base64 encoding it. To get
the password can then just do:

```
from secrets import mongo_uri
```

You can decide how paranoid you want to be here, but definitely __don't
commit your URI__.

Hello World!
------------

Now that you've done all that, we can modify the original script so we're
actually going to log the Hello World experiment:

```Python
from sacred import Experiment
from sacred.observers import MongoObserver
from secrets import mongo_uri

ex = Experiment('hello_world')

ex.observers.append(
    MongoObserver.create(url=mongo_uri, db_name='experiments')
                   )

@ex.automain
def my_main():
    print('Hello world!')
```

We will get the same result as we got the first time we ran the experiment,
but _this time it was logged in the database_.

But, now that we've shoved all these results into this MongoDB, how do we
actually look at them? 

Looking at your experiments
---------------------------

All of the experiments are stored in MongoDB, so we can access them in a
few different ways. The simplest option is we just open the database with
`pymongo`. I've done this to my database in a [Jupyter notebook][pymongo]
to show what this is like. Depending on how you've chosen to store
information in the database, you could write code on top of pymongo to
print whatever you're interested in.

Another option would be to use a GUI called [SacredBrowser][]. To use it
you'll have to grant your user additional permissions in the `admin`
database. Specifically, you'll need to do the following:

```
> use admin
> db.createUser({ user: 'another-user', pwd: 'some-other-password',
roles : [
		{
			role: "readWriteAnyDatabase",
			db: "admin"
		},
		{
			role: "clusterMonitor",
			db: "admin"
		}
	   ]});
```

Logging Stuff
-------------

So, you're training a neural network and you want to look at the learning
curve trace of some hyperparameter setting run you've run. In the
documentation for Sacred it says to [use the InfoDict][infodict], but gives
no example how to do this.

One way is the following example, by capturing a function and passing the
special `_run` argument, and accessing the InfoDict through that. Doing
this, it would be easy to make an arbitrary logging function for something
like neural network losses and append data to a list whenever it is called.

```
from sacred import Experiment
from sacred.observers import MongoObserver
from secrets import mongo_uri

# Create an Experiment instance and provide it with a name
ex = Experiment('hello_world')

ex.observers.append(MongoObserver.create(
    url=mongo_uri,
    db_name='experiments'))

# this must be a captured function to pass the _run arg
@ex.capture
def save_learning_curve(_run):
    _run.info['learning curve'] = range(10)

# This function should be executed so we are decorating it with @ex.automain
@ex.automain
def main():
    print('Hello world!')
    save_learning_curve()
```

In the future, I may write code to append such data and then use
[Holoviews][] to plot it, after pulling it out of a Sacred MongoDB record.

[sacred]: https://github.com/IDSIA/sacred
[idsia]: http://www.idsia.ch/idsia_en/institute.html
[decorators]: https://wiki.python.org/moin/PythonDecorators
[qkstart]: http://sacred.readthedocs.io/en/stable/quickstart.html
[otherobs]: http://sacred.readthedocs.io/en/latest/observers.html
[addobs]: http://sacred.readthedocs.io/en/stable/observers.html
[mlab]: https://mlab.com/
[dockmongo]: https://hub.docker.com/_/mongo/
[dockdata]: https://docs.docker.com/engine/tutorials/dockervolumes/#/creating-and-mounting-a-data-volume-container
[mongoinstall]: https://docs.mongodb.com/manual/installation/
[mongoshell]: https://docs.mongodb.com/manual/mongo/
[uri]: https://docs.mongodb.com/manual/reference/connection-string/
[userpass]: http://stackoverflow.com/questions/7014953/i-need-to-securely-store-a-username-and-password-in-python-what-are-my-options
[sacredbrowser]: https://github.com/michaelwand/SacredBrowser
[pymongo]: https://gist.github.com/gngdb/f1bcff4c4b546ff17ee91884061f75ad
[infodict]: http://sacred.readthedocs.io/en/latest/observers.html#info-dict
[holoviews]: http://holoviews.org/
