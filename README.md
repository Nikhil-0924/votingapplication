# votingapplication
This repo is based on the EKS, MongoDB, API, Security and Secrets Management, Kubernetes ResourcesstentVolume and PersistentVolumeClaim

**Frontend:**
The frontend of this application is built using React and JavaScript. It provides a responsive and user-friendly interface for casting votes.

**Backend and API:** 
The backend of this application is powered by Go (Golang). It serves as the API handling user voting requests. MongoDB is used as the database backend, 
configured with a replica set for data redundancy and high availability.

**Kubernetes Resources**
----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

**Namespace** : Kubernetes namespaces are utilized to create isolated environments for different components of the application, ensuring separation and organization.

**Secret**    : Kubernetes secrets store sensitive information, such as API keys or credentials, required by the application securely.

**Deployment** : Kubernetes deployments define how many instances of the application should run and provide instructions for updates and scaling.

**Service**: Kubernetes services ensure that users can access the application by directing incoming traffic to the appropriate instances.

**StatefulSet**: For components requiring statefulness, such as the MongoDB replica set, Kubernetes StatefulSets are employed to maintain order and unique identities.

**PersistentVolume and PersistentVolumeClaim**: These Kubernetes resources manage the storage required for the application, ensuring data persistence and scalability.

****Creating and deploying this cloud-native web voting application with Kubernetes offers a valuable learning experience. Here are some key takeaways:**
----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

**Containerization:** Gain hands-on experience with containerization technologies like Docker for packaging applications and their dependencies.

**Kubernetes Orchestration:** Learn how to leverage Kubernetes to efficiently manage, deploy, and scale containerized applications in a production environment.

**Microservices Architecture:** Explore the benefits and challenges of a microservices architecture, where the frontend and backend are decoupled and independently scalable.

**Database Replication:** Understand how to set up and manage a MongoDB replica set for data redundancy and high availability.

**Security and Secrets Management:** Learn best practices for securing sensitive information using Kubernetes secrets.

**Stateful Applications:** Gain insights into the nuances of deploying stateful applications within a container orchestration environment.

**Stateful : Key Features of StatefulSets**
 
1.	Stable, unique network identities:Pods in a StatefulSet get a persistent identity that does not change across rescheduling or scaling.
2.	Persistent storage: StatefulSets work with PersistentVolumeClaims (PVCs) to provide stable storage across pod restarts.
3.	Ordered deployment and scaling: Pods are created, updated, and deleted in a sequential order.
4.	Ordered, graceful termination: Pods are deleted in reverse order, from the highest to the lowest ordinal.
    ***(MySQL, PostgreSQL, MongoDB)

5.Pods: Each pod in a StatefulSet has a unique identifier and hostname, and it is associated with a PersistentVolume.
6.Headless Service: Unlike a regular service, a Headless Service is used for service discovery in StatefulSets. It provides direct access to pods without load balancing.
7.PersistentVolumes (PVs) and PersistentVolumeClaims (PVCs): Each pod in a StatefulSet can have its own persistent storage that is preserved across restarts.

**Persistent Storage:** Understand how Kubernetes manages and provisions persistent storage for applications with state.
By working through this project, you'll develop a deeper understanding of cloud-native application development, containerization, Kubernetes, and the various technologies involved in building and deploying modern web applications.
Steps to Deploy

