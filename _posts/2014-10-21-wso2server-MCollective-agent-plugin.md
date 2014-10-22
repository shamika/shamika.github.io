---
layout: post
title: wso2server - MCollective Agent Plugin for WSO2
tags: mcollective, agent, wso2
---

"wso2server" is a custom mcollective agent plugin to perform manupulations related to wso2 instances. The plugin can be found <a href="https://github.com/shamika/wso2-mcollective-plugin" target="_blank">here</a>.

Please refer previous posts to learn about <a href="http://shamika.github.io/First-Steps-on-MCollective/" target="_blank">what mcollective is</a> and <a href="http://shamika.github.io/Create-Custom-MCollective-Agent-Plugin/" target="_blank">how we can write a custom plugin</a>.

<h3>Plugin overview</h3>

Basically this initial version wso2server agent consists of follwoing actions.

0. echo - Test action for the plugin 
1. start_all - Start the wso2 instance running on the node
2. stop_all - Stop the wso2 instance running on the node
3. start - Start the wso2 instance based on the instance/cluster type. eg:- appserver
4. stop - Stop the wso2 instance based on the instance/cluster type. eg:- appserver

The main difference between actions 2,3 and 4,5 is the cluster name should be passed as an argument to the latter actions and they only respond if the particular node is belonged to given cluster name.

<h3>Configuration</h3>

Basic configuration needed for the "wso2server" is the location where the wso2 instance is placed. This should be configured by adding following line to mcollective main configuration file(server.cfg)

{% highlight powershell %}
  plugin.wso2server.location = /opt/wso2/ #wso2 instance location
{% endhighlight %}

If you are using Vagrant based demo add the above line to the file 

  mcollective-vagrant/deploy/modules/mcollective/templates/server.cfg.erb
  
<h3>Deployemnt</h3> 

Deploy the agent by copying the two files (wso2server.rb, wso2server.ddl) to mcollective agent plugins' location in each node which the wso2 instances are running. In the Vagrant based demo the location is, 

/usr/libexec/mcollective/mcollective/agent 

According to the Mcollective documentation this location differs as follows based on the OS, 

Red Hat-like OSes - /usr/libexec/mcollective 
Debian-like OSes - /usr/share/mcollective/plugins 

Even Though we copied these files manually across all the nodes, in a real cluster this is not practical. So we should generally use "Puppet" or other configuration management to copy the files.

Finally restart the Mcollective demons (mcollectived) in the nodes.

<h3>Demo</h3>

<ul><li>
To test the plugin,

{% highlight powershell %}
mco rpc wso2server echo msg="Hello"
{% endhighlight %}
Will send the Hello message from each node.
<li></li>
To see teh plugin documentation,
{% highlight powershell %}
mco plugin doc agent/wso2server
{% endhighlight %}
<li></li>
To stop all the wso2 server instances,
{% highlight powershell %}
mco rpc wso2server stop_all
{% endhighlight %}
<li></li>
To start all the wso2 server instances,
{% highlight powershell %}
mco rpc wso2server start_all
{% endhighlight %}
<li></li>
To stop all the nodes in app server cluser.
{% highlight powershell %}
mco rpc wso2server stop cluster="appserver"
{% endhighlight %}

The wso2 server insances belonged to appserver cluster will be stopped while others will reposnd as "Not Interested".<br/><br/>

Please note that with the current imeplentation, above node filtering is being done based on the hostname. In otherwords the nodes having "appserver" as pasrt of their hostname (eg:- 23451232.appserver.example.com) will be identified as a node in appserver cluster. In fact this is typical scenario in puppet based deployments.
</li></li>
To start all the nodes in app server cluser.
{% highlight powershell %}
mco rpc wso2server start cluster="appserver"
{% endhighlight %}
</li></ul>





