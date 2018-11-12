+++
author = "Randall Hunt"
date = 0001-01-01T00:00:00Z
description = ""
draft = true
slug = "cleaning-up-aws-with-boto3"
title = "Cleaning up AWS with Boto3"

+++

![](/content/images/2015/09/black-and-white-man-person-street.jpg)
A common problem I come across when people are first getting started with AWS is that they fail to properly clean up after themselves. They're like that roommate freshman year that has never lived on their own. You have this fantastic apartment but you're so focused on trying out all the cool things in it that you forget to take out the trash, dust, and do some general cleaning up.

By far the best way to get a handle on what you're spending and what you need to cleanup is through your billing dashboard at: https://console.aws.amazon.com/billing/home#/bill
![](/content/images/2015/09/Screenshot-2015-09-29-22-28-05.png)

For particularly large accounts you may also want to check out a tool that Netflix built called ICE: https://github.com/Netflix/ice
![](https://github.com/Netflix/ice/raw/master/screenshots/ss_summary.png?raw=true)

While both of these tools help you identify your cost categories they won't help you actually clamp down on them. For that you actually have to audit and maintain your resources. An existing tool for this is part of Netflix's existing simian army called [Janitor Monkey](https://github.com/Netflix/SimianArmy/wiki/Janitor-Home). I recommend checking that out if you find that these problems are frequently occurring.

For smaller accounts the cleanup can be entirely manual -- just go through and find the stuff you're not using anymore. However, once you start creating instances and AMIs programmatically you start running into issues where you're not quite sure what you should and shouldn't delete anymore.

Let's say you created an [AMI](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AMIs.html) and in the `blockmapping` you had not set `delete on termination` for the root volume. Now you have thousands of renegade snapshots and volumes and you're not quite sure what to do with them. Luckily there are great tools to help you fix that in boto3.

## DISCLAIMER:
<span style="color: red">This will delete things from your AWS account. Be very careful running this code.</span>

First lets try to find all of the volumes that are available.
```python
import boto3
from datetime import datetime, timedelta
region = "us-east-1"
ec2 = boto3.resource("ec2", region_name=region)
def get_available_volumes():
    available_volumes = ec2.volumes.filter(
        Filters=[{'Name': 'status', 'Values': ['available']}]
    )
    return available_volumes
```

Pretty straightforward right? Except what we really want to know is how long those volumes have been available. If they've been idle for a while maybe we can get rid of them. To do this we're going to need to interrogate AWS Cloudwatch for some info on the volumes. Luckily it exposes a `VolumeIdleTime` metric.

```python
import boto3
from datetime import datetime, timedelta
region = "us-east-1"
cloudwatch = boto3.client("cloudwatch", region_name=region)
today = datetime.now() + timedelta(days=1) # today + 1 because we want all of today
two_weeks = timedelta(days=14)
start_date = today - two_weeks

def get_metrics(volume_id):
    """Get volume idle time on an individual volume over `start_date`
       to today"""
    metrics = cloudwatch.get_metric_statistics(
        Namespace='AWS/EBS',
        MetricName='VolumeIdleTime',
        Dimensions=[{'Name': 'VolumeId', 'Value': volume_id}],
        Period=3600,  # every hour
        StartTime=start_date,
        EndTime=today,
        Statistics=['Minimum'],
        Unit='Seconds'
    )
    return metrics['Datapoints']

def is_candidate(volume_id):
    """Make sure the volume has not been used in the past two weeks"""
    metrics = get_metrics(volume_id)
    if len(metrics):
        for metric in metrics:
            # idle time is 5 minute interval aggregate so we use
            # 299 seconds to test if we're lower than that
            if metric['Minimum'] < 299:
                return False
    # if the volume had no metrics lower than 299 it's probably not
    # actually being used for anything so we can include it as
    # a candidate for deletion
    return True
```

What we're doing here is using the `get_metric_statistics` [method](http://docs.aws.amazon.com/AmazonCloudWatch/latest/APIReference/API_GetMetricStatistics.html) of CloudWatch to get which volumes aren't really being used anymore. We call `get_metrics` on each of our available volumes to see which ones are really not being used.

Then if we want to we can loop through and do something like:
```python
available_volumes = get_available_volumes()
candidate_volumes = [
    volume
    for volume in available_volumes
    if is_candidate(volume.volume_id)
]
# delete the unused volumes
# WARNING -- THIS DELETES DATA
for candidate in candidate_volumes:
    candidate.delete()
```

That's the an easy way to cleanup your volumes.

Next you clean up your AMIs and then your snapshots:
```
instances = ec2.instances.all()
my_images = ec2.images.filter(Owners=["my-account-id"))
# anything that's running or stopped we want to keep the AMI
good_images = set([instance.image_id for instance in ec2.instances.all()])
# build a dictionary of all the images that aren't in good_images
my_images_dict = {image.id: image for image in my_images if image.id not in good_images}
# now lets deregister all the AMIs older than two weeks
for image in image.values():
    created_date = datetime.strptime(
        image.creation_date, "%Y-%m-%dT%H:%M:%S.000Z")
    if created_date < two_weeks_ago:
         image.deregister()
```


The snapshots are also pretty easy to get rid of:
```python
images = ec2.images.all()
images = [image.id for image in images]
for snapshot in ec2.snapshots.filter(OwnerIds=["my-account-id"]):
    r = re.match(r".*for (ami-.*) from.*", snapshot.description)
    if r:
        if r.groups()[0] not in images:
            snapshot.delete()
```

As you can see using [boto3](http://boto3.readthedocs.org/en/latest/guide/quickstart.html) is pretty powerful and great for programmatically taking control of your AWS resources.

Let me know if this code doesn't work for you or if you have any other questions about AWS!