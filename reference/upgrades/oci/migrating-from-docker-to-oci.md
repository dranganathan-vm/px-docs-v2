# Migrating from Docker to OCI



### Step 1: Get your current Portworx arguments {#step-1-get-your-current-portworx-arguments}

Use the following command to get arguments supplied to your current Portworx DaemonSet. We will use that to generate the new DaemonSet spec that uses OCI later in this guide.

```text
$ kubectl get ds/portworx -n kube-system -o jsonpath='{.spec.template.spec.containers[*].args}'

[-k etcd:http://etcd1.acme.net:2379,etcd:http://etcd2.acme.net:2379 -c cluster123 \
 -s /dev/sdb1 -s /dev/sdc -x kubernetes]
```

