# local-elastic-stack
A simple one stop setup to start a local elastic stack on k8s. 

It uses ECK (which is installed using kubectl). 

As an optional step this setup installed nginx-ingress, which is installed using helm.

All elastic operator stuff is installed in elastic-system, while the stack it slef (elasticsearch, kibana, beats, apm) are  installed in a namespace called [devoops](https://www.youtube.com/watch?v=Pg3uPXTDFbk).  

![image](https://user-images.githubusercontent.com/72706/120281976-e7e1b800-c2b9-11eb-980f-d4a4c7b6773d.png)


## Steps
- Install [Docker for Windows](https://docs.docker.com/docker-for-windows/install/) (This should work on docker for mac as well, but I have not tested it). Enable kubernetes on it.
- Install [ECK](https://www.elastic.co/guide/en/cloud-on-k8s/current/k8s-deploy-eck.html).

```
kubectl create -f https://download.elastic.co/downloads/eck/1.8.0/crds.yaml
kubectl apply -f https://download.elastic.co/downloads/eck/1.8.0/operator.yaml
```

- Install nginx ingress using [helm](https://kubernetes.github.io/ingress-nginx/deploy/#using-helm).
```
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo update
helm install ingress-nginx ingress-nginx/ingress-nginx
```

- Apply the all-in-one.yml using 
```
kubectl apply -f all-in-one.yaml
```

- Browse to http://kubernetes.docker.internal/kibana and use elastic/elastic to login.
You can also port forward kibana as following

```
kubectl port-forward service/kbdev-kb-http 5601 -n devoops
```

## Test
 - Use the following [sample](https://github.com/geoaxis/cachingdemo/releases/tag/step6) to test the application
 
## Considerations 
 - This setup is meant for testing purposes only (to facilitate local development). It uses checked in default passwords/tokens on purpose. DON'T use those passwords and tokens in production. Alternatively comment out the lines that set the passwords/tokens and ECK will generate them for you witin K8s Secrets.
 - This sample uses Elastic enterprise features using ECK [license management](https://www.elastic.co/guide/en/cloud-on-k8s/current/k8s-licensing.html) features. Please check the [EULA](https://www.elastic.co/eula) for the 30 day trial this software is distributed under. 
