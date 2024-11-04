# local-elastic-stack
A simple one stop setup to start a local elastic stack on k8s using minikube. 

It uses ECK Operator (which is installed using kubectl). 

As an optional step this setup uses Ingress-Nginx (which is enabled using minikube plugins). 

To make life easier a self signed certificate is provided. You can install it on your OS  as a trusted authority to not get any warnings when using `https`` urls. 

All elastic operator stuff is installed in `elastic-system` namespace, while the stack it slef (elasticsearch, kibana, beats, apm) are  installed in a namespace called [devoops](https://www.youtube.com/watch?v=Pg3uPXTDFbk). Ingress pods are installed in in the `ingress-nginx` namespace.

![image](https://user-images.githubusercontent.com/72706/120281976-e7e1b800-c2b9-11eb-980f-d4a4c7b6773d.png)


## Steps
- Install Minikube on your environment. https://minikube.sigs.k8s.io/docs/ (I have tested it using Fedora and kvm2). You can also  use this setup using k8s from Docker for Desktop, but that was not tested with this version of documentation.
- Make sure you have sufficent resources available. I have the following 
- Confirm that your kubernetes setup is working by checking `kubectl get pods`  and/or `kubectl get nodes` 
- Make sure that `kubernetes.docker.internal` DNS name resolves to the IP of k8 pod i.e. run `nslookup kubernetes.docker.internal` and check the result.
- Install [ECK](https://www.elastic.co/guide/en/cloud-on-k8s/current/k8s-deploy-eck.html).

```
kubectl create -f https://download.elastic.co/downloads/eck/2.14.0/crds.yaml
kubectl apply -f https://download.elastic.co/downloads/eck/2.14.0/operator.yaml

```

Create a tls secret 
```
 kubectl -n kube-system create secret tls mkcert --key server.key --cert server.crt
```

Then 
```
minikube addons configure ingress
```
-- Enter custom cert(format is "namespace/secret"): kube-system/mkcert

and disable and enable ingress 

```
minikube addons disable ingress
minikube addons enable ingress
```


- Apply the all-in-one.yml using 
```
kubectl apply -f all-in-one.yaml
```

- We used a certificate in the step where we created nginx-tls-secret. To use the certificate just add it to your browser ( on windows it can be done by right clicking the crt file)
- Browse to http://kubernetes.docker.internal/kibana and use elastic/elastic to login. You can also port forward kibana as following

```
kubectl port-forward service/kbdev-kb-http 5601 -n devoops
```

## Test
 - Use the following [sample](https://github.com/geoaxis/elastic-k8s-in-7mins-samples) repo to test the application
 
## Considerations 
 - This setup is meant for testing purposes only (to facilitate local development). It uses checked in default passwords/tokens on purpose. DON'T use those passwords and tokens in production. Alternatively comment out the lines that set the passwords/tokens and ECK will generate them for you witin K8s Secrets.
 - This sample uses Elastic enterprise features using ECK [license management](https://www.elastic.co/guide/en/cloud-on-k8s/current/k8s-licensing.html) features. Please check the [EULA](https://www.elastic.co/eula) for the 30 day trial this software is distributed under. 
