# Preparing your Platform for Install

You can install Portworx with Kubernetes on various cloud and on-premise platforms. Find your platform below and follow the installation instructions. All platforms require you to have Kubernetes version 1.6 or later installed.

## Install in the Cloud with Kubernetes

{% tabs %}
{% tab title="Azure \(Container w/Kubernetes\)" %}
### Install the Azure Container Service Engine

The ACS Engine binary files are located [here](https://github.com/Azure/acs-engine/releases). To install the ACS Engine on Linux, run this command:

`curl -L https://aka.ms/InstallAzureCli | bash`

### Install the Azure CLI

Follow the steps [here](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) to install the Azure CLI.

### Login to Azure and set the subscription

```text
az login
az account set –subscription <Your-Azure-Subscription-UUID>
```

### Create the Azure resource group and location

Get the Azure locations using the Azure CLI command:

```text
az account list-locations
```

Example locations: 

`centralus,eastasia,southeastasia,eastus,eastus2,westus,westus2,northcentralus`   
`southcentralus,westcentralus,northeurope,westeurope,japaneast,japanwest`   
`brazilsouth,australiasoutheast,australiaeast,westindia,southindia,centralindia`   
`canadacentral,canadaeast,uksouth,ukwest,koreacentral,koreasouth`

### Create an Azure resource group by specifying a name and a location

```text
az group create –name <region-name> –location <location>
```

### Create a service principal in Azure AD

```text
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/72c299a4-xxxx-xxxx-xxxx-6855109979d9"
{
  "appId": "1311e5f6-xxxx-xxxx-xxxx-ede45a6b2bde",
  "displayName": "azure-cli-2017-10-27-07-37-41",
  "name": "http://azure-cli-2017-10-27-07-37-41",
  "password": "ac49a307-xxxx-xxxx-xxxx-fa551e221170",
  "tenant": "ca9700ce-xxxx-xxxx-xxxx-09c48f71d0ce"
}
```

### Select and customize the deployment configuration

The example deployment here uses Kubernetes with pre-attached disks and VM scale sets. Find a sample JSON file in the acs-engine repository here: [examples/disks-managed/kubernetes-preAttachedDisks-vmas.json](https://github.com/Azure/acs-engine/blob/master/examples/disks-managed/kubernetes-preAttachedDisks-vmas.json)

Ensure that the Portworx target nodes have at least one “local” attached disk which can be used to contribute storage to the global storage pool.

For the `masterProfile`, specify an appropriate value for `dnsPrefix` which will be used for fully qualified domain name \(FQDN\) \[ Ex: “myacsk8scluster”\].   
Use the default `vmSize` or select an appropriate value for the machine type and size.   
Specify the number and size of disks that will be attached to each DCOS private agent as per the template default:

```text
[...]
"diskSizesGB": [128, 128, 128, 128]
[...]
```

Specify the `adminUsername` and public key data in `keyData`

Specify the `clientId` and `secret`  which are located under the `servicePrincipalProfile`  

### Generate the Azure Resource Management \(ARM\) templates

```text
acs-engine generate my-k8s-preAttachedDisks-vmas.json
```

The output files are generated in the `_output/$NAME` directory where `$NAME` corresponds to the name used for the `dnsPrefix`.  For more information on the specific files that are generated, see this [article](https://github.com/Azure/acs-engine/blob/master/docs/acsengine.md).

### Deploy the generated ARM template

```text
az group deployment create \
    --name "$NAME" \
    --resource-group "$RGNAME" \
    --template-file "./_output/$NAME/azuredeploy.json" \
    --parameters "./_output/$NAME/azuredeploy.parameters.json"
```

where `$RGNAME` corresponds to the resource group name created above, and `$NAME` corresponds to the above value used for `dnsPrefix`
{% endtab %}

{% tab title="Azure \(Kubernetes Service\)" %}
To set up the Azure Kubernetes Service \(AKS\) to use Portworx, follow the steps below. For more information on AKS, see this [article](https://docs.microsoft.com/en-us/azure/aks/intro-kubernetes).

### Install the Azure CLI

Follow the steps [here](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) to install the Azure CLI.

### Login to the Azure and set the subscription

```text
az login
az account set –subscription <Your-Azure-Subscription-UUID>
```

### Create the Azure resource group and location

Get the Azure locations using the Azure CLI command:

```text
az account list-locations
```

Example locations: `centralus,eastasia,southeastasia,eastus,eastus2,westus,westus2,northcentralus`   
`southcentralus,westcentralus,northeurope,westeurope,japaneast,japanwest`   
`brazilsouth,australiasoutheast,australiaeast,westindia,southindia,centralindia`   
`canadacentral,canadaeast,uksouth,ukwest,koreacentral,koreasouth`

### Create an Azure Resource Group by specifying a name and a location:

```text
az group create –name <region-name> –location <location>
```

### Create a service principal in Azure AD

```text
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/72c299a4-xxxx-xxxx-xxxx-6855109979d9"
{
  "appId": "1311e5f6-xxxx-xxxx-xxxx-ede45a6b2bde",
  "displayName": "azure-cli-2017-10-27-07-37-41",
  "name": "http://azure-cli-2017-10-27-07-37-41",
  "password": "ac49a307-xxxx-xxxx-xxxx-fa551e221170",
  "tenant": "ca9700ce-xxxx-xxxx-xxxx-09c48f71d0ce"
}
```

### Create the AKS cluster

Create the AKS cluster using either the Azure CLI or the Azure Portal. This is described on the [AKS docs page](https://docs.microsoft.com/en-us/azure/aks/).

### Attach a Data Disk to Azure VM

Follow these instructions: [How to attach a data disk to a AKS nodes in the Azure portal ](https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-attach-disk-portal/)

Below is an example of how your deployment may look:

![Azure Add Disk](https://docs.portworx.com/images/azure-add-disk.png)
{% endtab %}

{% tab title="AWS \(KOPS and Auto-Scaling Groups\)" %}
For information on using KOPS, go [here](https://aws.amazon.com/blogs/compute/kubernetes-clusters-aws-kops/) and [here](https://github.com/kubernetes/KOPS/blob/master/docs/aws.md).

For more information on using Auto-Scaling groups, go [here](https://docs.aws.amazon.com/autoscaling/ec2/userguide/what-is-amazon-ec2-auto-scaling.html). 

### Granting Portworx the needed AWS permissions {#aws-requirements}

Portworx creates and attaches EBS volumes. As such, it needs the AWS permissions to do so. Below is a sample policy describing these permissions:

```text
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "<stmt-id>",
            "Effect": "Allow",
            "Action": [
                "ec2:AttachVolume",
                "ec2:DetachVolume",
                "ec2:CreateTags",
                "ec2:CreateVolume",
                "ec2:DeleteTags",
                "ec2:DeleteVolume",
                "ec2:DescribeTags",
                "ec2:DescribeVolumeAttribute",
                "ec2:DescribeVolumesModifications",
                "ec2:DescribeVolumeStatus",
                "ec2:DescribeVolumes",
                "ec2:DescribeInstances"
            ],
            "Resource": [
                "*"
            ]
        }
    ]
}
```

You can provide these permissions to Portworx in multiple ways. Click on the link below for more information.

{% page-ref page="key-management/portworx-with-aws-kms.md" %}

### Specify a disk template {#ebs-volume-template}

An EBS volume template defines the EBS volume properties that Portworx will use as a reference. There are two methods you can use to provide this template to Portworx. These are discussed below.

#### Method 1: Create your own template spec

In Portworx 1.3 and higher, you can specify a template spec which will be used by Portworx to create new EBS volumes.

The spec follows this format:

```text
"type=<EBS volume type>,size=<size of EBS volume>,iops=<IOPS value>"
```

* **type**: Following two types are supported
  * _gp2_
  * _io1_ \(For io1 volumes specifying the iops value is mandatory.\)
* **size**: This is the size of the EBS volume in GB
* **iops**: This is the required IOs per second from the EBS volume.

See [EBS details](https://aws.amazon.com/ebs/details/) for more details on above parameters.

Examples:

* `"type=gp2,size=200"`
* `"type=gp2,size=100","type=io1,size=200,iops=1000"`

You will use the template spec later, in the _Install Portworx with Kubernetes_ topic.

#### Method 2: Use existing EBS volumes as templates

You can also reference an existing EBS volume as a template. Create at least one EBS volume using the AWS console or AWS CLI. This volume \(or a set of volumes\) will serve as a template EBS volume\(s\). On every node where Portworx is active, a new EBS volume\(s\) identical to the template volume\(s\) will be created.

For example, create two volumes as:

```text
vol-0743df7bf5657dad8: 1000 GiB provisioned IOPS
vol-0055e5913b79fb49d: 1000 GiB GP2
```

Ensure that these EBS volumes are created in the same region as the auto scaling group.

You will use the EBS volume Id \(e.g. _vol-04e2283f1925ec9ee_\) in the _Install Portworx with Kubernetes_ topic, which is discussed next.
{% endtab %}

{% tab title="Google Kubernetes Engine" %}
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

You will use your existing GCP disk's template in the _Install Portworx with Kubernetes_ topic, which is discussed next.
{% endtab %}
{% endtabs %}

## Install On-Premise with Kubernetes

{% tabs %}
{% tab title="Openshift" %}
Portworx supports Openshift 3.7 and above.

### Add Portworx service accounts to the privileged security context

```text
oc adm policy add-scc-to-user privileged system:serviceaccount:kube-system:px-account
oc adm policy add-scc-to-user privileged system:serviceaccount:kube-system:portworx-pvc-controller-account
oc adm policy add-scc-to-user anyuid system:serviceaccount:default:default
```

### Prepare a docker-registry credentials secret

Create a Red Hat account if you don't already have one \([register here](https://www.redhat.com/wapps/ugc/register.html)\).

Configure a [Kubernetes secret](https://kubernetes.io/docs/concepts/containers/images/#creating-a-secret-with-a-docker-config) with username/password credentials:

```text
# confirm the username/password works  (e.g. user:john-rhel, passwd:s3cret)
docker login -u john-rhel -p s3cret registry.connect.redhat.com
> Login Succeeded

# configure username/password as a kubernetes "docker-registry" secret  (e.g. "regcred")
oc create secret docker-registry regcred --docker-server=registry.connect.redhat.com \
  --docker-username=john-rhel --docker-password=s3cret --docker-email=test@acme.org \
  -n kube-system
```
{% endtab %}
{% endtabs %}

In the next topic, you will install Portworx with Kubernetes.







