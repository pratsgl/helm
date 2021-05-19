# Helm Charts

### What is helm ?
Helm (site) is an open-source Kubernetes package and operations manager. A Helm Chart at its minimum will contain a deployment and a service, but can contain any number of Kubernetes objects, for example ingress and persistent Volume Claims.

In this post, we will see how we can get started with Helm and deploy Kubernetes Objects with Helm
   -  Use of Helm
   -  Understanding Helm’s Architecture
   -  Working with Kubernetes Cluster
   -  Helm Basic Operations
   -  Example project with "nginx" pod
   -  Building a Helm Chart
   -  Installing a Local Helm Chart
   -  Upgrading Deployment
   -  Rolling back deployment
   -  Delete deployment

### Why would we use Helm?
Helm really shines where Kubernetes didn't go. For instance, templating. The scope of the Kubernetes project is to deal with your containers for you, not your template files. 

This makes it overly difficult to create truly generic files to be used across a large team or a large organization with many different parameters that need to be set for each file.

And also, how do you version sensitive information using Git when template files are plain text?

The answer: Go templates. Helm allows you to add variables and use functions inside your template files. This makes it perfect for scalable applications that'll eventually need to have their parameters changed.

We have already learnt that a Helm Chart is used to deploy an application or even as a part of a larger application deployment strategy. There are many official Helm Charts, which can be downloaded from their respective repositories. Because they utilize an open-source licensing model you are free to use as-is or modify to suit your purpose. For example modify a Helm Chart to install a PostgreSQL container rather than a MySQL one.

The power of a Helm Chart is clear. It simplifies software deployment (installation, configuration, integration). Helm is a layer of abstraction that can simplify deployment whilst providing a method of repeatability, which in turn brings stability – and stability is one of the core pillars of operational desires.

### Understanding Helm’s Architecture

Helm’s mainly focuses on three things when you are managing applications in the cluster.

Security: Helm makes sure that the package comes from a trusted source and the security of the network it is pulled from, etc.

Reusability: We can install the same thing again and again into the cluster or namespace in a cluster. We can do it repeatedly and predictably.

Configurability: We can externalize the configuration and pass it while installing the repository into the cluster. Even though Helm is not a configuration management tool but still provides some configuration.
Charts

A Chart in a Helm is nothing but a packaged version of your application. A chart is a set of files and directories that follows some specification for describing the resources to be installed into Kubernetes.

A chart contains Chart.yaml that provides the information about the chart version, name, description, etc. All the Kubernetes manifest files are under the templates folder. You can pass the configuration with the Values.yaml file. This file contains parameters that you can override during installation and upgrade.

### Customizing your Helm Environment

Currently we have only added the standard Helm repository, but you will want to create your own Charts that are customized for your environment. As your customized Charts cannot be easily be hosted on the default repository; this will mean hosting your own repository. Fortunately, this is easy as a Helm repository is effectively any HTTPS server that can service yaml, tar files and answer GET requests. 

### What does a Helm Chart look like?

At its most basic a Helm Chart is a yaml file. You will need to get acquainted with these if you want to deploy your own custom applications.

To generate a skeleton Chart you use the following command (chartname is your name for your Chart):
```console
$ helm create buildchart

$ find .
.
./buildchart
./buildchart/.helmignore
./buildchart/values.yaml
./buildchart/Chart.yaml
./buildchart/charts
./buildchart/templates
./buildchart/templates/serviceaccount.yaml
./buildchart/templates/_helpers.tpl
./buildchart/templates/ingress.yaml
./buildchart/templates/deployment.yaml
./buildchart/templates/NOTES.txt
./buildchart/templates/tests
./buildchart/templates/tests/test-connection.yaml
./buildchart/templates/service.yaml
```
This creates a values.yaml file, a template folder that contains three more yaml files; deployment, ingress and service. Amongst a number of other files as outlined below:


#### Examine the chart's structure

Now that you have created the chart, take a look at its structure to see what's inside. The first two files you see—Chart.yaml and values.yaml—define what the chart is and what values will be in it at deployment.

Look at Chart.yaml, and you can see the outline of a Helm chart's structure:

