# Migrating to OCI

This page describes the procedure to migrate your current Portworx installation to use OCI/runc containers.

### Step 1: Get your current Portworx arguments {#step-1-get-your-current-portworx-arguments}

Use the following command to get arguments supplied to your current Portworx DaemonSet. We will use that to generate the new DaemonSet spec that uses OCI later in this guide.

```text
$ kubectl get ds/portworx -n kube-system -o jsonpath='{.spec.template.spec.containers[*].args}'

[-k etcd:http://etcd1.acme.net:2379,etcd:http://etcd2.acme.net:2379 -c cluster123 \
 -s /dev/sdb1 -s /dev/sdc -x kubernetes]
```

### Step 2: Get the Daemonset spec that uses OCI {#step-2-get-the-daemonset-spec-that-uses-oci}

While generating the spec using below procedure, specify the parameters from step 1.

To generate the spec file, head on to the below URLs for the PX release you wish to use.

* [1.4 Tech Preview](https://install.portworx.com/1.4/).
* [1.3 Stable](https://install.portworx.com/1.3/).
* [1.2 Stable](https://install.portworx.com/1.2/).

Alternately, you can use curl to generate the spec as described in [Generating Portworx Kubernetes spec using curl](https://docs.portworx.com/scheduler/kubernetes/px-k8s-spec-curl.html).

**Secure ETCD and Certificates**

If using secure etcd provide “https” in the URL and make sure all the certificates are in the _/etc/pwx/_ directory on each host which is bind mounted inside PX container.

**Using Secrets to Provision Certificates**

Instead of manually copying the certificates on all the nodes, it is recommended to use [Kubernetes Secrets to provide etcd certificates to Portworx](https://docs.portworx.com/scheduler/kubernetes/etcd-certs-using-secrets.html). This way, the certificates will be automatically available to new nodes joining the cluster.

**Installing behind the HTTP proxy**

During the installation Portworx may require access to the Internet, to fetch kernel headers if they are not available locally on the host system. If your cluster runs behind the HTTP proxy, you will need to expose _PX\_HTTP\_PROXY_ and/or _PX\_HTTPS\_PROXY_ environment variables to point to your HTTP proxy when starting the DaemonSet.

Use _e=PX\_HTTP\_PROXY=&lt;http-proxy&gt;,PX\_HTTPS\_PROXY=&lt;https-proxy&gt;_ query param when generating the DaemonSet spec.

### Step 3: Verify the generated spec for migration {#step-3-verify-the-generated-spec-for-migration}

In the generated spec file, make sure the image for the Portworx DaemonSet is portworx/oci-monitor:**&lt;your-current-version&gt;**.

This ensures that you simply migrate to Portworx using OCI and don’t end up upgrading to a new Portworx version.

If you have custom changes to the DaemonSet spec apart from the arguments, go ahead and make those too.

### Step 4: Apply and migrate {#step-4-apply-and-migrate}

Apply the spec using `kubectl apply -f <spec-file>`.

As the DaemonSet upgrade strategy in _RollingUpgrade_, each node will migrate to Portworx using OCI one by one.

To monitor the migration process: `kubectl rollout status ds/portworx -n kube-system`

To monitor the Portworx pods: `kubectl get pods -o wide -n kube-system -l name=portworx`

