+++
author = "Randall Hunt"
categories = ["Amazon Web Services", "s3"]
date = 0001-01-01T00:00:00Z
description = "Solving Amazon Simple Storage Service eventual consistency and read-after-write issues."
draft = true
slug = "randomness-in-tests"
tags = ["Amazon Web Services", "s3"]
title = "The Need for Randomness in Tests"

+++

I was working on a project recently that reminded me of a very common problem I face when working with eventually consistent system. The problem is that as an end-user of that system you're writing tests that depend on a vast underlying system of queues and messages that depend on thousands of other components and customers. This makes writing integration tests that depend on specific timing pretty much impossible.

This post is just a quick demonstration of this class of problem and the best way of getting around it that I know of.

Imagine you're testing your integration with S3 -- I'll use python:

```python
import unittest
import boto3


```