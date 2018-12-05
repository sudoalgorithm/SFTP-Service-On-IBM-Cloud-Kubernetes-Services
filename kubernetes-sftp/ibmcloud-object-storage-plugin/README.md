# Helm chart to install the IBM Cloud Object Storage plug-in

IBM Cloud Object Storage plug-in is a Kubernetes Flex-Volume plugin that enables Kubernetes pods to access IBM Cloud Object Storage buckets from cluster.

## Before you begin
   - If you do not have one yet, [create a standard cluster](https://console.bluemix.net/docs/containers/cs_clusters.html#clusters_cli).
   - [Target your CLI](https://console.bluemix.net/docs/containers/cs_cli_install.html#cs_cli_configure) to the cluster where you want to install the IBM Cloud Object Storage plug-in.

## Installing the IBM Cloud Object Storage plug-in

Install the IBM Cloud Object Storage plug-in with a Helm chart to set up pre-defined storage classes for IBM Cloud Object Storage. You can use these storage classes to create a PVC to provision IBM Cloud Object Storage for your apps.

1. Follow the [instructions](https://console.bluemix.net/docs/containers/cs_integrations.html#helm) to install the Helm client on your local machine, install the Helm server (tiller) in your cluster, and add the IBM Cloud Helm chart repository to the cluster where you want to use the IBM Cloud Object Storage plug-in.

   **Important:** If you use Helm version 2.9 or higher, make sure that you installed tiller with a [service account](https://console.bluemix.net/docs/containers/cs_integrations.html#helm).

2. Add the IBM Cloud Helm repository `ibm` to your cluster.
   ```
   helm repo add ibm https://registry.bluemix.net/helm/ibm
   ```

3. Update the Helm repo to retrieve the latest version of all Helm charts in this repo.
   ```
   helm repo update
   ```

4. Install the IBM Cloud Object Storage Helm plug-in `ibmc`. The plug-in is used to automatically retrieve your cluster location and to set the API endpoint for your IBM Cloud Object Storage buckets in your storage classes.

   - Download the Helm chart and unpack the chart in your current directory.
     ```
     helm fetch --untar ibm/ibmcloud-object-storage-plugin
     ```
   - Install the Helm plug-in.
     ```
     helm plugin install ibmcloud-object-storage-plugin/helm-ibmc
     ```
     Example output:
     ```
     Installed plugin: ibmc
     ```

5. Verify that the `ibmc` plug-in is installed successfully.
   ```
   helm ibmc --help
   ```
   Example output:
   ```
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


6. **Optional:** Limit the IBM Cloud Object Storage plug-in to access only the Kubernetes secrets that hold your IBM Cloud Object Storage service credentials. By default, the plug-in is authorized to access all Kubernetes secrets in your cluster.
   * [Create your IBM Cloud Object Storage service instance](https://console.bluemix.net/docs/containers/cs_storage_cos.html#create_cos_service).
   * [Store your IBM Cloud Object Storage service credentials in a Kubernetes secret](https://console.bluemix.net/docs/containers/cs_storage_cos.html#create_cos_secret).
   * Navigate to the `templates` directory and list available files.
     ```
     cd ibmcloud-object-storage-plugin/templates && ls
     ```
   * Open the `provisioner-sa.yaml` file and look for the `ibmcloud-object-storage-secret-reader` ClusterRole definition.
   * Add the name of the secret that you created earlier to the list of secrets that the plug-in is authorized to access in the `resourceNames` section.
     ```
     kind: ClusterRole
     apiVersion: rbac.authorization.k8s.io/v1beta1
     metadata:
       name: ibmcloud-object-storage-secret-reader
     rules:
     - apiGroups: [""]
       resources: ["secrets"]
       resourceNames: ["<secret_name1>","<secret_name2>"]
       verbs: ["get"]
     ```
   * Save your changes.

7. Install the IBM Cloud Object Storage plug-in. When you install the plug-in, pre-defined storage classes are added to your cluster.

   **Install without a limitation to specific Kubernetes secrets:**
   ```
   helm ibmc install ibm/ibmcloud-object-storage-plugin --name ibmcloud-object-storage-plugin -f ibmcloud-object-storage-plugin/ibm/values.yaml
   ```
   **Install with a limitation to specific Kubernetes secrets as described in the previous step:**
   ```
   helm ibmc install ./ibmcloud-object-storage-plugin --name ibmcloud-object-storage-plugin -f ibmcloud-object-storage-plugin/ibm/values.yaml
   ```

   Example output:
   ```
   Installing the Helm chart
   DC: dal10  Chart: ibm/ibmcloud-object-storage-plugin
   NAME:   ibmcloud-object-storage-plugin
   LAST DEPLOYED: Mon Jul 30 13:12:59 2018
   NAMESPACE: default
   STATUS: DEPLOYED

   RESOURCES:
   ==> v1/Pod(related)
   NAME                                             READY  STATUS             RESTARTS  AGE
   ibmcloud-object-storage-driver-hzqp9             0/1    ContainerCreating  0         1s
   ibmcloud-object-storage-driver-jtdb9             0/1    ContainerCreating  0         1s
   ibmcloud-object-storage-driver-tl42l             0/1    ContainerCreating  0         1s
   ibmcloud-object-storage-plugin-7d87fbcbcc-wgsn8  0/1    ContainerCreating  0         1s

   ==> v1beta1/StorageClass
   NAME                                  PROVISIONER       AGE
   ibmc-s3fs-cold-cross-region           ibm.io/ibmc-s3fs  1s
   ibmc-s3fs-cold-regional               ibm.io/ibmc-s3fs  1s
   ibmc-s3fs-flex-cross-region           ibm.io/ibmc-s3fs  1s
   ibmc-s3fs-flex-perf-cross-region      ibm.io/ibmc-s3fs  1s
   ibmc-s3fs-flex-perf-regional          ibm.io/ibmc-s3fs  1s
   ibmc-s3fs-flex-regional               ibm.io/ibmc-s3fs  1s
   ibmc-s3fs-standard-cross-region       ibm.io/ibmc-s3fs  1s
   ibmc-s3fs-standard-perf-cross-region  ibm.io/ibmc-s3fs  1s
   ibmc-s3fs-standard-perf-regional      ibm.io/ibmc-s3fs  1s
   ibmc-s3fs-standard-regional           ibm.io/ibmc-s3fs  1s
   ibmc-s3fs-vault-cross-region          ibm.io/ibmc-s3fs  1s
   ibmc-s3fs-vault-regional              ibm.io/ibmc-s3fs  1s

   ==> v1/ServiceAccount
   NAME                            SECRETS  AGE
   ibmcloud-object-storage-driver  1        1s
   ibmcloud-object-storage-plugin  1        1s

   ==> v1beta1/ClusterRole
   NAME                                   AGE
   ibmcloud-object-storage-secret-reader  1s
   ibmcloud-object-storage-plugin         1s

   ==> v1beta1/ClusterRoleBinding
   NAME                                   AGE
   ibmcloud-object-storage-plugin         1s
   ibmcloud-object-storage-secret-reader  1s

   ==> v1beta1/DaemonSet
   NAME                            DESIRED  CURRENT  READY  UP-TO-DATE  AVAILABLE  NODE SELECTOR  AGE
   ibmcloud-object-storage-driver  3        3        0      3           0          <none>         1s

   ==> v1beta1/Deployment
   NAME                            DESIRED  CURRENT  UP-TO-DATE  AVAILABLE  AGE
   ibmcloud-object-storage-plugin  1        1        1           0          1s

   NOTES:
   Thank you for installing: ibmcloud-object-storage-plugin.   Your release is named: mewing-duck

   Please refer Chart README.md file for creating a sample PVC.
   Please refer Chart RELEASE.md to see the release details/fixes.
   ```

8. Verify that the IBM Cloud Object Storage plug-in is installed correctly.
   ```
   kubectl get pod -n kube-system -o wide | grep object
   ```
   Example output:
   ```
   ibmcloud-object-storage-driver-9n8g8                              1/1       Running   0          2m
   ibmcloud-object-storage-plugin-7c774d484b-pcnnx                   1/1       Running   0          2m
   ```
   The installation is successful when you see one `ibmcloud-object-storage-plugin` pod and one or more `ibmcloud-object-storage-driver` pods. The number of `ibmcloud-object-storage-driver` pods equals the number of worker nodes in your cluster. All pods must be in a `Running` state for the plug-in to function properly. If the pods fail, run `kubectl describe pod -n kube-system <pod_name>` to find the root cause for the failure.

9. Verify that the storage classes are created successfully.
   ```
   kubectl get storageclass | grep s3
   ```
   Example output:
   ```
   ibmc-s3fs-cold-cross-region            ibm.io/ibmc-s3fs   8m
   ibmc-s3fs-cold-regional                ibm.io/ibmc-s3fs   8m
   ibmc-s3fs-flex-cross-region            ibm.io/ibmc-s3fs   8m
   ibmc-s3fs-flex-perf-cross-region       ibm.io/ibmc-s3fs   8m
   ibmc-s3fs-flex-perf-regional           ibm.io/ibmc-s3fs   8m
   ibmc-s3fs-flex-regional                ibm.io/ibmc-s3fs   8m
   ibmc-s3fs-standard-cross-region        ibm.io/ibmc-s3fs   8m
   ibmc-s3fs-standard-perf-cross-region   ibm.io/ibmc-s3fs   8m
   ibmc-s3fs-standard-perf-regional       ibm.io/ibmc-s3fs   8m
   ibmc-s3fs-standard-regional            ibm.io/ibmc-s3fs   8m
   ibmc-s3fs-vault-cross-region           ibm.io/ibmc-s3fs   8m
   ibmc-s3fs-vault-regional               ibm.io/ibmc-s3fs   8m
   ```

10. Repeat the steps for all clusters where you want to access IBM Cloud Object Storage buckets.


## Updating the IBM Cloud Object Storage plug-in
You can upgrade the existing IBM Cloud Object Storage plug-in to the latest version.
1. Update the Helm repo to retrieve the latest version of all Helm charts in this repo.
   ```
   helm repo update
   ```

2. Download to latest Helm chart to your local machine and unzip the package to review the `release.md` file to find the latest release information.
   ```
   helm fetch --untar ibm/ibmcloud-object-storage-plugin
   ```

3. Find the installation name of your helm chart.
   ```
   helm ls | grep ibmcloud-object-storage-plugin
   ```
   Example output:
   ```
   <helm_chart_name>     1           Mon Sep 18 15:31:40 2017    DEPLOYED    ibmcloud-object-storage-plugin-1.0.0    default
   ```

4. Upgrade the IBM Cloud Object Storage plug-in to the latest version.
   ```
   helm ibmc upgrade <helm_chart_name> ibm/ibmcloud-object-storage-plugin --force --recreate-pods -f ./ibmcloud-object-storage-plugin/ibm/values.yaml
   ```

5. Verify that the `ibmcloud-object-storage-plugin` is successfully upgraded.
   ```
   kubectl rollout status deployment/ibmcloud-object-storage-plugin -n kube-system
   ```
   The upgrade of the plug-in is successful when you see `deployment "ibmcloud-object-storage-plugin" successfully rolled out` in your CLI output.

6. Verify that the `ibmcloud-object-storage-driver` is successfully upgraded.
   ```
   kubectl rollout status ds/ibmcloud-object-storage-driver -n kube-system
   ```
   The upgrade is successful when you see `daemon set "ibmcloud-object-storage-driver" successfully rolled out` in your CLI output.

7. Verify that the IBM Cloud Object Storage pods are in a `Running` state.
   ```
   kubectl get pods -n kube-system -o wide | grep object-storage
   ```

## Removing the IBM Cloud Object Storage plug-in
If you do not want to provision and use IBM Cloud Object Storage in your cluster, you can uninstall the helm charts.

**Note:** Removing the plug-in does not remove existing PVCs, PVs, or data. When you remove the plug-in, all the related pods and daemon sets are removed from your cluster. You cannot provision new IBM Cloud Object Storage for your cluster or use existing PVCs and PVs after you remove the plug-in, unless you configure your app to use the IBM Cloud Object Storage API directly.

Before you begin:
   - [Target your CLI to the cluster](https://console.bluemix.net/docs/containers/cs_cli_install.html#cs_cli_configure).
   - Make sure that you do not have any PVCs or PVs in your cluster that use IBM Cloud Object Storage. To list all pods that mount a specific PVC, run

     `kubectl get pods --all-namespaces -o=jsonpath='{range .items[*]}{"\n"}{.metadata.name}{":\t"}{range .spec.volumes[*]}{.persistentVolumeClaim.claimName}{" "}{end}{end}' | grep "<pvc_name>"`

To remove the plug-in:

1. Find the installation name of your helm chart.
   ```
   helm ls | grep ibmcloud-object-storage-plugin
   ```
   Example output:
   ```
   <helm_chart_name>     1           Mon Sep 18 15:31:40 2017    DEPLOYED    ibmcloud-object-storage-plugin-1.0.0    default
   ```

2. Delete the IBM Cloud Object Storage plug-in by removing the Helm chart.
   ```
   helm delete --purge <helm_chart_name>
   ```

3. Verify that the IBM Cloud Object Storage pods are removed.
   ```
   kubectl get pod -n kube-system | grep object-storage
   ```
   The removal of the pods is successful if no pods are displayed in your CLI output.

4. Verify that the storage classes are removed.
   ```
   kubectl get storageclasses | grep s3
   ```
   The removal of the storage classes is successful if no storage classes are displayed in your CLI output.

5. Remove the `ibmc` Helm plug-in.
   ```
   rm -rf ~/.helm/plugins/helm-ibmc
   ```

6. Verify that the `ibmc` plug-in is removed.
   ```
   helm plugin list
   ```
   Example output:
   ```
   NAME    VERSION    DESCRIPTION
   ```
   The ibmc `plug-in` is removed successfully if the `ibmc` plug-in is not listed in your CLI output.

## Further reading

For details, refer to [storing data on IBM Cloud Object Storage](https://console.bluemix.net/docs/containers/cs_storage_cos.html#object_storage).
