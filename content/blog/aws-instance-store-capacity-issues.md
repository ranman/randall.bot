+++
author = "Randall Hunt"
date = 0001-01-01T00:00:00Z
description = ""
draft = true
slug = "aws-instance-store-capacity-issues"
title = "AWS Instance Store Capacity Issues"

+++

**TL;DR**: Always specify your entire block device mapping at instance launch.

Today I got to enjoy this little "feature" (quirk/bug) of AWS.

`aws ec2 run-instances --image-id ami-12345abc --instance-type m3.xlarge --key-name demo`

Let's say my `ami-12345abc` has a block device mapping of:

```
/dev/sda1=snap-12345678:8:true:standard
/dev/sdb=ephemeral0
/dev/sdc=ephemeral1
```

I was surprised that when I launched this on some fairly sizable instances I did not get my ephemeral (instance store) drives. **However**, if one specifies even a single ephemeral drive in the block device mapping at launch I would get every single ephemeral drive available on the instance (not just the two I requested in the AMI).

Fast forward to 30 minutes later and I find the source of this mysterious failure:
If you launch an M3.* instance-type and your AMI has a block device mapping that specifies instance stores you're not guaranteed to get them.

>"Depending on instance store capacity at launch time, **M3 instances may ignore AMI instance store block device mappings** at launch **unless they are specified at launch**. You should specify instance store block device mappings at launch time, even if the AMI you are launching has the instance store volumes mapped in the AMI, to ensure that the instance store volumes are available when the instance launches."

><cite>[http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/block-device-mapping-concepts.html](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/block-device-mapping-concepts.html)</cite>

<iframe src="//giphy.com/embed/6OWIl75ibpuFO" width="480" height="360" frameBorder="0" class="giphy-embed" allowFullScreen></iframe><p><a href="http://giphy.com/gifs/star-trek-frustrated-facepalm-6OWIl75ibpuFO">via GIPHY</a></p>

So, some words of advice: always specify your block device mappings at launch.