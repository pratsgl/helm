# Helm Charts

$ helm create buildachart

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


vi ./buildchart/values.yaml 
<make required changes> 

cd .. 

helm package buildchart

### Installation steps 

method 1 : $ helm install --dry-run --debug release1 buildchart-0.1.0.tgz    
                  $ helm install release1 buildchart-0.1.0.tgz  

Or

method 2:    helm install my-cherry-chart buildachart/ --values buildachart/values.yaml 


$ kubectl get nodes --namespace default -o jsonpath="{.items[0].status.addresses[0].address}"

$ kubectl get --namespace default -o jsonpath="{.spec.ports[0].nodePort}" services cherry-chart

Open Browser and check with NodePort & Port

$ helm list   or /     helm ls -n mytools

### Upgrade : 

Copy values.yaml to override-values.yaml - change replicas to "2"

$ helm upgrade -f values.yaml -f override-values.yaml release1 ./../buildchart -n mytools  [ you should be inside respective chart folder] 

$ helm list -n mytools

### Rollback 

$ helm rollback release1 1 -n mytools

$ helm list -n mytools  [watch "REVISION" number changes ]

$ kubectl get secrets -n mytools


$helm delete  release1

### Install on diff namespace

helm install release1 buildchart-0.1.0.tgz -n mytools 

helm delete release1  -n mytools

Resources :

- https://opensource.com/article/20/5/helm-charts
- https://medium.com/bb-tutorials-and-thoughts/how-to-get-started-with-helm-b3babb30611f
- https://amazicworld.com/using-helm-to-package-and-deploy-container-applications/
