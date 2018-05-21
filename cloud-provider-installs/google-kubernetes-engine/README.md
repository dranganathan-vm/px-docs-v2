# Google Kubernetes Engine

To set up Google Kubernetes Engine to use Portworx, follow the steps below.

### Create a GKE cluster {#create-a-gke-cluster}

Portworx is supported on GKE cluster provisioned on [Ubuntu Node Images](https://cloud.google.com/kubernetes-engine/docs/node-images). When creating clusters, specify the node image as **Ubuntu**. Find more information about creating GKE clusters [here](https://cloud.google.com/kubernetes-engine/docs/clusters/operations).

### Specify a disk template

Portworx takes in a disk spec which gets used to provision GCP persistent disks dynamically.

A GCP disk template defines the Google persistent disk properties that Portworx will use as a reference. There are two methods you can use to provide this template to Portworx. These are discussed below.

**Method 1: Create your own template spec**

The spec follows the following format:

```text
"type=<GCP disk type>,size=<size of disk>"
```

* **type**: Following two types are supported
  * _pd-standard_
  * _pd-ssd_
* **size**: This is the size of the disk in GB

See [GCP disk](https://cloud.google.com/compute/docs/disks/) for more details on the above parameters.

Examples:

* `"type=pd-ssd,size=200"`
* `"type=pd-standard,size=200","type=pd-ssd,size=100"`

You will use the template spec later, in the _Install Portworx with Kubernetes_ topic

**Method 2: Reference an existing GCP disk's template spec**

You can also reference an existing GCP disk as a template.  On every node where Portworx is active, a new GCP volume\(s\) identical to the template volume\(s\) will be created.

For example, if you created a template GCP disk called _px-disk-template-1_, you can pass this in to Portworx as a parameter as a storage device.

Ensure that these disks are created in the same zone as the GCP node group.

You will use your existing GCP disk's template in the _Install Portworx with Kubernetes_ topic, which is discussed below.

### Install Portworx with Kubernetes

After completing the previous steps, you are ready to install Portworx with Kubernetes.

Click on the link below to find the instructions for this install. As part of this install, you will refer to the _Spec Parameter Notes_ section that is directly underneath this section.

{% page-ref page="../../kubernetes-portworx-install/" %}





