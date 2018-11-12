+++
author = "Randall Hunt"
date = 2014-11-24T00:00:00Z
description = ""
draft = false
slug = "new-elastic-beanstalk-cli"
title = "New Elastic Beanstalk CLI"

+++

We recently launched a significant update to the [AWS Elastic Beanstalk] Command Line Interface.

If you've never used Elastic Beanstalk then I really suggest checking it out. Elastic Beanstalk lets you easily deploy your applications into highly available, load balanced, scalable, and secure environments. It lets us spend more time building applications and less time focusing on managing operations.

My favorite thing about this update is that the entire CLI is now pip installable!

### Prerequisites
To get started is to use pip to install the new `awsebcli` package, I'd recommend throwing in the `--upgrade` flag just in case you have some cached or old versions of one of the dependencies.

```python
pip install --upgrade awsebcli
```

Now, we have to make sure we have the appropriate credentials in `~/.aws/config`, or exported as environment variables. Alternatively you can just input those credentials when prompted by the CLI tool. If you're not sure how to get your AWS API credentials then check out AWS Security Credentials documentation [here][1].

As always, I recommend using IAM and not your root account as outlined [here][2].

The [installation information for the EB CLI][3] has all of this written out explicitly so be sure to check that out if you don't understand something above.

Now that we have all of that sorted out lets create a basic [flask] application and launch it into EB.

### Create A Basic Flask App

I'm going to create a file called `application.py` in a directory called `flaskapp`. The directory name doesn't matter but the name `application.py` is very important because the WSGI wrapper is pre-configured to look for the variable `application` in an application module, which in this case is our file `application.py`. I'm also going to start git, create a `requirements.txt`, and commit all of that.

```shell
mkdir flaskapp && cd flaskapp
git init
touch application.py
touch requirements.txt
```

Now in `requirements.txt` I'm just going to put the string `flask`.

In `application.py` I'm going to put a very basic flask app.

```python
from flask import Flask
application = Flask(__name__)


@application.route('/')
def hello():
        return "Hello From Elastic Beanstalk!"

if __name__ == '__main__':
    application.run()

```

Do a quick sanity check to make sure everything is working (install flask in a virtualenv)

```shell
$ python application.py
 * Running on http://127.0.0.1:5000/
```

Now I'll just add all of this into git and commit it.

```shell
git add application.py
git add requirements.txt
git commit -m "create a basic flask app"
```

### Deploy Application With Elastic Beanstalk

Now that we're setup with both the tools and the app we can deploy it!

There are three steps we'll follow to launch this:

1. Create the elasticbeanstalk scaffolding locally with `eb init`
1. Create the environment we want to deploy to with `eb create`
1. Deploy the code to our environment with `eb deploy`

We'll start simply with `eb init`.
```shell
$ eb init
Select an application to use
1) [ Create new Application ]
(default is 1): 1

Enter Application Name
(default is "flaskapp"):

It appears you are using Python. Is this correct?
(y/n): y

Select a platform version.
1) Python 2.7
2) Python
(default is 1): 1
Do you want to set up SSH for your instances?
(y/n): y

Select a keypair.
1) [ Create new KeyPair ]
(default is 1): 1
```

We'll follow all the prompts, creating a new application, using Python 2.7, setting up SSH, creating a keypair and so on.

If you look in your directory you'll see that the eb tool created a simple yaml file:

```shell
$ cat .elasticbeanstalk/config.yml
branch-defaults:
  master:
    environment: null
global:
  application_name: flaskapp
  default_ec2_keyname: aws-eb
  default_platform: Python 2.7
  default_region: null
  profile: null
  sc: git
```

Now we'll create an environment to launch into:

