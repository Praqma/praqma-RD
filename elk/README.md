# ELK aka Elasticsearch + LogStash + Kibana

While the ELK stack (elasticsearch, logstash and kiban) is very popular, we're missing a quick and easy way to spin up an ELK stack for demonstration and for small POCs.

As it wasn't obvious which one to chose this write-up can maybe be of good use if you're also missing an easy to run ELK stack, that can grow to a production like setup if it needs to.


## Containers it is

I'm looking for a Docker container setup, as this makes it very easy to manage, spin-up and move.

There is no official images on Dokcer Hub if I search ELK, though each of the tools have their individual official images.

[sebp/elk](https://hub.docker.com/r/sebp/elk/) is the one pulled the most, and the one with most stars.

It also have nice docs under http://elk-docker.readthedocs.io and Github project is https://github.com/spujadas/elk-docker.

But he also made a decision about using Filebeat as default way to accept data, so that might not exactly fit my purpose.

> 5044 (Logstash Beats interface, receives logs from Beats such as Filebeat – see the Forwarding logs with Filebeat section).

(from http://elk-docker.readthedocs.io/#usage)




## docker-elk from deviantony's Github

https://github.com/deviantony/docker-elk

The docker-compose file builds the images, thus it need to run the build on the same host as we clone the repo, so can't use my Digital Ocean droplet.

Using my own machine:


    $ git clone https://github.com/deviantony/docker-elk
    cd docker-elk

Setting requirement for max map count:

    sudo sysctl vm.max_map_count
    sudo sysctl -w vm.max_map_count=262144
    sudo sysctl vm.max_map_count


Spinning up...

    $ docker-compose up

To persist data, add the following to the docker-compose file from the above repo under the elasticsearch service:

    volumes:
      - ./elasticsearch/data:/usr/share/elasticsearch/data
