# Riak Dev Cluster for Ubuntu 12.x

Easily run a 5-node [Riak](http://wiki.basho.com/Riak.html) cluster on Ubuntu 12.x.

* The names of the nodes are riak[1-5]@127.0.0.1
* The HTTP port of riak1 is 11098: <http://127.0.0.1:11098>
* Riak Control (Admin UI) is available here: <https://admin:admin@127.0.0.1:11098/admin> 
* All nodes use the [eleveldb](http://wiki.basho.com/LevelDB.html) storage backend
  to support [secondary indexes](http://wiki.basho.com/Secondary-Indexes.html)
* Please see riak[1-5]/etc/app.config for the other ports and settings

## Getting started

Clone the repository:

    $ git clone git://github.com/nopolabs/riak-dev-cluster.git

Go to the riak-dev-cluster directory:

    $ cd riak-dev-cluster

There is a bootstrap command available that installs, starts and joins the riak cluster in one go:

    $ rake bootstrap

The install target will download and build a copy of Riak from source.

You should have Erlang installed first. Consult
http://docs.basho.com/riak/latest/tutorials/installation/Installing-on-Debian-and-Ubuntu/#Installing-Riak-From-Source
for details. 

## Control commands

Start all nodes:

    $ rake start

Join all nodes as a cluster (only needed once):

    $ rake join

Stop all nodes:

    $ rake stop

Clear all data and restart the cluster:

    $ rake clear

## Other commands

See all available commands:

    $ rake -T

## Note

Depending on your erlang cookie, you may have to use the commands with `sudo`.

## Author

* [Dan Revel](https://github.com/nopolabs)

This repo was cloned from an OS/x version at https://github.com/simonvc/riak-dev-cluster

I have editted the Rakefile to support Ubuntu 12.x.

See https://github.com/simonvc/riak-dev-cluster for the OS/x version.

