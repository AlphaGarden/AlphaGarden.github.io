---
layout: single
title:  "Tess Notes(kubernetes)"
header:
  overlay_color: "#000"
  overlay_filter: "0.1"
  overlay_image: /
  cta_label: "Learn more about kubernetes!"
  cta_url: "https://www.containership.io"
  caption: "Photo credit: [**_Jiayong Mo_**](https://alphagarden.github.io)"
classes: wide
date:   2018-06-23 16:51:01 -0600

excerpt: "A note for kubernetes learning"
categories: Node.js Openstack REST
---

#### Tess notes (kubernetes)

* Create resource from a file or from stdin.
`tess kubectl create`
* Display one or many resource.
`tess kubectl get`
* Show details of a specific resource or group of resources.  This command joins many API calls together to form a detailed description of a given resource or group of resources. 
`tess kubectl describe`
* Execute a command in a container.
`tess kubectl exec`
* Delete resources by filenames, stdin, resources and names, or by resources, and label selector.
`tess kubectl delete`
* Get the account information 
`tess kubectl get account xxx -oyaml`
* Create a new name space 
`tess kubectl create namepsace xxxx`
* Get all  information for this namespace
`tess kubectl get all -n xxxx `
* Deploy an application to the namespace
`tess kubectl create -f xxxx -n xxxx`
* Create and run a particular image, possibly replicated. Create a deployment or job to manage the created container(s).
`tess kubectl run`

#### `Kubernetes Objects`
Every kubernetes object includes two nested object fields that govern the object's configuration: the object `spec` and the object `status`. 
* `spec` must provide by people, describes the desired state for the object
* `status` provide by Kubernetes System, describes the actual stated of the object.
`kubernetes obejct example`
``` yaml
apiVersion: apps/v1 
metadata:
  name: nginx-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.7.9
        ports:
        - containerPort: 80
```
* **apiVersion** - Which version of the Kubernetes API you’re using to create this object
*  **kind** - What kind of object you want to create
* **metadata** - Data that helps uniquely identify the object, including a `name`string, `UID`, and optional `namespace`
* **spec** -[Podspec](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.10/#podspec-v1-core), [DeploymentSpec](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.10/#deploymentspec-v1-apps)

####`Pod`
A pod encapsulates an application container(or in cases, multiple containers), storage resources, an unique network IP, and options that govern how the container(s) should run. 
A pod represents a unit of deployment: a single instance of an application in kubernetes.
* **Pods that run a single container**
* **Pods that run multiple containers that need to work together**

`pod template`
Pod templates are pod specifications which are included in other objects, such as Replication Controllers, Jobs, and DaemonSets.
``` yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  labels:
    app: myapp
spec:
  containers:
  - name: myapp-container
    image: busybox
    command: ['sh', '-c', 'echo Hello Kubernetes! && sleep 3600']
```

`Pod phase`
* Pending
* Running
* Succeeded: All containers terminated in success.
* Failed: All containers terminated in failure.
* Unknown: State can not be obtained.

`Pod lifetime`
* Use a Job for Pods that are expected to terminate, for example, batch computations. Jobs are appropriate only for Pods with restartPolicy equal to OnFailure or Never.

* Use a `ReplicationController`, `ReplicaSet`, or `Deployment`for Pods that are not expected to terminate, for example, web servers. `ReplicationControllers` are appropriate only for Pods with a restartPolicy of Always.

* Use a `DaemonSet` for Pods that need to run one per machine, because they provide a machine-specific system service.

`initContainers`
The `initContainers` is the containers run before the app container.
* They always run to comletion.
* Each one must complete successfully before the next one is stared.

`Pod Preset`
PodPresets are objects of injecting certain information into pods at creation time. The information can include secrets, volumes, volume mounts, and environment variables.

#### `Controller`

`ReplicationController` 
A ReplicationController ensures that a specified number of pod replicas are running at any one time. In other words, a ReplicationController makes sure that a pod or a homogeneous set of pods is always and available.

This example ReplicationController config runs three copies of the nginx web server.
``` yaml
apiVersion: v1
kind: ReplicationController
metadata:
  name: nginx
spec:
  replicas: 3
  selector:
    app: nginx
  template:
    metadata:
      name: nginx
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx
        ports:
        - containerPort: 80
```


`ReplicaSet` 

`Service`
`Volume`
`Namespace`