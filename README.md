# Riak Dev Cluster for OS X

Easily run a 5-node [Riak](http://wiki.basho.com/Riak.html) cluster on OS X.

* The names of the nodes are riak[1-5]@127.0.0.1
* The HTTP port of riak1 is 11098: <http://127.0.0.1:11098>
* Riak Control (Admin UI) is available here: <https://admin:admin@127.0.0.1:11098/admin> 
* All nodes use the [LevelDB](http://docs.basho.com/riak/latest/ops/advanced/backends/leveldb/) storage backend
* [Riak Search](http://docs.basho.com/riak/latest/dev/using/search/) is disabled (default)
* Please see riak[1-5]/etc/riak.conf for the other ports and settings

## Getting started

Ensure that [Open File Limits](http://docs.basho.com/riak/latest/ops/tuning/open-files-limit/#Mac-OS-X) are set

Clone the repository:

    $ git clone git://github.com/basho/riak-dev-cluster.git

Go to the riak-dev-cluster directory:

    $ cd riak-dev-cluster

There is a bootstrap command available that installs, starts and joins the riak cluster in one go:

    $ rake bootstrap

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

## Authors

* [Erick Dennis](https://github.com/edennis)
* [Sebastian Röbke](https://github.com/boosty)

Updates by:
* [Simon Vans-Colina](https://github.com/simonvc)
* [Seth Thomas](https://github.com/cheeseplus)
* [Joel Jacobson](https://github.com/joeljacobson)
* [Tyler Hannan](https://github.com/tylerhannan)