```console
apiVersion: v2
name: buildachart
description: A Helm chart for Kubernetes

# A chart can be either an 'application' or a 'library' chart.
#
# Application charts are a collection of templates that can be packaged into versioned archives
# to be deployed.
#
# Library charts provide useful utilities or functions for the chart developer. They're included as
# a dependency of application charts to inject those utilities and functions into the rendering
# pipeline. Library charts do not define any templates and therefore cannot be deployed.
type: application

# This is the chart version. This version number should be incremented each time you make changes
# to the chart and its templates, including the app version.
version: 0.1.0

# This is the version number of the application being deployed. This version number should be
# incremented each time you make changes to the application.
appVersion: 1.16.0
```
The first part includes the API version that the chart is using (this is required), the name of the chart, and a description of the chart. The next section describes the type of chart (an application by default), the version of the chart you will deploy, and the application version (which should be incremented as you make changes).

The most important part of the chart is the template directory. It holds all the configurations for your application that will be deployed into the cluster. As you can see below, this application has a basic deployment, ingress, service account, and service. This directory also includes a test directory, which includes a test for a connection into the app. Each of these application features has its own template files under **templates**/:
```console
$ ls templates/
NOTES.txt            _helpers.tpl         deployment.yaml      ingress.yaml   
```
There is another directory, called charts, which is empty. It allows you to add dependent charts that are needed to deploy your application. Some Helm charts for applications have up to four extra charts that need to be deployed with the main application. When this happens, the values file is updated with the values for each chart so that the applications will be configured and deployed at the same time. This is a far more advanced configuration (which I will not cover in this introductory article), so leave the **charts**/ folder empty.

Understand and edit values

Template files are set up with formatting that collects deployment information from the values.yaml file. Therefore, to customize your Helm chart, you need to edit the values file. By default, the values.yaml file looks like:

Change the value to Always.

Before:
```console
image:
  repository: nginx
  pullPolicy: IfNotPresent
```
After:
```console
image:
  repository: nginx
  pullPolicy: Always
```
Naming and secrets

Next, take a look at the overrides in the chart. The first override is imagePullSecrets, which is a setting to pull a secret, such as a password or an API key you've generated as credentials for a private registry. Next are nameOverride and fullnameOverride. From the moment you ran helm create, its name (buildachart) was added to a number of configuration files—from the YAML ones above to the templates/helper.tpl file. If you need to rename a chart after you create it, this section is the best place to do it, so you don't miss any configuration files.

Change the chart's name using overrides.

Before:
```console
imagePullSecrets: []
nameOverride: ""
fullnameOverride: ""
```
After:
```console
imagePullSecrets: []
nameOverride: "cherry-awesome-app"
fullnameOverride: "cherry-chart"
```
Accounts
Service accounts provide a user identity to run in the pod inside the cluster. If it's left blank, the name will be generated based on the full name using the helpers.tpl file. I recommend always having a service account set up so that the application will be directly associated with a user that is controlled in the chart.

As an administrator, if you use the default service accounts, you will have either too few permissions or too many permissions, so change this.

Before:
```console
serviceAccount:
 # Specifies whether a service account should be created
  create: true
  # Annotations to add to the service account
  annotations: {}
  # The name of the service account to use.
  # If not set and create is true, a name is generated using the fullname template
  Name:
```
After:
```console

serviceAccount:
 # Specifies whether a service account should be created
  create: true
  # Annotations to add to the service account
  annotations: {}
  # The name of the service account to use.
  # If not set and create is true, a name is generated using the fullname template
  Name: cherrybomb
```
Security

You can configure pod security to set limits on what type of filesystem group to use or which user can and cannot be used. Understanding these options is important to securing a Kubernetes pod, but for this example, I will leave this alone.
```console
podSecurityContext: {}
  # fsGroup: 2000

securityContext: {}
  # capabilities:
  #   drop:
  #   - ALL
  # readOnlyRootFilesystem: true
  # runAsNonRoot: true
  # runAsUser: 1000
```
Networking

There are two different types of networking options in this chart. One uses a local service network with a ClusterIP address, which exposes the service on a cluster-internal IP. Choosing this value makes the service associated with your application reachable only from within the cluster (and through ingress, which is set to false by default). The other networking option is NodePort, which exposes the service on each Kubernetes node's IP address on a statically assigned port. This option is recommended for running minikube, so use it for this how-to.
```console
Before:

service:
  type: ClusterIP
  port: 80

ingress:
  enabled: false
```
After:
```console
service:
  type: NodePort
  port: 80

ingress:
  enabled: false
```
Resources

Helm allows you to explicitly allocate hardware resources. You can configure the maximum amount of resources a Helm chart can request and the highest limits it can receive. Since I'm using Minikube on a laptop, I'll set a few limits by removing the curly braces and the hashes to convert the comments into commands.

