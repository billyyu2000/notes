
# Docker k8s desktop

## Enable Kuberneters on Docker Desktop

Preferences / Kuberneters / Enable Kuberneters


## Install ingress controller 

[ingress for desktop](https://kubernetes.github.io/ingress-nginx/deploy/#docker-for-mac)

The problem is the default listening port 80 is gonna easily conflict with other service you already have, so sometimes you may need to alter ingress' default http listening port.


```bash
# 1. dowdload deploy.yaml
wget https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v0.47.0/deploy/static/provider/cloud/deploy.yaml
# 2. modify 1:  replace 2 http 80 port to the number you want to listening, e.g. we can use 10080
# 3. modify 2:  Deployment / containers / args , add  `- --http-port=10080` , 
#        so as to ingress listening 10080 -> 80 ingress
# 4. apply
kubectl apply -f deploy.yaml
```

ingress-nginx-controller listening port has been changed

```bash
$ kubectl get svc -A
NAMESPACE       NAME                                 TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)                           AGE
ingress-nginx   ingress-nginx-controller             LoadBalancer   10.108.84.93    localhost     10080:31402/TCP,443:32053/TCP   20s
```

wait ingress-nginx-controller to be running.

```bash
$ kubectl get po -n ingress-nginx
NAME                                       READY   STATUS      RESTARTS   AGE
ingress-nginx-admission-create-sxwsg       0/1     Completed   0          51s
ingress-nginx-admission-patch-b85nx        0/1     Completed   1          50s
ingress-nginx-controller-b6cb664bc-tnzbm   1/1     Running     0          51s
```

[ingress sample](http://threelambda.com/2020/07/06/run-ingress-example-on-mac/)

**Do NOT add ANNOTATIONS that are not recognized by ingress nginx !! e.g. aws alb annotations**

<details>
<summary>
apple.yaml
</summary>

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: apple-app
  labels:
    app: apple
spec:
  containers:
    - name: apple-app
      image: hashicorp/http-echo
      args:
        - "-text=apple"

---

kind: Service
apiVersion: v1
metadata:
  name: apple-service
spec:
  selector:
    app: apple
  ports:
    - port: 5678 # Default port for image
```

</details>


<details>
<summary>
banana.yaml
</summary>

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: banana-app
  labels:
    app: banana
spec:
  containers:
    - name: banana-app
      image: hashicorp/http-echo
      args:
        - "-text=banana"

---

kind: Service
apiVersion: v1
metadata:
  name: banana-service
spec:
  selector:
    app: banana
  ports:
    - port: 5678 # Default port for image
```

</details>


<details>
<summary>
ingress.yaml
</summary>

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: example-ingress
  annotations:
    ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - http:
      paths:
        - path: /apple_test
          backend:
            serviceName: apple-service
            servicePort: 5678
        - path: /banana_test
          backend:
            serviceName: banana-service
            servicePort: 5678
```

</details>

To check whether your ingress have got address.

```bash
$ kubectl get ingress -A
NAMESPACE     NAME                  CLASS    HOSTS              ADDRESS     PORTS   AGE
default       example-ingress       <none>   *                  localhost   80      4m17s

$ curl localhost:10080/apple  # because we have changed the listening port to 10080
apple
```

route with specific host:

<details>
<summary>
ingress host specified
</summary>

```yaml
- apiVersion: networking.k8s.io/v1
  kind: Ingress
  metadata:
    name: {name}-ingress-443
  spec:
    rules:
    - host: dot-iap-dev.imac
      http:
        paths:
          - pathType: Prefix
            path: /
            backend:
              service:
                name: {name}
                port:
                  number: {port}             

```

</details>

```bash
$ kubectl get ingress -A
NAMESPACE     NAME                  CLASS    HOSTS              ADDRESS     PORTS   AGE
umc-dot-dev   dot-iap-ingress-443   <none>   dot-iap-dev.imac   localhost   80      28m

$ curl localhost:10080
$ curl dot-iap-dev.imac:10080
ok
$ curl localhost:10080
404 Not Found
$ curl -H "host: dot-iap-dev.imac" localhost:10080
ok
```




