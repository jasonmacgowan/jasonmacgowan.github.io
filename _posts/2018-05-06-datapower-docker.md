---
layout: post
title:  "Getting Started with DataPower in Docker"
date:   2018-05-06 21:00:00 -0400
categories: datapower
---

### Create DataPower Container

Start by creating a new container that sets some environment variables that DataPower expects, mounts useful directories, and exposes the web management port.

| Environment Variable       | Description                  |
| -------------------------- | ---------------------------- |
| `DATAPOWER_ACCEPT_LICENSE` | Accept required license      |
| `DATAPOWER_INTERACTIVE`    | Allows cli interaction       |
| `DATAPOWER_WORKER_THREADS` | Configure CPU resource limit |

{% highlight bash %}
docker run -it \
 -e DATAPOWER_ACCEPT_LICENSE=true \
 -e DATAPOWER_INTERACTIVE=true \
 -e DATAPOWER_WORKER_THREADS=2 \
 -v $PWD/config:/drouter/config \
 -v $PWD/local:/drouter/local \
 -p 9090:9090 \
 --name datapower1 \
 ibmcom/datapower
{% endhighlight %}

### Login to CLI Interface

When the container is launched, we're automatically attached to the container. We set the `DATAPOWER_INTERACTIVE` flag to true, so we're presented with a login prompt.

Login with username `admin` and password `admin`.

{% highlight java %}
login: admin
Password: ****

Welcome to IBM DataPower Gateway console configuration.
Copyright IBM Corporation 1999,2018-2018

Version: IDG.7.7.0.2 build 298364 on Apr 17, 2018 3:33:37 PM
Serial number: 0000001
idg#
{% endhighlight %}

### Enable Web Management

This CLI is fine, but DataPower has a powerful web-based GUI that we can use instead. To enable it, enter config mode.

{% highlight text %}
idg# config
Global configuration mode
{% endhighlight %}

Next, let's actually enable the service, binding to all IPs with `0.0.0.0` and to port `9090`

{% highlight text %}
idg(config)# web-mgmt 0.0.0.0 9090
Web management: successfully started
{% endhighlight %}

Finally, write out changes to disk so it will persist on restarts

{% highlight text %}
idg(config)# write mem
Overwrite previously saved configuration? Yes/No [y/n]: y
Configuration saved successfully.
{% endhighlight %}

Now we can visit it in our browser at `https://localhost:9090`

#### Resources

1.  [Docker RUN Reference](https://docs.docker.com/engine/reference/run)
1.  [IBM DataPower Docker Public Repository](https://hub.docker.com/r/ibmcom/datapower)
