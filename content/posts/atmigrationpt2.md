---
title: "Federation with the AT Protocol - Part 2"
date: 2024-03-12T12:45:31-04:00
draft: false
---

If you're coming here from [Part 1](https://www.kghorvath.com/posts/atmigration/), you should have a personal data server (PDS) up and running. In this part, we'll explore actually using it - by either creating or migrating an account to the new instance.

Either way, we'll need to communicate with our new instance using a RESTful API. The simplest way is to just use `curl` from the command line.

### Creating a new account

Since we did not set up our PDS with any sort of email verification, we'll need to first create an invite code in order to be able to create an account. To do this, we'll need to construct a POST request to the server as follows, using the admin credentials you specified when you created the PDS in part 1.

```
curl -X POST -L "https://pds.example.com/xrpc/com.atproto.server.createInviteCode" \
    -H "Content-Type: application/json" \
	--user "admin:<password>" \
	-d '{"useCount": 1}' \
	| jq --raw-output '.code'
```

If all goes well, it should spit on an invite code. From here, you can do another POST request to create your user, but it's probably easier just to head to [bsky.app](https://bsky.app) and create a new account there, pointing towards your PDS.

### Migrating an account

Migrating an existing account over is a bit more complicated. There's currently no user-friendly way to do so, so the only way is again through the RESTful API. The [account migration doc](https://github.com/bluesky-social/pds/blob/main/ACCOUNT_MIGRATION.md) on the official repository does a good job of explaining the process, as well as the risks and implications of doing so. To make things easier, I've also created a [shell script](https://github.com/kghorvath/atmigration) that semi-automates the process. I encourage you to read through it to get an understanding of what steps are taking place before following through.
