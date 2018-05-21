# Create Cloud Credentials

#### Click on Manage Credentials {#click-on-manage-credentials}

In the snapshots tab , click on the cloud icon on the right side pane.

![Lighthouse snapshot tab](https://docs.portworx.com/images/lighthouse-new-manage-credentials-1.png)

#### Create New Cloud Credentials {#create-new-cloud-credentials}

Click `New` button on the Manage Credentials page. Three cloud providers are supported \[Azure, GCP, Amazon S3\]. Newly created credentials will be validated before creation.

`Note` You can create multiple credentials with the same set of details, each credential will generate a unique UUID.

![Lighthouse new credentials](https://docs.portworx.com/images/lighthouse-new-create-new-credentials-1.png)

#### Azure Credentials {#azure-credentials}

Provide the `Account Name` and `Account Key` to create credentals.

![Lighthouse group snapshot](https://docs.portworx.com/images/lighthouse-new-azure-credentials.png)

#### S3 Credentials {#s3-credentials}

Provide the `Access Key` , `Secret Key` , `Region` and `Endpoint`to create S3 credentials.

`Note` You can only create credentials using only one Region per cluster. `Endpoint` value is of type `s3.<region-name>.amazonaws.com`.

![Lighthouse group snapshot](https://docs.portworx.com/images/lighthouse-new-s3-credentials.png)

#### Google Credentials {#google-credentials}

Provide the `Project ID` and `JSON Key file` to create credentals.

![Lighthouse group snapshot](https://docs.portworx.com/images/lighthouse-new-google-credentials.png)

