+++
author = "Randall Hunt"
categories = ["JIRA", "atlassian"]
date = 0001-01-01T00:00:00Z
description = "Quick post describing how to "
draft = true
image = "/images/2016/05/Screen-Shot-2016-05-19-at-3-43-46-PM.png"
slug = "adding-a-jira-homepage-in-a-jira-plugin"
tags = ["JIRA", "atlassian"]
title = "Adding a JIRA Homepage in a JIRA Plugin"

+++

_This post assumes knowledge of how to write custom JIRA plugins._

Recently I was trying to solve a problem of elegantly encouraging users of an internal JIRA application to adopt a new dashboard we had built. Many of the users are non-technical and are a little hesitant to change so I didn't want to wholesale 301 redirect to something if there was a gentler way of introducing new features.

JIRA provides a few mechanisms:

1. Modify the [System Dashboard](https://confluence.atlassian.com/jira064/configuring-the-default-dashboard-720412276.html) and add a new gadget. (Supported)
1. Override the redirect in ``/opt/atlassian/jira/atlassian-jira/secure/default.jsp`` to point at your new URL. (Not supported, restart required)
1. The completely undocumented MyJiraHome classes.

The problem with #1 is that you can't provide links within your dashboard -- there is no means of "deep linking" in a gadget (if there is and I don't know it please shoot me a message or leave a comment).

The problem with #2 is the lack of maintainability and support. Also I dislike the idea of hacking around JIRA -- it's a pretty awful product in the first place but when you add in tons of hacks you're only making things worse.

I stumbled on #3 on a whim while going through the source of JIRA.

It turns out you can direct to a brand new homepage like so:
![](/content/images/2016/05/Screen-Shot-2016-05-19-at-3-43-46-PM-1.png)

Add the following to your `atlassian-plugin.xml`:

```xml
<web-item key="set_my_jira_home_custom_dashboard" name="Set My JIRA Home to Custom Dashboard" section="system.user.options/set_my_jira_home" weight="10">
    <label>My Fancy Custom Dashboard</label> 
    <link linkId="set_my_jira_home_custom_dashboard">/plugins/servlet/custom_dashboard</link>
</web-item>
```

You can later on update all users by going to your database and running something like:
```sql
UPDATE propertystring 
SET    propertyvalue = 
'org.my-company.jira.plugin.my_custom_plugin:set_my_jira_home_custom_dashboard' 
WHERE  id IN (SELECT id 
              FROM   propertyentry 
              WHERE  property_key = 'my.jira.home'); 
```

Hope this helps people. I spent a few days googling around and was pleasantly surprised to stumble onto this simple answer.