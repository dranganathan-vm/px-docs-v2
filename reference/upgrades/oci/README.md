# OCI

If you have deployed Portworx in a standalone container using OCI,, then you can upgrade Portworx by executing  `pxctl upgrade` ,as shown below, on each node in the cluster.

```text
# pxctl upgrade px-enterprise
Upgrading px-enterprise to the latest version
Downloading latest PX enterprise layers...
Pulling from portworx/px-enterprise
Digest: sha256:5e6fa51a8cd0b2028e70243d480983df07a419228be85031fca9f5a5be0cad2b
Status: Image is up to date for portworx/px-enterprise:latest
Removing old PX enterprise layers...
Starting latest PX Enterprise...

# pxctl --version
pxctl version 1.0.0-a28e0a4
```

