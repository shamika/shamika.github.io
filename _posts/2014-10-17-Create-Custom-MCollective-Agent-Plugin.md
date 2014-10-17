---
layout: post
title: Create Custom Agent Plugin - MColelctive
tags: mcollective, agent
---
In this post I will walk through steps of implement custom agent plugin MCollective. If you need an basic understanding of what MCollective and what it does please refer my previous post.

As it explains Mcollective itself comes with built in agents plugins like package, service etc to deal with various needs within the cluster. But sometimes this may not sufficient for our need. MCollective provides an facility to plug our own agent plugin easily. 

Typically Mcollective plugins are written in ruby. A plugin should contain two files.

<ol>
<li>
Implementation file - File where the main logic of the plugin is placed <br/>
</li><li>
Data Definition File (DDL) - Meta data and the validation information of the plugin is placed.
</li>

We are going to implement simple echo plugin with the following steps. 

<h3>Steps</h3>

<ol>
<li>
Create a ruby file indicating your agent plugin’s name - I name it as wso2server.rb ; So my agent’s name would be wso2server.
<br/></li><li>
Add the follwoing code to the wso2server.rb file, which consis the logic to echo the message.

{% highlight ruby %}
 module MCollective
   module Agent
     class Wso2server< RPC::Agent
       
       # Basic echo server
       action "echo" do
         validate :msg, String

         reply[:msg] = request[:msg]
         reply[:time] = Time.now.to_s
       end
     end
    end
 end
 {% endhighlight %}

As you see the agent plugin should be a sub class of RPC::Agent. It has ac action called "echo" which expects a parameter called :msg. Next line it validates the parameter :msg whether is string or not. These agent actions are stuffed with request and reply variables. In the logic the requests :msg is copied to reply's :msg, so does echo. Aditionally sets the :time value in reply message.

<br/></li>
<li>
Create meta data file corresponding to wso2server agent. It should be same as agent name with .ddl extention.Create wso2server.ddl and add the following,

{% highlight ruby %}
metadata        :name        => "wso2",
                :description => "Agent for MCollective to manupulate wso2 instances",
                :author      => "Shamika",
                :license     => "Apche",
                :version     => "1.0",
                :url         => "http://projects.puppetlabs.com/projects/mcollective-plugins/wiki",
                :timeout     => 60

requires :mcollective => "2.2.1"
{% endhighlight %}

</li>
</ol>
