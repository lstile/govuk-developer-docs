---
owner_slack: "#2ndline"
title: SSH into GOV.UK servers from the VM
section: Development VM
layout: manual_layout
parent: "/manual.html"
last_reviewed_on: 2017-09-27
review_in: 6 months
---

You will need to either forward your publickey from the host machine to the
VM or have your VM publickey added to your [user manifest][user-manifests].

To confirm your key has been forwarded to the development vm you can run:

```shell
vagrant ssh # ssh onto vm
ssh-add -L  # list key and location on host machine
```
Things to check if it doesn't work:

-   **Can you SSH directly onto the jumpbox?**
    `ssh jumpbox.integration.publishing.service.gov.uk` If not, check your ssh
    version and config.
-   **Do you get a permission denied error?** Make sure you're in the
    user list in the [govuk-secrets repo](https://github.com/alphagov/govuk-secrets/tree/master/puppet/hieradata)
    for production access, or the [govuk-puppet repo](https://github.com/alphagov/govuk-puppet/tree/master/hieradata)
    for access to other environments.
-   **Are you connecting from outside Aviation House?** You'll need to
    connect to the Aviation House VPN first; SSH connections are
    restricted to the Aviation House IP addresses.
