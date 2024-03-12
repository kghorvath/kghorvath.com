---
title: "Federation with the AT Protocol - Part 1"
date: 2024-03-11T22:30:46-04:00
draft: false
---

The creators of Bluesky recently introduced early-access to federation of their AT Protocol. This means that individuals can now self-host their own so-called Bluesky instances, called Personal Data Servers, or (PDS)es.

The process of setting up a PDS is fairly straightfoward. An installation script is provided on the official [repository](https://github.com/bluesky-social/pds) but it's really just a wrapper around installing Docker and spinning up a container hosting the service. The script also spins up a Caddy container for reverse-proxying, and a Watchtower container for updates - but the PDS itself is self-contained in a single container, which is a nice change from Mastodon.

Since I already have a Caddy container running as a reverse proxy, and I can handle updates manually, I'll just spin up the lone PDS container. I'm using Podman so this is the incantation I used:
```
podman run -d --name sky -p 8080:3000 --env-file=pds.env -v /containers/pds:/pds:Z ghcr.io/bluesky-social/pds:0.4
```

Here, I'm using the path `/containers/pds` as my stateful volume for data storage, and `8080` is the external port I'm passing to Caddy to proxy. The `pds.env` file contains the necessary environment variables needed to initially configure the container. The repo contains a full list of options you can customize, but here's the bare minimum I started with.
```
PDS_HOSTNAME=pds.example.com
PDS_JWT_SECRET=<openssl rand --hex 16>
PDS_ADMIN_PASSWORD=<secretpasswordhere>
PDS_PLC_ROTATION_KEY_K256_PRIVATE_KEY_HEX=<openssl ecparam --name secp256k1 --genkey --noout --outform DER | tail --bytes=+8 | head --bytes=32 | xxd --plain --cols 32>
PDS_DATA_DIRECTORY=/pds
PDS_BLOBSTORE_DISK_LOCATION=/pds/blocks
PDS_DID_PLC_URL=https://plc.directory
PDS_BSKY_APP_VIEW_URL=https://api.bsky.app
PDS_BSKY_APP_VIEW_DID=did:web:api.bsky.app
PDS_REPORT_SERVICE_URL=https://mod.bsky.app
PDS_REPORT_SERVICE_DID=did:plc:ar7c4by46qjdydhdevvrndac
PDS_CRAWLERS=https://bsky.network
LOG_ENABLED=true
```

Most of these can remain unchanged. The key ones to set are obviously the PDS_HOSTNAME and the PDS_ADMIN_PASSWORD. PDS_JWT_SECRET and PDS_PLC_ROTATION_KEY_K256_PRIVATE_KEY_HEX can be generated with the commands listed, which are pulled straight from the installer script.

If all goes well, you should be able to browse to your public facing PDS domain and see this:
```
This is an AT Protocol Personal Data Server (PDS): https://github.com/bluesky-social/atproto

Most API routes are under /xrpc/
```

Part 2 of this post will describe how to migrate or create an account on your new PDS and get federating with the atproto network.
