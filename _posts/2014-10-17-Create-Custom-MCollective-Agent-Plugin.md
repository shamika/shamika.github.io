---
layout: post
title: Create Custom Agent Plugin - MCollelctive
tags: mcollective, agent
---
In this post I will walk through steps of implement custom agent plugin MCollective. If you need an basic understanding of what MCollective and what it does please refer my previous post.

As it explains Mcollective itself comes with built in agents plugins like package, service etc to deal with various needs within the cluster. But sometimes this may not sufficient for our need. MCollective provides an facility to plug our own agent plugin easily. 

Typically Mcollective plugins are written in ruby. A plugin should contain two files.

<ol>
<li>
Implementation file - File where the main logic of the plugin is placed <br/><br/>
</li><li>
Data Definition File (DDL) - Meta data and the validation information of the plugin is placed.
</li>

We are going to implement simple echo plugin and going to deploy it in the Vagrant based demo described in the last post. Here are the steps. 

<h3>Steps</h3>

<ol>
<li>
Create a ruby file indicating your agent plugin’s name - I name it as wso2server.rb; So my agent’s name would be wso2server.
<br/><br/></li><li>
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

<br/><br/></li>
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

action "echo", :description => "Perform URL test" do
    display :always

     input :msg,
          :prompt      => "Message",
          :description => "Message to echo",
          :type        => :string,
          :validation  => '^[a-zA-Z\-_\d]+$',
          :optional    => false,
          :maxlength   => 120

     output :msg,
          :description => "Mesage recieved",
          :display_as  => "Message"

     output :time,
          :description => "Time that the message was recieved",
          :display_as  => "Time"

end

{% endhighlight %}
In the meatadata section we define general info about the agent such as :name, :auther, :license etc. One of the important variable that we define here is the “:timeout”. This is the time that Mcollective should wait until any of the actions in this plugin is performed in a node. In other words if no response comes for echo action from a particular node within 60 seconds the mcollective will times out and no longer waiting for a response.

Then we define the meta data related to the actions. In above we have defined what are the input and output parameters and their validation conditions for echo action.

Now the implementation of the agent is completed. Let us see how can we deploy this.
<br/><br/></li><li>
Deploy the agent by copying the two files (wso2server.rb, wso2server.ddl) to mcollective plugins location in each node. In the demo the location is,

/usr/libexec/mcollective/mcollective/agent

According to the Mcollective documentation the location differs as follows <br />

Red Hat-like OSes - /usr/libexec/mcollective <br />
Debian-like OSes - /usr/share/mcollective/plugins <br />

Even Though we copied these files manually across all the nodes, in a real cluster this is not practical. So we should generally use Puppet or other configuration management to copy the files.

<br/><br/></li><li>
Restart the Mcollective demons (mcollectived) in the nodes.
Now everything is done. Let's test it.
</li>
</li>
</ol>

<h3>Test it</h3>

<li>
Let us check the doc of the plugins available in each node and see our newlly created plugin is available,

mco plugin doc

You will see it added in the "Agents" section.
![MCollective Component Architecture](/images//posts/20141017/custom_agent.png)




</li>
