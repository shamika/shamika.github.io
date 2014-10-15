---
layout: post
title: First Steps of MCelective
---

The Marionette Collective (MCollective) is a compressive toolkit developed by Puppet Labs to programmatically manage/orchestrate the jobs in a clusters of servers. Let me brief some of the use cases which imply the need of such system. Let’s imagine we do have two clusters with 3 nodes each up and running. 

Install a package - Lets say we want to install new package to all the nodes. This may be either a general package (eg:- zip), or the typical use cases of this in a clustered environment will be apply OS patches.

Update a configuration - We may find situations where need to update a configuration in all the nodes( eg:- update a conf file )

Restart a service - Due to the update of a configuration or any other reason we may need to restart a particular service in all or selected nodes in a cluster

Debug - Verify the connectivity between/outside the nodes etc.

Monitor - constantly check the status of the all or selected nodes such as load, memory etc.

