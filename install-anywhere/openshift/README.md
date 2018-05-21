# OpenShift

To set up OpenShift to use Portworx, follow the steps below.

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

### Install Portworx with Kubernetes

After completing the previous steps, you are ready to install Portworx with Kubernetes.

Click on the link below to find the instructions for this install. As part of this install, you will refer to the _Spec Parameter Notes_ section that is directly underneath this section.

{% page-ref page="../../kubernetes-portworx-install/" %}

