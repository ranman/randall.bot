+++
author = "Randall Hunt"
categories = ["JIRA", "python", "API", "atlassian", "java"]
date = 2014-01-21T00:00:00Z
description = "Quick post describing the use of the JIRA UPM"
draft = false
slug = "jira-plugin-api"
tags = ["JIRA", "python", "API", "atlassian", "java"]
title = "JIRA Universal Plugin Manager API"

+++

Deploying JIRA plugins isn't the most enjoyable process. You're forced to go work through a UI that takes ~10 seconds to load a single page where you upload a .jar hoping that your request went through since there's no feedback. What if you want to automate this process? What if you want to do continuous deployment of a certain plugin?

The functionality for this exists in JIRA and is fairly simple to use. It is also entirely undocumented therefore entirely subject to change. Yay JIRA :(.

JIRA's UPM Documenation: https://ecosystem.atlassian.net/wiki/display/UPM/UPM+REST+API




### Programatically Disable Modules You Don't Need

To do this we'll make a simple [PUT](http://stackoverflow.com/a/630475/240004) request that changes the state of individual plugins or modules to disabled instead of enabled. Simple right? Sort of.

This header: 
`'Content-Type': 'application/vnd.atl.plugins.plugin.module+json'`
tells JIRA that we're sending plugin info. Without it your request will be ignored.

This header: `'X-Atlassian-Token': 'nocheck'` tells JIRA not to check the CSRF token (???) because we're a script and not a client.

To find what the URL for the component you would like to disable you can just make GET requests at higher levels to list the components at lower levels. So I would just `curl -u user:password https://localhost:2990/rest/plugins/1.0/` and drill down the list based on the results.

```prettyprint lang-python
import json
import requests
from requests.auth import HTTPBasicAuth
baseurl = 'http://localhost:2990/jira/rest/plugins/1.0/'
headers = {
    # This is the only content-type the API will accept
    'Content-Type': 'application/vnd.atl.plugins.plugin.module+json',
    # This disables some token checking
    'X-Atlassian-Token': 'nocheck'
}
# We use HTTPBasicAuth (equivalent to curl -u user:pass)
auth = HTTPBasicAuth('admin', 'admin')
# This very long URL is made of two pieces:
#    1. the plugin namespace and key
#    2. the module namespace and key
# You can discover this info with a GET request to these same endpoints.
module_url = 'com.atlassian.jira.plugin.system.issuetabpanels-key/modules/changehistory-tabpanel-key'

# first we grab the JSON representation of the module
data = requests.get(baseurl + module_url, auth=auth).json()

# now we just flip the enabled state in the json
# and PUT it back (with our fancy content-type headers)
data['enabled'] = False
requests.put(
    baseurl+module_url,
    headers=headers,
    auth=auth,
    data=json.dumps(data)
)
```

The API is actually very RESTful and you can discover a lot of the functionality by making GET requests to the same endpoints we're sending these PUT requests. I'd say this is one of JIRA's strong points.

### Uploading A New Module

Luckily installing new plugins is similarly straightforward -- with one additional caveat. JIRA's universal plugin manager (UPM) requires you to pass in a request/session/authentication token with your request.

Let's say I have a module called `my-fancy-plugin.jar` that I've made with `atlas-package`. I can easily upload this with a simple python script like the one below:

```python
import requests
from requests.auth import HTTPBasicAuth
auth = HTTPBasicAuth('admin', 'admin')
url = 'http://localhost:2990/jira/rest/plugins/1.0/'
# tell it not to check XSRF stuff
headers = {'X-Atlassian-Token': 'nocheck'}
# we first GET the URL to get the UPM token
r = requests.get(url, auth=auth)
upm_token = r.headers.get('upm-token')
# lets grab our plugin file (created with atlas-package)
files = {'plugin': open('my-fancy-plugin.jar', 'rb')}
requests.post(
    # shove that UPM token on the end of the URL
    url+'?token='+upm_token,
    files=files,
    headers=headers,
    auth=auth
)
```

This makes devops for jira plugins a lot easier!
You can even follow the status of the installation. The `POST` response has a `links` key that will link to the installation URL and you can ping that for status updates.

A huge shoutout to [@miknight](https://miknight.com/) for all of his help with figuring this out!

JIRA is actually pretty cool -- even though it has some rough spots.