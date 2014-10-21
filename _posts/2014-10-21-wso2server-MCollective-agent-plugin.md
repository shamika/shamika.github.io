---
layout: post
title: wso2server - A Custom MCollectoive Agent Plugin 
tags: mcollective, agent, wso2
---

"wso2server" is a custom mcollective agent plugin to perform manupulations related to wso2 instances. The plugin can be found <a href="https://github.com/shamika/wso2-mcollective-plugin">here</a>.

Please refer previous posts to learn about <a href="http://shamika.github.io/First-Steps-on-MCollective/">what mcollective is</a> and <a href="http://shamika.github.io/Create-Custom-MCollective-Agent-Plugin/">how we can write a custom plugin</a>.

<h3>Plugin overview</h3>

Basically this initial version wso2server agent consists of follwoing actions.

0. echo - Test action for the plugin 
1. start_all - Start the wso2 instance running on the node
2. stop_all - Stop the wso2 instance running on the node
3. start - Start the wso2 instance based on the instance/cluster type. eg:- appserver
4. stop - Stop the wso2 instance based on the instance/cluster type. eg:- appserver

The main difference between actions 1,2 and 3,4 is the cluster name should be passed as a argument to the latter actions and they only respond if the particular node is belonged to given cluster name.

<h3>Configuration</h3>

Basic configuration needed for the "wso2server" is the location where the wso2 instance is placed. This should be configured by adding following line to mcollective main configuration file(server.cfg)

{% highlight %}
  plugin.wso2server.location = /opt/wso2/ #wso2 instance location
{% endhighlight %}

If you are using Vagrant based demo add the above line to the file 

  mcollective-vagrant/deploy/modules/mcollective/templates/server.cfg.erb
  
<h3>Deployemnt</h3> 



<h3>Demo</h3>

After adding that respin/reload the 





