## helm-ibmc

A Helm plugin that installs or upgrades Helm charts in IBM K8S Service

* https://docs.helm.sh/helm/#helm-ibmc

## Installation
  * `helm repo add ibm https://registry.bluemix.net/helm/ibm`
  * `helm repo update`
  * `helm fetch --untar ibm/ibmcloud-object-storage-plugin`
  * `helm plugin install ./ibmcloud-object-storage-plugin/helm-ibmc`
  * `helm ibmc --help`

## Upgrade
  * `helm ibmc --update`

## Usage

### Install IBM Cloud Object Storage plug-in chart
  * `helm ibmc install <chart repo>/<chart name> [flags]`
  * `helm ibmc install ibm/ibmcloud-object-storage-plugin -f ./ibmcloud-object-storage-plugin/ibm/values.yaml`

### Example
```
Mayank-Macbooks-MacBook-Pro:~ mayank-macbook$ helm plugin install ./ibmcloud-object-storage-plugin/helm-ibmc
Installed plugin: ibmc
Mayank-Macbooks-MacBook-Pro:~ mayank-macbook$ helm ibmc --help
Install or upgrade Helm charts in IBM K8S Service

Available Commands:
    helm ibmc install [CHART] [flags]              Install a Helm chart
    helm ibmc upgrade [RELEASE] [CHART] [flags]    Upgrades the release to a new version of the Helm chart

Available Flags:
    --verbos                      (Optional) Verbosity intensifies... ...
    -f, --values valueFiles       (Optional) specify values in a YAML file (can specify multiple) (default [])
    -h, --help                    (Optional) This text.
    -u, --update                  (Optional) Update this plugin to the latest version

Example Usage:
    helm ibmc install ibm/ibmcloud-object-storage-plugin -f ./ibmcloud-object-storage-plugin/ibm/values.yaml
```

```
Mayank-Macbooks-MacBook-Pro:~ mayank-macbook$ helm ibmc install ibm/ibmcloud-object-storage-plugin --verbos -f ./ibmcloud-object-storage-plugin/ibm/values.yaml
PARAMS
-f ./ibmcloud-object-storage-plugin/ibm/values.yaml
 .........................
Helm kubeconfig = /Users/mayank-macbook/.bluemix/plugins/container-service/clusters/verify-s3-plugin/kube-config-dal10-verify-s3-plugin.yml
Helm home = /Users/mayank-macbook/.helm
Helm plugin = /Users/mayank-macbook/.helm/plugins
Helm plugin dir = /Users/mayank-macbook/.helm/plugins/helm-ibmc
PASSTHRU = install ibm/ibmcloud-object-storage-plugin
FLAGS = -f ./ibmcloud-object-storage-plugin/ibm/values.yaml
Installing the Helm chart
DC: dal10  Chart: ibm/ibmcloud-object-storage-plugin
NAME:   factual-bronco
LAST DEPLOYED: Tue Jul 31 18:15:07 2018
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1beta1/DaemonSet
NAME                            DESIRED  CURRENT  READY  UP-TO-DATE  AVAILABLE  NODE SELECTOR  AGE
ibmcloud-object-storage-driver  2        2        2      2           2          <none>         2s

==> v1beta1/Deployment
NAME                            DESIRED  CURRENT  UP-TO-DATE  AVAILABLE  AGE
ibmcloud-object-storage-plugin  1        1        1           1          2s

==> v1/Pod(related)
NAME                                             READY  STATUS   RESTARTS  AGE
ibmcloud-object-storage-driver-tkrjn             1/1    Running  0         2s
ibmcloud-object-storage-driver-wcps6             1/1    Running  0         2s
ibmcloud-object-storage-plugin-5bc4d6d584-wtr79  1/1    Running  0         2s

==> v1beta1/StorageClass
NAME                                  PROVISIONER       AGE
ibmc-s3fs-cold-cross-region           ibm.io/ibmc-s3fs  2s
ibmc-s3fs-cold-regional               ibm.io/ibmc-s3fs  2s
ibmc-s3fs-flex-cross-region           ibm.io/ibmc-s3fs  2s
ibmc-s3fs-flex-perf-cross-region      ibm.io/ibmc-s3fs  2s
ibmc-s3fs-flex-perf-regional          ibm.io/ibmc-s3fs  2s
ibmc-s3fs-flex-regional               ibm.io/ibmc-s3fs  2s
ibmc-s3fs-standard-cross-region       ibm.io/ibmc-s3fs  2s
ibmc-s3fs-standard-perf-cross-region  ibm.io/ibmc-s3fs  2s
ibmc-s3fs-standard-perf-regional      ibm.io/ibmc-s3fs  2s
ibmc-s3fs-standard-regional           ibm.io/ibmc-s3fs  2s
ibmc-s3fs-vault-cross-region          ibm.io/ibmc-s3fs  2s
ibmc-s3fs-vault-regional              ibm.io/ibmc-s3fs  2s

==> v1/ServiceAccount
NAME                            SECRETS  AGE
ibmcloud-object-storage-driver  1        2s
ibmcloud-object-storage-plugin  1        2s

==> v1beta1/ClusterRole
NAME                                   AGE
ibmcloud-object-storage-secret-reader  2s
ibmcloud-object-storage-plugin         2s

==> v1beta1/ClusterRoleBinding
NAME                                   AGE
ibmcloud-object-storage-plugin         2s
ibmcloud-object-storage-secret-reader  2s

NOTES:
Thank you for installing: ibmcloud-object-storage-plugin.   Your release is named: factual-bronco

Please refer Chart README.md file for creating a sample PVC.
Please refer Chart RELEASE.md to see the release details/fixes.
```
