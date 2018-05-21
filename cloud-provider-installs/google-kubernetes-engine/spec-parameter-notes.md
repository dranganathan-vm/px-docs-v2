# Spec Parameter Notes

You should read this section after reading _1b. Parameter Notes_, located under _Portworx Install with Kubernetes_.

When you create the DaemonSet spec to deploy Portworx using the Google Kubernetes Engine, follow the instruction\(s\) below.

{% hint style="info" %}
You must specify the disk template \(that you previously generated\) in the DaemonSet spec file. If generating the spec via the GUI wizard, specify the disk template in the **List of drives** field. If generating the spec using the command line, specify the disk template using the `s` parameter.
{% endhint %}

