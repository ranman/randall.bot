+++
author = "Randall Hunt"
date = 0001-01-01T00:00:00Z
description = ""
draft = true
slug = "govcloud-lessons-learned"
title = "AWS GovCloud Lessons Learned"

+++

The following information is relevant for very few people but I feel like it should be documented somewhere.

## What is govcloud?

The AWS page here describes it pretty well: https://aws.amazon.com/govcloud-us/

"AWS GovCloud (US) is an isolated AWS Region designed to allow US government agencies and customers to move sensitive workloads into the cloud by addressing their specific regulatory and compliance requirements. The AWS GovCloud (US) framework adheres to U.S. International Traffic in Arms Regulations (ITAR) regulations as well as the Federal Risk and Authorization Management Program (FedRAMPSM) requirements"

Working at SpaceX means we spend a lot of time complying with and adhering to ITAR. GovCloud is one of the ways we're able to do that. However GovCloud is not a first class citizen in the AWS world. To demonstrate this is what a regular AWS console looks like:
![regular aws console](/content/images/2015/11/Screenshot-2015-11-25-14-26-30.png)

and this is what the govcloud console looks like:
![govcloud aws console](/content/images/2015/11/Screenshot-2015-11-25-14-27-03.png)

Obviously a lot of features are missing. I want to say that AWS has been doing a really great job of getting new features into govcloud but there are a few oversights and bugs that I hit and I wanted to document them so other people would not spend any time on the same issues.


## GovCloud ARNs

Many AWS tutorials will reference an ARN which frequently has a region built in. Some identity federation tutorials for instance will give you URLs/ARNs with non-govcloud friendly endpoints... In general when you see 

## GovCloud S3

S3 in govcloud has a couple of issues:
1. You need to access things in S3 from within AWS using the FIPS endpoints