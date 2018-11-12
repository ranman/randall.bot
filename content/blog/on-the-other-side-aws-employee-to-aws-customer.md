+++
author = "Randall Hunt"
date = 2015-12-09T00:00:00Z
description = ""
draft = false
slug = "on-the-other-side-aws-employee-to-aws-customer"
title = "On the other side: AWS Employee to AWS Customer"

+++

For most of 2014 and 2015 I worked as a developer evangelist at AWS in New York City. I really enjoyed my job there and would probably still be there if the opportunity to work at SpaceX had not presented itself. Before I ever worked at Amazon I was an AWS customer so going into my job as a developer evangelist was actually pretty easy. I was already evangelizing AWS usage for my friends and professional peers. I learned a tremendous amount working at Amazon. Everything is on wikis, internal training, and videos. If you spend a lot of time on planes you can download a lot of information in advance and just read/watch/listen for hours and absorb the cumulative effort and knowledge of 10,000 people in a matter of weeks. So, obviously, I did that... because I'm a nerd.

As an engineer at Amazon I was very impressed with the pace of innovation but I also found it pretty easy to keep up with things. It was my job, in fact, to be familiar with all of the services. As a customer of amazon the pace of innovation is ABSOLUTELY staggering. Don't get me wrong, I have numerous complaints and there are hundreds, if not thousands, of things that can be better. However, I cannot deny how insanely impressive their rate of new services/updates is. I wanted to take the next few hundred words and highlight a few things that Amazon is doing right.

1. Customer Focus -- The account manager (Eric Abbott) that I interact with most for AWS has made himself available any time of day or night. We text frequently about updates/requests. He has organized meetings with the heads of various products/services/teams and escalated tickets as necessary. We recently had an issue that required an IMMEDIATE response and he, with the support team, was able to resolve that completely non-trivial request within 3 hours. I'm not saying the AWS support is always good. They're famous for violating their SLAs -- but they go above and beyond to deliver results and the people working there know what they're doing. They're all top-notch engineers with a complete understanding of the services they work on.

2. API first design: At Amazon designers (people who are vital and do way more than just draw pictures) and developers work closely on new services to design the developer experience. As a developer I know I'm prone to laziness and I think most other developers would admit this as well. Having a designer hold everything from your API endpoints to your UI compatibility to a high standard makes sure you're constantly executing and operating exceptionally well.

3. VPC -- I could write 1000s of words about the amazing things you can do with VPC that you really couldn't do efficiently before. However, like everything at Amazon there are tons of frustrating parts: DNS, peering, routetables, ACLs -- there's not a clear single way of accomplishing any single task.

4. API Gateway and Lambda: this is the opus, the madonna, the masterpiece that makes AWS the future beyond any other cloud provider. When Werner announced this for the first time at re:invent 2014 you could tell the difference between the tech journalists and the engineers instantly. Half the audience was in awe and the rest failed to understand the implications. AWS Lambda allows for truly infrastructureless code. With API Gateway in front of it you get an infinitely scalable 


Now for some things AWS could do betterL
1. Documentation. One quick win here is to stop listing the documentation version as the API version which for s3 hasn't been updated in about 10 years. It makes deciding if the resource you're looking at is current impossible. In general documentation lacks enough examples for each invocation and parameter of the various commands and API endpoints.
2. Govcloud. This is the poor bastard child of AWS and I feel like it should either be cut out entirely or made into a real AWS region. There are enough paranoid enterprises that it could be a real region that AWS could charge extra for the increased security. Alternatively you could build a means of isolating individual workloads/accounts for ITAR/fedramp/govcloud compliance.
3. More service status introspection: the AWS service status page is notorious for lagging behind actual outages and the information provided is often opaque.
4. Service integration: I hesitate to include this but the various Code* services are not nearly as integrated as they could have been. ElasticBeanstalk should be able to spin up RDS/DynamoDB/Elasticache/etc. more easily (it's pretty easy in the UI but in the API it's heinously difficult). API Gateway should have predefined lambda or other endpoints for talking to RDS (otherwise API gateway's integration with other services is insanely good!). VPC service endpoints should be abstract instead of just for S3. Etc. etc.... 

It's strange to think that I have hundreds of complaints about AWS but the list gets smaller and smaller all the time. They release early and often and it means you're perpetually at least a little frustrated with the services... but when you zoom out and take a macro level view of what you're doing you can't help but be amazed at how much easier things are than they used to be.

I'm betting big on AWS.