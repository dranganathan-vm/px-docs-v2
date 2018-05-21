# Lighthouse On-Premise

To get access to Portworx PX-Lighthouse docker repository, contact us at support@portworx.com from your company email address.

### Lighthouse 1.1.11 Release notes {#lighthouse-1111-release-notes}

* Monitoring-only version of lighthouse with create/delete/snap/node decommissioning disabled

### Lighthouse 1.1.10 Release notes {#lighthouse-1110-release-notes}

#### Key Fixes {#key-fixes}

* Fixed an issue where with Portworx 1.2.11, all volumes were showing as attached in UI

### Lighthouse 1.1.9 Release notes {#lighthouse-119-release-notes}

#### Key Fixes {#key-fixes-1}

* Fixed an issue where deleting user group was crashing UI

### Lighthouse 1.1.8 Release notes {#lighthouse-118-release-notes}

* Added in support for the latest Openstorage api’s and made Lighthouse work with these.
* Added creation of the influx px\_stats database if not present.
* Added kubernetes commands for spinning up a px node/cluster
* Added feature where the user can specify admin email and company.
* Added feature where the user can specify which port Lighthouse would be running on.

#### Key Fixes {#key-fixes-2}

* Fixed an issue where volumes would always show as attached
* Fixed an issue where in different timezones last communication date would show 2017 years ago.

### Lighthouse 1.1.6 Release notes {#lighthouse-116-release-notes}

* Updated dashboard with better representation of different IO Priority pools
* Dashboard updates for node status and cluster map
* Improved performance charting in dashboard and performance page
* Shortcuts to performance page from node and volume pages
* Added alerting fade-aways to give better feedback for user actions
* Faster response time for page transitions

### Lighthouse 1.1.3 Release notes {#lighthouse-113-release-notes}

In order to use Lighthouse 1.1.3 with authentication, please download and install PX-Enterprise 1.1.2 as well.

#### Key Features {#key-features}

* Support for authenticated etcd2. Refer to [Lighthouse with Secure etcd](https://docs.portworx.com/enterprise/lighthouse.html)

#### Known issues {#known-issues}

* This version does not support clear alerts. This will be fixed in the upcoming release along with the addition of “Clear All Alerts” capability
* Email server setup with TLS does not work with certificates signed by private CA
* Lighthouse container requires restart, if it has lost connectivity to etcd2

### Lighthouse 1.1.2 Release notes {#lighthouse-112-release-notes}

#### Key Fixes {#key-fixes-3}

* Handle kvdb access failures and show banner alerts to indicate access issues
* Handle influxDB access timeouts and show banner alerts to indicate access issues
* Improve feedback on Consul and influxDB container restarts
* Fix panics around influx or consul being down and when customer does first login after initial setup
* Fix inability to create new clusters after a node is added to a cluster

#### Known issues {#known-issues-1}

* This version does not support clear alerts. This will be fixed in the upcoming 1.1.3 release along with the addition of “Clear All Alerts” capability.