```shell
$ eb create
Enter Environment Name
(default is flaskapp-dev):
Enter DNS CNAME prefix
(default is flaskapp-dev):
Environment details for: flaskapp-dev
  Application name: flaskapp
  Region: us-east-1
  Deployed Version: 2e30
  Environment ID: e-adhkgp5qhp
  Platform: 64bit Amazon Linux 2014.03 v1.0.9 running Python 2.7
  Tier: WebServer-Standard-1.0
  CNAME: flaskapp-dev.elasticbeanstalk.com
  Updated: 2014-10-30 21:00:18.760000+00:00
Printing Status:
INFO: createEnvironment is starting.
INFO: Using elasticbeanstalk-us-east-1-054060359478 as Amazon S3 storage bucket for environment data.
INFO: Created load balancer named: awseb-e-a-AWSEBLoa-XGVQEWSMHVJ9
INFO: Created security group named: awseb-e-adhkgp5qhp-stack-AWSEBSecurityGroup 1GWC34WGCANPF
INFO: Created Auto Scaling launch configuration named: awseb-e-adhkgp5qhp-stack-AWSEBAutoScalingLaunchConfiguration-19K9ZUWLZIWVA
INFO: Waiting for EC2 instances to launch. This may take a few minutes.
INFO: Created Auto Scaling group named: awseb-e-adhkgp5qhp-stack-AWSEBAutoScalingGroup-WOBT5YY3TSIW
INFO: Created Auto Scaling group policy named: arn:aws:autoscaling:us-east-1:054060359478:scalingPolicy:af3360bd-b878-463b-9a5d-d12248f1774f:autoScalingGroupName/awseb-e-adhkgp5qhp-stack-AWSEBAutoScalingGroup-WOBT5YY3TSIW:policyName/awseb-e-adhkgp5qhp-stack-AWSEBAutoScalingScaleUpPolicy-CZBD5JBXA1M3
INFO: Created Auto Scaling group policy named: arn:aws:autoscaling:us-east-1:054060359478:scalingPolicy:2e484f0b-4011-4bc9-9dfa-869c7a38dda4:autoScalingGroupName/awseb-e-adhkgp5qhp-stack-AWSEBAutoScalingGroup-WOBT5YY3TSIW:policyName/awseb-e-adhkgp5qhp-stack-AWSEBAutoScalingScaleDownPolicy-1K25H4NNBVZ75
INFO: Created CloudWatch alarm named: awseb-e-adhkgp5qhp-stack-AWSEBCloudwatchAlarmHigh-PPH9HS7CIZ8H
INFO: Created CloudWatch alarm named: awseb-e-adhkgp5qhp-stack-AWSEBCloudwatchAlarmLow-LN6HRLWJXEWA
INFO: Added EC2 instance 'i-3cf4cdd1' to Auto Scaling Group 'awseb-e-adhkgp5qhp-stack-AWSEBAutoScalingGroup-WOBT5YY3TSIW'.
INFO: Application available at flaskapp-dev.elasticbeanstalk.com.
INFO: Successfully launched environment: flaskapp-dev
```

With that we're actually ready to go and deploy. It's a simple `eb deploy` and our application is live! (You may notice that we've actually already deployed the app but I want to point out that `eb deploy` is how you'll push from now on).

```shell
$ eb deploy
WARNING: Deploying a previously deployed commit
INFO: Environment update is starting.
INFO: Deploying new version to instance(s).
INFO: New application version was deployed to running EC2 instances.
INFO: Environment update completed successfully.
```

Open up [flaskapp-dev.elasticbeanstalk.com](http://flaskapp-dev.elasticbeanstalk.com), or whatever URL you chose and voila! Your app is ready to go.

Now this is just a very basic flask app. Typically we want to work with other AWS resources and in a follow up post I'll address how to do that but just in case you don't want to wait I'll share how I launch an instance with a postgres RDS instance here:

```shell
eb create \
--instance_type t2.micro \
--database \
--database.engine postgresql \
--database.instance db.t2.micro
```

Thanks for reading and feel free to reach out [@jrhunt] with any questions!





[AWS Elastic Beanstalk]: http://aws.amazon.com/elasticbeanstalk/ "AWS Elastic Beanstalk"
[installation instructions]: http://docs.aws.amazon.com/elasticbeanstalk/latest/dg/eb-cli3-getting-set-up.html "Installation Instructions"
[flask]: http://flask.pocoo.org/docs/0.10/quickstart/ "Flask Quickstart"
[1]: http://docs.aws.amazon.com/AWSSecurityCredentials/1.0/AboutAWSCredentials.html#AccessKeys "Access Keys"
[2]: http://docs.aws.amazon.com/IAM/latest/UserGuide/ManagingCredentials.html#Using_CreateAccessKey "Access Keys via IAM"
[3]: http://docs.aws.amazon.com/elasticbeanstalk/latest/dg/eb-cli3-getting-set-up.html "Getting Setup With eb-cli3"
[@jrhunt]: https://twitter.com/jrhunt "Twitter Account"
