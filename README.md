# Deploy SFTP (SHH File Transfer Protocol) On IBM Cloud Kubernetes Service

## Prerequisite

- Docker CLI
- IBM Cloud CLI
- Kubernetes CLI

### Step - 1 Build and Upload SFTP container image to IBM Cloud Container Registry

In step 1 we will pull an existing SFTP container image from docker hub, make few changes to it and upload it to IBM Cloud Container Registry (Private).

- First Login into your IBM Cloud account.
  '''
  ibmcloud login -a <region_endpoint>
  '''

- Set your organisation and space.
  '''
  ibmcloud target -o "<organisation_name>" -s "<space_name>"
  '''

![alt text](images/image1.png)

- Once you are logged into your IBM Cloud account, next step is to pull a base docker image for SFTP.
  '''
  docker pull fauria/vsftpd
  '''
  ![alt text](images/image2.png)

- Next step is to make changes to this base image and upload it IBM Cloud container registry (Private).

'''
ibmcloud plugin install container-registry -r Bluemix
'''
![alt text](images/image3.png)
'''
ibmcloud cr namespace-add <my_namespace>
'''
![alt text](images/image4.png)
'''
ibmcloud cr login
'''
![alt text](images/image5.png)
'''
docker tag fauria/vsftpd registry.eu-gb.bluemix.net/<my_namespace>/fauria/vsftpd:latest
'''
'''
docker push registry.eu-gb.bluemix.net/<my_namespace>/fauria/vsftpd:latest
'''
![alt text](images/image6.png)

- After executing the commands mentioned above, check weather the image is uploaded correctly or not by executing the command :-
  '''
  ibmcloud cr image-list
  '''
  ![alt text](images/image7.png)
