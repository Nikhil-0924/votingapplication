# votingapplication
This repo is based on the EKS, MongoDB, API, Security and Secrets Management, PersiKubernetes ResourcesstentVolume and PersistentVolumeClaim

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

**Persistent Storage:** Understand how Kubernetes manages and provisions persistent storage for applications with state.
By working through this project, you'll develop a deeper understanding of cloud-native application development, containerization, Kubernetes, and the various technologies involved in building and deploying modern web applications.
Steps to Deploy

