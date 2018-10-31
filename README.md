# Deploy SFTP (SSH File Transfer Protocol) Service On IBM Cloud Kubernetes Service

## Prerequisite

- [IBM Cloud CLI](https://console.bluemix.net/docs/cli/reference/bluemix_cli/get_started.html#getting-started)
- [Docker CLI](https://docs.docker.com/install/)
- **IBM Cloud Container Registry Plugin**

  ```
  ibmcloud plugin install container-registry -r Bluemix
  ```
### Step 1:- Build and Upload SFTP container image to IBM Cloud Container Registry

In step 1 we will pull an existing SFTP container image from docker hub, make few changes to it and upload it to IBM Cloud Container Registry (Private).

- First Login into your IBM Cloud account.For Example 'ibmcloud login -a https://api.eu-gb.bluemix.net' for UK region
  ```
  ibmcloud login -a <region_endpoint>
  ```

- Set your organisation and space. You can get this from your Bluemix dashboard
  ```
  ibmcloud target -o "<organisation_name>" -s "<space_name>"
  ```
  ![alt text](images/image1.png)

- Target the IBM Cloud Container Service region in which you want to work. You can get this from your Bluemix dashboard. For Example ibmcloud cs region-set uk-south
  ```
  ibmcloud cs region-set <service_region>
  ```
  
- Once you are successfully logged into your IBM Cloud account, pull a base docker image of SFTP from **Docker Hub**. Note that you need to have a **Docker Client** running otherwise the command will return an error).

  ![alt text](images/image9.png)  

  ```
  docker pull fauria/vsftpd
  ```

  ![alt text](images/image3.png)

- Once pull is complete, next we need to make changes to this base image and upload it IBM Cloud container registry (Private).

- Note:- Execute each command mentioned below step by step

  ```
  ibmcloud cr namespace-add <my_namespace>
  ```

  ![alt text](images/image2.png)

  ```
  ibmcloud cr login
  ```

  ![alt text](images/image4.png)

  ```
  docker tag fauria/vsftpd registry.eu-gb.bluemix.net/<my_namespace>/fauria/vsftpd:latest
  ```
    ![alt text](images/image5.png)

  ```
  docker push registry.eu-gb.bluemix.net/<my_namespace>/fauria/vsftpd:latest
  ```

  ![alt text](images/image6.png)

- After executing the commands mentioned above, check weather the image is uploaded correctly either by executing the command mentioned below or by going to IBM Cloud Container Registry dashboard
  - **Command**
  ```
  ibmcloud cr image-list
  ```
  ![alt text](images/image7.png)
  
  - **IBM Cloud Container Registry Dashboard**

  ![alt text](images/image8.png)

### Step 2:- Deploy the SFTP Service To IBM Cloud Kubernetes Service And Create Persistant Volume To Store the Data.

#### Create A Kubernetes Cluster

- Go to your **IBM Cloud Dashboard** and [Sign in](https://console.bluemix.net/dashboard/apps/)
- Go to **IBM Kubernetes Service**

![alt text](images/image10.png)

- Click on **Create Cluster**

![alt text](images/image11.png)

- Select the **region** where you want to deploy the cluster, give a **name** to your cluster and click on **create cluster**.
- Depending upon your account (**Paid or Free**), select the appropriate cluster type.
- It takes some time for cluster to get ready (around 30 mins).

![alt text](images/image12.png)

- Once the cluster is ready, click on your cluster name and you will be redirected to a new page containing information regarding your cluster and worker node.

![alt text](images/image13.png)

- Click on worker node tab, to get cluster's **Public IP**.

![alt text](images/image14.png)

- After the cluster is deployed successfully, go back to github and clone or download the repository.

- Once the repository is on your local system switch to inside the main directory **kubernetes-sftp directory**.Inside the directory you will find 5 file required to deploy the SFTP service container on to kuberentes and create a persistant volume.

- Execute each command mentioned below step by step

```
kubectl create -f task-pv-volume.yaml
```

```
kubectl get pv task-pv-volume
```

```
kubectl create -f task-pv-claim.yaml
```

```
kubectl get pvc task-pv-claim
```

```
kubectl create -f sftp-deployment.yaml
```

```
kubectl create -f sftp-service.yaml
```

```
kubectl create -f sftp-ingress.yaml
```
