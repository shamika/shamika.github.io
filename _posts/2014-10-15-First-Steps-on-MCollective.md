---
layout: post
title: First Steps on MCollective
tags: mcollective, clustering

---

The Marionette Collective (<a target="_blank" href="https://docs.puppetlabs.com/mcollective/">MCollective</a>) is a compressive toolkit developed by Puppet Labs to programmatically manage/orchestrate the jobs in a clusters of servers. Let me brief some of the use cases which imply the need of such a system. 

<ul>
<li>
Install a package - Lets say we want to install new package to all the nodes. This may be either a general package (eg:- zip), or the typical use cases of this in a clustered environment will be apply OS patches.
</li><li>
Update a configuration - We may find situations where need to update a configuration in all the nodes( eg:- update a conf file )
</li><li>
Restart a service - Due to the update of a configuration or any other reason we may need to restart a particular service in all or selected nodes in a cluster
</li><li>
Debug - Verify the connectivity between/outside the nodes etc.
</li><li>
Monitor - Constantly check the status of the all or selected nodes such as load, memory etc.
</li><li>
Orchestration - There are situations where you want to perform operations on nodes in a particular order or based on some dependencies. Lets say restart servers one after the other. 
</li></ul>
Mcollective supported these functionalities open-endedly. Why I say open ended is that it comes with bunch of inbuilt features to do those but not limited to it. We can plug our own functionalities easily.

<h3>Tips on MCollective Architecture</h3>

![MCollective Component Architecture](/images//posts/20141015/mcollective_architecture.jpg)

<ul>
<li>
Whole MCollective architecture is consisted with three main components - MCollective Demon, Middleware and the Client. 
</li><li>
Middleware is a kind of message broker most of the messaging middleware available (eg:- activeMQ) can be used for the purpose.
</li><li>
Demons (mcollectived) running on each node gets connected with middleware and communication happens through pub/sub manner.
</li><li>
Client will send the message to the middleware and the middleware publishes them accordingly(Broadcast/Directed).
</li><li>
Simple broadcast paradigm is used that demons receive the all the messages with an attached filter but the nodes matches with that filter only get act on them.
</li><li>
In each component has series of plugins (eg:- to perfome above validation a security plugin is available on demon).
</li><li>
Also set of Agent plugin plugins are available to perform actual work in the server demons. For an example to install a package in nodes a package agent plugin must be available on the each node. Sameway to send such request, a agent plugin definition is available in the client as well. There are called DDLs. We can come up with our own agent plugin will be discussed in my next post.
</li></ul>

More detailed view of the architecture can be found <a target="_blank" href="https://docs.puppetlabs.com/mcollective/overview_components.html">here</a>.

<h3>Demo</h3>

Mcollective documentation provides a vagrant based demo. It creates a lightweight cluster with Cent-os based vagrant instances. Instances are configured with mcollective (using puppet) and get connected with middleware. In this demo Redis is used as middleware. Using this we can do hands on on  MCollective. Here are the steps to setup the demo.

<ol type="1">
<li>
Install VirtualBox. 
</li><li>
Download Vagrant from <a target="_blank" href="http://www.vagrantup.com/downloads.html">here</a> based on your current OS and install it. 
</li><li>
Go to the <a href="https://github.com/ripienaar/mcollective-vagrant">mcollective-vagrant repository</a> on GitHub. Clone the repo to your host machine
{% highlight PowerShell %}
git clone git://github.com/ripienaar/mcollective-vagrant.git
{% endhighlight %}
</li><li>
Go to the folder and open Vagrantfile. Change the variables accordingly. By default the instance count (the number of instances to be spawned) is mentioned as 5. 
</li><li>
Then start the Vagrant instances by,
{% highlight PowerShell %}
vagrant up
{% endhighlight %}

This will take several minutes to get all the instances up and running. This will spawn the no of instances mentioned in above plus the middleware instances.
</li><li>

Log in to the middleware Vagrant instance by
{% highlight PowerShell %}
vagrant ssh middleware
{% endhighlight %}
</li>
</ol>
OK, now we are inside the cluster and ready to deal with Mcollective system.
A command line client (mco) is used to interact with Mcollective middleware.Sample command interactions are as follows.

<h3>Examples</h3>
<ul>
<li>
To see the nodes connected,
{% highlight PowerShell %}
mco ping
{% endhighlight %}
</li><li>
MCollective keeps lot of information about the nodes. We can retrieve them by,
{% highlight PowerShell %}
mco inventory middleware.example.net
{% endhighlight %}
</li><li>
To deal with packages in the nodes Mcoolective comes with inbuild package agent plugin
<ul><li>
To check the status of a package,
{% highlight PowerShell %}
mco package status mcollective
{% endhighlight %}
</li><li>
To install a new package,
{% highlight PowerShell %}
mco package install zip
{% endhighlight %}
</li></ul>
For more package manipulation commands,
{% highlight PowerShell %}
mco package --help
{% endhighlight %}
</li><li>
To start/stop services in nodes, service agent plugin is provided
{% highlight PowerShell %}
mco service status httpd
mco service restart httpd
{% endhighlight %}
</li><li>
For more service manipulation commands,
{% highlight PowerShell %}
mco service --help
{% endhighlight %}
</li><li>
Check the network connectivity among nodes, nettest agent is provided,
{% highlight PowerShell %}
mco nettest ping 192.168.2.10
{% endhighlight %}
</li><li>
To monitor processes in node list, process agent is provided
</li><li>
To list the java process running in nodes
{% highlight PowerShell %}
     mco process list java
{% endhighlight %}     
This is exactly like running ps command in each node with grep
</li><li>
To list all such plugins installed in each node,
{% highlight PowerShell %}
mco plugin doc
{% endhighlight %}
</li><li>
To see actions available on those plugins 
{% highlight PowerShell %}
mco plugin doc agent/package
{% endhighlight %}
</li>
</ul>

<h3>Selective communication with nodes</h3>

Further we can narrow down the nodes who will respond to the above commands by introducing a filter to the command itself. As these machines are built with Puppet you can use classes and facts for addressing them.

To the nodes with the roles::middleware class,
{% highlight PowerShell %}
    mco ping -W roles::middleware
{% endhighlight %}   

To the nodes with the roles::node class.
{% highlight PowerShell %}
    mco ping -W roles::node
{% endhighlight %} 

We can now combine this fact with Puppet Classes to pick a subset of your nodes, this
is an AND search,

{% highlight PowerShell %}
mco ping -W "roles::node cluster=alfa"

mco ping -S "(roles::node or roles::middleware) and cluster=alfa"
{% endhighlight %}    

To selectively install zip package to all the nodes,
{% highlight PowerShell %}
mco package install zip -W “roles::node”
{% endhighlight %}

OK, so far we have been dealing with inbuilt agents.Mcollective provides an easy way of writing our own agents to suit our purpose. Sample agent creation will be discussed in my next post.