**AWS RESOUCRES**
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
1.EKS (cluster-1)
2.Role (MyAamzonEKSclusterRole)
   
   {
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "Service": [
                    "eks.amazonaws.com"
                ]
            },
            "Action": "sts:AssumeRole"
        }
    ]
}

  3.ADD-ONS in cluster
  -> **Amazon EBS CSI Driver**
  
  4.Compute in clusters 
  -> **Nodes for Workers**
    
     1.**Roles**

    -->AmazonEBSCSIDriverPolicy
    -->AmazonEC2ContainerRegistryReadOnly
    -->AmazonEKS_CNI_Policy
    -->AmazonEKSWorkerNodePolicy
    
    **T2.medium**
  

  5.EC2 Instances 
  
  -->**coustmerMangaedRole**
   [polices.txt](https://github.com/user-attachments/files/17389752/polices.txt)

--->**EBSRole(AmazonEBSCSIDriverPolicy)**

**Install Kubectl:**
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
  ```bash
curl -O https://s3.us-west-2.amazonaws.com/amazon-eks/1.24.11/2023-03-17/bin/linux/amd64/kubectl
chmod +x ./kubectl
sudo cp ./kubectl /usr/local/bin
export PATH=/usr/local/bin:$PATH
```


**Install AWScli:**
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
```bash
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
```

**Once the Cluster is ready run the command to set context:**
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
```bash
aws eks update-kubeconfig --name EKS_CLUSTER_NAME --region us-west-2
```

To check the nodes in your cluster run
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
```bash
kubectl get nodes
```
If using EC2 and getting the "You must be logged in to the server (Unauthorized)" error, refer this:
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
https://repost.aws/knowledge-center/eks-api-server-unauthorized-error

1.AWS configure

2.Config Map 
```bash
kubectl edit configmap aws-auth -n kube-system
```
Create CloudChamp Namespace
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
This is command is used to check the namesapces
```bash
kubectl get ns
```
This is command is used to create the namesapces
```bash
kubectl create ns <name>
```
```bash
kubectl get all -n cloudchmap
```
This Command is used to check PersistentVolumes (PVs)
```bash
kubectl get pv -n cloudchmap
```
```bash
 kubectl get pods -n <nameSpace>
```
```bash
 kubectl get pods -n <namespace> -w
```
```bash
 kubectl get pv -n <namespace>
```
```bash
 kubectl get pvc -n <namespace>
```

**MONGO Database Setup**
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

To create Mongo statefulset with Persistent volumes, run the command in manifests folder:

```bash
kubectl apply -f mongo-statefulset.yaml
```
Mongo Service
```bash
kubectl apply -f mongo-service.yaml
```
way to set the current context's default namespace

```bash
kubectl config set-context --current --namespace=<name>
```
Command to check inside the Database
```bash
kubectl exec -it mongo-0 -- mongo
```
On the mongo-0 pod, initialise the Mongo database Replica set. In the terminal run the following command:
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
```bash
cat << EOF | kubectl exec -it mongo-0 -- mongo
rs.initiate();
sleep(2000);
rs.add("mongo-1.mongo:27017");
sleep(2000);
rs.add("mongo-2.mongo:27017");
sleep(2000);
cfg = rs.conf();
cfg.members[0].host = "mongo-0.mongo:27017";
rs.reconfig(cfg, {force: true});
sleep(5000);
EOF
```
Load the Data in the database by running this command:
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
```bash
cat << EOF | kubectl exec -it mongo-0 -- mongo
use langdb;
db.languages.insert({"name" : "csharp", "codedetail" : { "usecase" : "system, web, server-side", "rank" : 5, "compiled" : false, "homepage" : "https://dotnet.microsoft.com/learn/csharp", "download" : "https://dotnet.microsoft.com/download/", "votes" : 0}});
db.languages.insert({"name" : "python", "codedetail" : { "usecase" : "system, web, server-side", "rank" : 3, "script" : false, "homepage" : "https://www.python.org/", "download" : "https://www.python.org/downloads/", "votes" : 0}});
db.languages.insert({"name" : "javascript", "codedetail" : { "usecase" : "web, client-side", "rank" : 7, "script" : false, "homepage" : "https://en.wikipedia.org/wiki/JavaScript", "download" : "n/a", "votes" : 0}});
db.languages.insert({"name" : "go", "codedetail" : { "usecase" : "system, web, server-side", "rank" : 12, "compiled" : true, "homepage" : "https://golang.org", "download" : "https://golang.org/dl/", "votes" : 0}});
db.languages.insert({"name" : "java", "codedetail" : { "usecase" : "system, web, server-side", "rank" : 1, "compiled" : true, "homepage" : "https://www.java.com/en/", "download" : "https://www.java.com/en/download/", "votes" : 0}});
db.languages.insert({"name" : "nodejs", "codedetail" : { "usecase" : "system, web, server-side", "rank" : 20, "script" : false, "homepage" : "https://nodejs.org/en/", "download" : "https://nodejs.org/en/download/", "votes" : 0}});
EOF

**MongoDB commands**
```bash 
rs.status()
```
```bash
use langbd
```
```bash
db.languages.find()
```
```bash
db.languages.find().pretty();
```

```
Create Mongo secret:
```bash
kubectl apply -f mongo-secret.yaml
```
API Setup

Create GO API deployment by running the following command:
```bash
kubectl apply -f api-deployment.yaml
```
Expose API deployment through service using the following command:
```bash
kubectl expose deploy api \  --name=api \ --type=LoadBalancer \ --port=80 \ --target-port=8080
```
Next set the environment variable:

```bash
{
API_ELB_PUBLIC_FQDN=$(kubectl get svc api -ojsonpath="{.status.loadBalancer.ingress[0].hostname}")
until nslookup $API_ELB_PUBLIC_FQDN >/dev/null 2>&1; do sleep 2 && echo waiting for DNS to propagate...; done
curl $API_ELB_PUBLIC_FQDN/ok
echo
}
```
Test and confirm that the API route URL /languages, and /languages/{name} endpoints can be called successfully. In the terminal run any of the following commands:
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
```bash
curl -s $API_ELB_PUBLIC_FQDN/languages | jq .
curl -s $API_ELB_PUBLIC_FQDN/languages/go | jq .
curl -s $API_ELB_PUBLIC_FQDN/languages/java | jq .
curl -s $API_ELB_PUBLIC_FQDN/languages/nodejs | jq .

If everything works fine, go ahead with Frontend setup.

{
API_ELB_PUBLIC_FQDN=$(kubectl get svc api -ojsonpath="{.status.loadBalancer.ingress[0].hostname}")
echo API_ELB_PUBLIC_FQDN=$API_ELB_PUBLIC_FQDN
}
Frontend setup
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Create the Frontend Deployment resource. In the terminal run the following command:
```bash
kubectl apply -f frontend-deployment.yaml
```
Create a new Service resource of LoadBalancer type. In the terminal run the following command:
```bash
kubectl expose deploy frontend \
 --name=frontend \
 --type=LoadBalancer \
 --port=80 \
 --target-port=8080
```
 
Confirm that the Frontend ELB is ready to recieve HTTP traffic. In the terminal run the following command:
```bash
{
FRONTEND_ELB_PUBLIC_FQDN=$(kubectl get svc frontend -ojsonpath="{.status.loadBalancer.ingress[0].hostname}")
until nslookup $FRONTEND_ELB_PUBLIC_FQDN >/dev/null 2>&1; do sleep 2 && echo waiting for DNS to propagate...; done
curl -I $FRONTEND_ELB_PUBLIC_FQDN
}
```
Generate the Frontend URL for browsing. In the terminal run the following command:
```bash
echo http://$FRONTEND_ELB_PUBLIC_FQDN
```

Test the full end-to-end cloud native application

Using your local workstation's browser - browse to the URL created in the previous output.

After the voting application has loaded successfully, vote by clicking on several of the +1 buttons, this will generate AJAX traffic which will be sent back to the API via the API's assigned ELB.

Query the MongoDB database directly to observe the updated vote data. In the terminal execute the following command:
```bash
kubectl exec -it mongo-0 -- mongo langdb --eval "db.languages.find().pretty()"
```
