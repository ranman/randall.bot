+++
author = "Randall Hunt"
date = 0001-01-01T00:00:00Z
description = ""
draft = true
slug = "building-an-application-on-amazon-api-gateway-dynamodb-and-s3"
title = "Building an application on Amazon API Gateway, DynamoDB, and S3"

+++

In 2011 I was bored and built an application called [gitshots](http://www.gitshots.com). It was built on top of MongoDB and Flask because I worked at MongoDB and knew flask. I still think that those were perfectly fine choices but I also don't like maintaining servers and infrastructure. This is where the JAWS (Javascript on AWS) stack really shines. You can deploy your entire application without ever needing to ssh.

###Gitshots
At it's core gitshots is 2 very simple POST endpoints: the first accepts a jpeg or png and the second accepts some JSON.