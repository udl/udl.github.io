---
layout: post
title:  "Using docker for testing"
date:   2014-07-16 11:39:09
categories: devops
---

The other day, I wrote a tiny application using [neo4j](http://www.neo4j.org/).
While it's a nice piece of software, it supports just one running database.
Running tests required a clean database, so I always had to stop the neo4j
instance, change the database path to test.db, run the tests
and do the same hamsterdance again to have a development database in
place.

Very tedious!

So why not use [docker](http://www.docker.com/) to spawn a fresh
database? It's the perfect solution for a throwaway test db.
Here's how I did it:

## Create a shell script that starts the database and runs tests

```
  #!/bin/bash
  echo "Starting docker containers"
  docker run -p 7474:7474 --name 'test\_neo4j' -d tpires/neo4j
  echo "Running tests"
  sleep 10
  if [ `uname` == "Darwin" ]; then
    # we have boot2docker on OSX
    neo_ip=`boot2docker ip | grep -Eo '[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}'`
  else
    neo_ip=`docker inspect test\_neo4j | grep IPAddress | cut -d '"' -f 4`
  fi

  # running tests
  NEO\_PORT\_7474\_TCP\_ADDR=$neo\_ip bundle exec rspec

  # catching exit code
  rc=$?
  echo "Stopping docker containers"
  docker stop test\_neo4j

  echo "Removing obsolete containers"
  docker rm test\_neo4j
  # exiting with exit code of tests
  exit $rc
```

This shell script

- starts a neo4j instance
- runs ruby [rspec](http://rspec.info/) tests
- catches the exit code of the tests
- stops and removes the neo4j container
- exits with the test exit code

Only thing you have to do application wise, is to use the environment
parameter **NEO\_PORT\_7474\_TCP\_ADDR** for connecting to the neo4j.

But, as we are all big fans of [12 factor apps](http://12factor.net/),
we do that anyways, right?

So far this approach has worked out really well. No more annoying "how
do I have to setup the test db again?" question.
Just install docker and you're ready to roll!