Before:
```console
resources: {}
  # We usually recommend not to specify default resources and to leave this as a conscious
  # choice for the user. This also increases chances charts run on environments with little
  # resources, such as Minikube. If you do want to specify resources, uncomment the following
  # lines, adjust them as necessary, and remove the curly braces after 'resources:'.
  # limits:
  #   cpu: 100m
  #   memory: 128Mi
  # requests:
  #   cpu: 100m
  #   memory: 128Mi
```
After:
```console
resources:
 # We usually recommend not to specify default resources and to leave this as a conscious
  # choice for the user. This also increases chances charts run on environments with little
  # resources, such as Minikube. If you do want to specify resources, uncomment the following
  # lines, adjust them as necessary, and remove the curly braces after 'resources:'.
   limits:
     cpu: 100m
     memory: 128Mi
   requests:
     cpu: 100m
     memory: 128Mi
```console
Tolerations, node selectors, and affinities

These last three values are based on node configurations. Although I cannot use any of them in my local configuration, I'll still explain their purpose.

nodeSelector comes in handy when you want to assign part of your application to specific nodes in your Kubernetes cluster. If you have infrastructure-specific applications, you set the node selector name and match that name in the Helm chart. Then, when the application is deployed, it will be associated with the node that matches the selector.

Tolerations, tainting, and affinities work together to ensure that pods run on separate nodes. Node affinity is a property of pods that attracts them to a set of nodes (either as a preference or a hard requirement). Taints are the opposite—they allow a node to repel a set of pods.

In practice, if a node is tainted, it means that it is not working properly or may not have enough resources to hold the application deployment. Tolerations are set up as a key/value pair watched by the scheduler to confirm a node will work with a deployment.

Node affinity is conceptually similar to nodeSelector: it allows you to constrain which nodes your pod is eligible to be scheduled based on labels on the node. However, the labels differ because they match rules that apply to scheduling.
```console
nodeSelector: {}

tolerations: []

affinity: {}
```
After changes values.yaml look as following
```console
# Default values for buildchart.
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

replicaCount: 1

image:
  repository: nginx
  pullPolicy: Always

imagePullSecrets: []
nameOverride: "cherry-awesome-app"
fullnameOverride: "cherry-chart"

serviceAccount:
  # Specifies whether a service account should be created
  create: true
  # The name of the service account to use.
  # If not set and create is true, a name is generated using the fullname template
  name: cherrybomb

podSecurityContext: {}
  # fsGroup: 2000

securityContext: {}
  # capabilities:
  #   drop:
  #   - ALL
  # readOnlyRootFilesystem: true
  # runAsNonRoot: true
  # runAsUser: 1000

service:
  type: NodePort
  port: 80

ingress:
  enabled: false
  annotations: {}
    # kubernetes.io/ingress.class: nginx
    # kubernetes.io/tls-acme: "true"
  hosts:
    - host: chart-example.local
      paths: []
  tls: []
  #  - secretName: chart-example-tls
  #    hosts:
  #      - chart-example.local

resources:
 # We usually recommend not to specify default resources and to leave this as a conscious
  # choice for the user. This also increases chances charts run on environments with little
  # resources, such as Minikube. If you do want to specify resources, uncomment the following
  # lines, adjust them as necessary, and remove the curly braces after 'resources:'.
   limits:
     cpu: 100m
     memory: 128Mi
   requests:
     cpu: 100m
     memory: 128Mi

nodeSelector: {}

tolerations: []

affinity: {}
```


#### How do these files map together?
 It is in here that Helm finds the YAML definitions for your Services, Deployments and other Kubernetes objects. If you have already generated definitions for your application, all you need to do is replace the generated empty YAML files for your own and voila, you have a working Chart that you can simply deploy using the helm install command.

Helm uses Go to run each of the files in the templates directory using the template rendering engine, Helm has extended the template language by adding a number of utility functions for writing Charts.

### Installation steps 

When the Chart is deployed Helm well generate a definition that will look more like a valid service, we can dry-run this with the following command.

Let’s have a quick look at the service.yaml file that is auto-generated by the creation command.

The next file to take note of is the values.yaml file. This is effectively a variables file; here you can see two variables that relate to the ingress and target port for the application.

if you desire a different value from that in your values.yaml file, you can override the values.yaml file by directly setting the value on the command line .
We can see that later at "upgrade" section 

The next file to look at is the NOTES.txt file this is effectively your Chart documentation, this is a templated file that is printed out after the Chart has successfully deployed.

