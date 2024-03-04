# API

K8s API is a way to work with k8s cluster. 

K8s works in a way that it has some **[objects](https://kubernetes.io/docs/concepts/overview/working-with-objects/)**, which describe what user wants from a k8s cluster. These objects have their controllers, which implement the logic of each object.

E.g. Pod object reflects the user need of running a certain containerized app. The Pod Controller ensures that a pod with defined app is running, and when it crashes it reinstantiates it again.

List of all possible object types (so called - **kinds**) and their business purpose is here:

Workloads
- Pod: The smallest deployable units that can be created, scheduled, and managed.
- ReplicaSet: Ensures a specified number of pod replicas are running at any given time.
- Deployment: Provides declarative updates for Pods and ReplicaSets.
- StatefulSet: Manages the deployment and scaling of a set of Pods, and provides guarantees about the ordering and uniqueness of these Pods.
- DaemonSet: Ensures that all (or some) Nodes run a copy of a Pod.
- Job: Creates one or more Pods and ensures that a specified number of them successfully terminate.
- CronJob: Manages time-based Jobs, such as running a job periodically on a given schedule.

Configuration and Storage
- ConfigMap: Stores non-confidential data in key-value pairs. Can be consumed by pods or used to store configuration files.
- Secret: Stores sensitive information, such as passwords, OAuth tokens, and ssh keys.
- Volume: A directory containing data, accessible to the containers in a pod.
- PersistentVolume (PV): A piece of storage in the cluster that has been provisioned by an administrator or dynamically provisioned using Storage Classes.
- PersistentVolumeClaim (PVC): A request for storage by a user.

Networking
- Service: An abstract way to expose an application running on a set of Pods as a network service.
- Ingress: Manages external access to the services in a cluster, typically HTTP.
- NetworkPolicy: Specifies how groups of pods are allowed to communicate with each other and with other network endpoints.

Cluster-Level Resources
- Node: A worker machine in Kubernetes.
- Namespace: Supports multiple virtual clusters backed by the same physical cluster.
- ResourceQuota: Provides constraints that limit aggregate resource consumption per namespace.
- LimitRange: Imposes constraints on the size of objects or on the amount of compute resources that can be requested in a namespace.

Metadata
- Label: Key/value pairs that are attached to objects, such as pods, for the purpose of identification and organization.
- Annotation: A key-value pair associated with a Kubernetes object used to store additional, non-identifiable information.

Access Control and Policy
- Role: Sets permissions within a namespace.
- ClusterRole: Sets permissions at the cluster-level.
- RoleBinding: Grants the permissions defined in a role to a user or set of users.
- ClusterRoleBinding: Grants the permissions defined in a ClusterRole to a user or set of users at the cluster level.

To Create,Read,Update,Delete (CRUD) objects of these kinds you will need to use cluster api which is exposed by the kube-api-server.

E.g. when you want to create new pod create HTTP call as such:
```http
POST http://<kube-apiserver-address>/api/v1/namespaces/default/pods
<pod yaml definiton>
```
As you can see the URI is divided into several parts:
- **http://**<kube-apiserver-address>/api/v1/namespaces/default/pods
    - used protocol
- http://<**kube-apiserver-address**>/api/v1/namespaces/default/pods 
    - IP address of a host with kube-api-server
- http://<kube-apiserver-address>/**api**/v1/namespaces/default/pods
    - group
- http://<kube-apiserver-address>/api/**v1**/namespaces/default/pods
    - version
- http://<kube-apiserver-address>/api/v1/**namespaces/default**/pods
    - part related to the namespaces, not important here
- http://<kube-apiserver-address>/api/v1/namespaces/default/**pods**
    - kind

## Group
When you are designing an api and want to group some of its part because of related functionality, you create a group. In here, a group is a set of related Kinds. 
## Version 
When you are designing an api and want to change it over time you need to introduce versioning, so the client is aware of it and experience only expected behavior. In here, what you are versioning is a group.
## Kind
As said before.

## Groups in Kubernetes
In Kubernetes, API groups organize Kubernetes resources and operations into a set of related functionalities. Each API group is specified in the API URL path used to access its resources. API groups are a way to extend Kubernetes with new features and custom resources. There are several built-in API groups in Kubernetes, each serving a specific purpose:

**Core Group (often referred to as the legacy group)**: This is the group for core Kubernetes objects and does not have a named group in its API path. It includes common types like Pods, Services, and ConfigMaps. The path for resources in this group is /api/v1.

**Named Groups:** These groups have a specific name and are used for specific functionalities. They have a path prefix of /apis/<group_name>/<version>. Some of the named groups include:

- apps: Includes resources related to deploying applications, such as Deployments, StatefulSets, and DaemonSets.
- batch: Contains job-related resources, including Job and CronJob objects.
- extensions: Initially used for experimental features. While many resources initially in this group have been moved to more specific groups, it historically included Deployments, DaemonSets, etc.
- networking.k8s.io: Manages networking aspects of the cluster, including Network Policies and Ingresses.
- rbac.authorization.k8s.io: Manages role-based access control (RBAC) policies, including Roles and RoleBindings.
- storage.k8s.io: Includes resources related to storage, such as StorageClasses and VolumeAttachments.

**Custom Resource Definitions** allow users to create their own API groups and this can be considered as the 3rd branch of group/groups.

## Versioning conventions
In Kubernetes each group is versioned to allow for API stability and evolution. The version can be either:

- **Alpha (v1alpha1, v1alpha2, etc.)**: Indicates that the feature is new, may not be complete, and is subject to breaking changes without notice.
- **Beta (v1beta1, v1beta2, etc.)**: More stable than alpha, indicating that the feature is well-tested but still subject to potential changes.
- **Stable (v1, v2, etc.)**: Indicates a mature and stable feature that is unlikely to change.

The structure of the API groups and their versioning allows Kubernetes to evolve over time, adding new features and refining existing ones while maintaining compatibility with existing deployments.

## Resource vs Kind vs Object
Object is a K8s Object as being said at the begining of this doc.

Object can be of some Kind, so each object is an instance of some Kind.

But thse terms are kubernetes-ecosystem-scoped. The term that is closer to you instance of a Kubernetes cluster is more like a Resource.
These are your resources, that you can use to obtain your goals.

You cluster have resources of a specific kind. E.g. you can have a resource of kind/type "Pod".
It means that having an object of Pod is possible in your cluster. But not every cluster in the world have the same set of resource available.

E.g. by default you have no such resource as "Loop". But when you register it in your cluster with Custrom Resource Definition. You have it, which means that you can create objects of kind "Loop", and you will use api to do so. The resource can be either empty or contain any count of objects.

## How does that apply to you?

When you create a CRD place it into group next to Kubernetes Default group. Mark it that it is yours, customly defined group.
Use versioning and come up with some good Kind name.