### Deploying your first Helm Chart
```console
$ vi ./buildchart/values.yaml 
```
Customize changes
When your Chart is ready, you can use this command to create a .tgz package:
```console
$ helm package buildchart
```
You can upload this package to your Chart repository, or install it to your cluster. You can also install or upgrade the Chart without packaging it first.

First, install your Chart “chartname”:

 #### method 1 (with dryrun) : 
 ``` console
 $ helm install --dry-run --debug release1 buildchart-0.1.0.tgz    

install.go:149: [debug] Original chart version: ""
install.go:166: [debug] CHART PATH: /home/user/projects/helm/buildchart-0.2.0.tgz

NAME: release2
LAST DEPLOYED: Tue May 18 10:47:19 2021
NAMESPACE: default
STATUS: pending-install
REVISION: 1
USER-SUPPLIED VALUES:
{}

COMPUTED VALUES:
affinity: {}
fullnameOverride: cherry-chart
image:
  pullPolicy: Always
  repository: nginx
imagePullSecrets: []
ingress:
  annotations: {}
  enabled: false
  hosts:
  - host: chart-example.local
    paths: []
  tls: []
nameOverride: cherry-awesome-app
nodeSelector: {}
podSecurityContext: {}
replicaCount: 2
resources:
  limits:
    cpu: 100m
    memory: 128Mi
  requests:
    cpu: 100m
    memory: 128Mi
securityContext: {}
service:
  port: 80
  type: NodePort
serviceAccount:
  create: true
  name: cherrybomb
tolerations: []

HOOKS:
---
# Source: buildchart/templates/tests/test-connection.yaml
apiVersion: v1
kind: Pod
metadata:
  name: "cherry-chart-test-connection"
  labels:

    helm.sh/chart: buildchart-0.2.0
    app.kubernetes.io/name: cherry-awesome-app
    app.kubernetes.io/instance: release2
    app.kubernetes.io/version: "1.16.0"
    app.kubernetes.io/managed-by: Helm
  annotations:
    "helm.sh/hook": test-success
spec:
  containers:
    - name: wget
      image: busybox
      command: ['wget']
      args:  ['cherry-chart:80']
  restartPolicy: Never
MANIFEST:
---
# Source: buildchart/templates/serviceaccount.yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: cherrybomb
  labels:

    helm.sh/chart: buildchart-0.2.0
    app.kubernetes.io/name: cherry-awesome-app
    app.kubernetes.io/instance: release2
    app.kubernetes.io/version: "1.16.0"
    app.kubernetes.io/managed-by: Helm
---
# Source: buildchart/templates/service.yaml
apiVersion: v1
kind: Service
metadata:
  name: cherry-chart
  labels:
    helm.sh/chart: buildchart-0.2.0
    app.kubernetes.io/name: cherry-awesome-app
    app.kubernetes.io/instance: release2
    app.kubernetes.io/version: "1.16.0"
    app.kubernetes.io/managed-by: Helm
spec:
  type: NodePort
  ports:
    - port: 80
      targetPort: http
      protocol: TCP
      name: http
  selector:
    app.kubernetes.io/name: cherry-awesome-app
    app.kubernetes.io/instance: release2
---
# Source: buildchart/templates/deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: cherry-chart
  labels:
    helm.sh/chart: buildchart-0.2.0
    app.kubernetes.io/name: cherry-awesome-app
    app.kubernetes.io/instance: release2
    app.kubernetes.io/version: "1.16.0"
    app.kubernetes.io/managed-by: Helm
spec:
  replicas: 2
  selector:
    matchLabels:
      app.kubernetes.io/name: cherry-awesome-app
      app.kubernetes.io/instance: release2
  template:
    metadata:
      labels:
        app.kubernetes.io/name: cherry-awesome-app
        app.kubernetes.io/instance: release2
    spec:
      serviceAccountName: cherrybomb
      securityContext:
        {}
      containers:
        - name: buildchart
          securityContext:
            {}
          image: "nginx:1.16.0"
          imagePullPolicy: Always
          ports:
            - name: http
              containerPort: 80
              protocol: TCP
          livenessProbe:
            httpGet:
              path: /
              port: http
          readinessProbe:
            httpGet:
              path: /
              port: http
          resources:
            limits:
              cpu: 100m
              memory: 128Mi
            requests:
              cpu: 100m
              memory: 128Mi

NOTES:
1. Get the application URL by running these commands:
  export NODE_PORT=$(kubectl get --namespace default -o jsonpath="{.spec.ports[0].nodePort}" services cherry-chart)
  export NODE_IP=$(kubectl get nodes --namespace default -o jsonpath="{.items[0].status.addresses[0].address}")
  echo http://$NODE_IP:$NODE_PORT

 ```
 #### Installation 
```console
$ helm install  release2 buildchart-0.2.0.tgz -n staging
NAME: release2
LAST DEPLOYED: Tue May 18 10:47:40 2021
NAMESPACE: staging
STATUS: deployed
REVISION: 1
NOTES:
1. Get the application URL by running these commands:
  export NODE_PORT=$(kubectl get --namespace staging -o jsonpath="{.spec.ports[0].nodePort}" services cherry-chart)
  export NODE_IP=$(kubectl get nodes --namespace staging -o jsonpath="{.items[0].status.addresses[0].address}")
  echo http://$NODE_IP:$NODE_PORT
```
 ```console
 $ helm install release1 buildchart-0.1.0.tgz  
 ```
Or

#### method 2 : [by passing absolue path of values file]   
```console
$ helm install my-cherry-chart buildachart/ --values buildachart/values.yaml 
```
Check resources on K8s cluster
```console
$ kubectl get nodes --namespace default -o jsonpath="{.items[0].status.addresses[0].address}"
$ kubectl get --namespace default -o jsonpath="{.spec.ports[0].nodePort}" services cherry-chart
```
Open Browser and check with NodePort & Port
List the helm releases – you should see a generated deployment name with the Docker image designated by “chartname”.

```console
$ helm list -n staging or helm ls -n staging
NAME    	NAMESPACE	REVISION	UPDATED                                	STATUS  	CHART           	APP VERSION
release2	staging  	1       	2021-05-18 10:47:40.609466405 +0530 IST	deployed	buildchart-0.2.0	1.16.0 
```
### Upgrade : 
To make changes, update the version number in chart.yaml. Package the Chart, and upgrade.

Copy values.yaml to override-values.yaml - change replicas to "2"
```console
$ helm upgrade -f values.yaml -f override-values.yaml release1 ./../buildchart -n staging  [ you should be inside respective chart folder] 
Release "release1" has been upgraded. Happy Helming!
NAME: release1
LAST DEPLOYED: Tue May 18 10:39:15 2021
NAMESPACE: staging
STATUS: deployed
REVISION: 2
NOTES:
1. Get the application URL by running these commands:
  export NODE_PORT=$(kubectl get --namespace staging -o jsonpath="{.spec.ports[0].nodePort}" services cherry-chart)
  export NODE_IP=$(kubectl get nodes --namespace staging -o jsonpath="{.items[0].status.addresses[0].address}")
  echo http://$NODE_IP:$NODE_PORT

$ helm list -n staging
NAME    	NAMESPACE	REVISION	UPDATED                                	STATUS  	CHART           	APP VERSION
release1	staging  	2       	2021-05-18 10:39:15.494479617 +0530 IST	deployed	buildchart-0.1.0	1.16.0
```
### Rollback 
```console
$ helm rollback release1 1 -n staging
Rollback was a success! Happy Helming!

$ helm list -n staging [watch "REVISION" number changes ]
NAME    	NAMESPACE	REVISION	UPDATED                                	STATUS  	CHART           	APP VERSION
release1	staging  	3       	2021-05-18 10:42:02.056325362 +0530 IST	deployed	buildchart-0.1.0	1.16.0  

$ kubectl get secrets -n staging
```

### Install on diff namespace
```console
$ helm install release1 buildchart-0.1.0.tgz -n staging 
```
### Delete the deployment.
```console
$ helm delete release1  -n staging
```

### Helm commands

#### Search in local repo
$ helm search repo 
eg: helm search repo nginx 

### Search in hub
$ helm search hub nginx

#### Update
$ helm repo update

### Add to repo
$ helm repo add bitnami https://charts.bitnami.com/bitnami 
Note: These repos are not stored in your local system repo , but they are reference of metadata

### Pull charts
$ helm pull bitnami/nginx --untar=true

### Install helm charts
$ helm install helm-nginx-release1 bitnami/nginx -n staging 
Note :  "staging" is a name space on K8s

### Delete helm deployment
$ helm delete helm-nginx -n staging 


#### Conclusion

Helm is here to stay. It has helped and will help a lot of Kubernetes developers out there for a long time. 
If you want to know how to use Helm, you can refer to their docs: https://helm.sh/

Resources :
- https://opensource.com/article/20/5/helm-charts
- https://medium.com/bb-tutorials-and-thoughts/how-to-get-started-with-helm-b3babb30611f
- https://amazicworld.com/using-helm-to-package-and-deploy-container-applications/

