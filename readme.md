# CKAD

## Background

> The Certified Kubernetes Application Developer (CKAD) exam certifies that candidates can design, build and deploy cloud-native applications for Kubernetes.

~ [Linux Foundation](https://training.linuxfoundation.org/certification/certified-kubernetes-application-developer-ckad/)

* [Curriculum](https://github.com/cncf/curriculum)
* [k8s doc site](https://kubernetes.io/docs/home/)
* [k8s cheatsheet](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)
* [k8s tutorials](https://kubernetes.io/docs/tutorials/kubernetes-basics/)
* [kubectl commands](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands)
* [K8s arch overview](https://devopscube.com/kubernetes-architecture-explained/)

## Prep

### Courses

1. [Kubernetes Certified Application Developer (CKAD) with Tests - Udemy](https://www.udemy.com/course/certified-kubernetes-application-developer/)
1. [Kubernetes Tutorial for Beginners - Youtube](https://www.youtube.com/watch?v=X48VuDVv0do)

### Study Guides

* [https://devopscube.com/ckad-exam-study-guide/](https://devopscube.com/ckad-exam-study-guide/)
* [https://mattburman.com/how-i-passed-the-ckad-exam/](https://mattburman.com/how-i-passed-the-ckad-exam/)
* [https://learning.oreilly.com/library/view/certified-kubernetes-application/](https://learning.oreilly.com/library/view/certified-kubernetes-application)

## Practice

### Minikube (local)

1. [Install link]((https://minikube.sigs.k8s.io/docs/start/))

    **NOTE** - because using Windows home edition, default hyper-v driver doesn't work...

    Run the following to set default to use docker driver

    `minikube config set driver docker`
1. Start Docker
1. Start minikube: `minikube start`
1. Start dashboard service: `minikube dashboard`

### Online Environments

* [Killer.sh](https://killer.sh/ckad)
* [KubeAcademy](https://kube.academy/courses/ckad-practice/lessons/introduction)
* [KodeKloud](https://kodekloud.com/courses/kubernetes-challenges/)
* [Killercoda](https://killercoda.com/killer-shell-ckad)
* [play with k8s](https://labs.play-with-k8s.com/)

### Example Questions

1. [https://matthewpalmer.net/kubernetes-app-developer/articles/ckad-practice-exam.html](https://matthewpalmer.net/kubernetes-app-developer/articles/ckad-practice-exam.html)
    1. [Walkthrough](https://thospfuller.com/2020/11/09/answers-to-five-kubernetes-ckad-practice-questions-2021/)
1. [https://medium.com/bb-tutorials-and-thoughts/practice-enough-with-these-questions-for-the-ckad-exam-2f42d1228552](https://medium.com/bb-tutorials-and-thoughts/practice-enough-with-these-questions-for-the-ckad-exam-2f42d1228552) 
1. [https://github.com/dgkanatsios/CKAD-exercises](https://github.com/dgkanatsios/CKAD-exercises)
1. [Cloud Academy Webinar](https://www.youtube.com/watch?v=vWYtnP8rZ4I)
1. [k8s practice](https://github.com/StenlyTU/K8s-training-official)
1. [bmuschko github](https://github.com/bmuschko/ckad-prep)

### Commands

```bash
#Set context and namespace
kubectl config set-context <context-of-question> --namespace=<namespace-of-question>

#alias kubectl
alias k=kubectl

#view shortnames for api resources
kubectl api-resources

#delete things instantly
kubectl delete pod nginx --grace-period=0 --force

#view things before and after greped thing via option c
kubectl get pods -o yaml | grep -C 5 labels:

#list api versions compatible with your cluster versions
kubectl api-versions

#create yaml file via kubectl
kubectl run test --image=busybox --restart=Never --port=80 -o yaml --dry-run=client > pod.yaml
```

## Topics

### Configuration

#### Configmap

* store a set of kv pairs
* Can be used as environment variables or mounted as a volume
* configmap can be created using one of the following
        
        #Literal Values
        kubectl create configmap test --from-literal=foo=bar
       
        #Single file w/ env vars
        $ kubectl create configmap test --from-env-file=foo.env
        
        #Single file
        $ kubectl create configmap test --from-file=foo.txt
        
        #Directory containing files
        $ kubectl create configmap test --from-file=foobar

        #or a yaml file like so
        apiVersion: v1
        kind: ConfigMap
        metadata:
          name: test
        data:
          foo: bar
* Referencing configmap

        apiVersion: v1
        kind: Pod
        metadata:
        name: configmap-example
        spec:
        containers:
        - image: busybox:1.36.1
            name: foobar
            envFrom:
            - configMapRef:
                name: foobar

* View configmap values in pod: ```kubectl exec configmap-example -- env```
* Changing name of environment variable at mapping
        
        apiVersion: v1
        kind: Pod
        metadata:
        name: configmap-example
        spec:
        containers:
        - image: busybox:1.36.1
            name: foobar
            env:
            - name: FOO
            valueFrom:
                configMapKeyRef:
                name: foobar
                key: foo

* Mounting a configmap as a volume

        apiVersion: v1
        kind: Pod
        metadata:
        name: configmap-example
        spec:
        containers:
        - image: busybox:1.36.1
            name: foobar
            volumeMounts:
            - name: foo-volume
            mountPath: /etc/config
        volumes:
        - name: foo-volume
            configMap:
            name: foobar

#### Secrets

* store a set of kv pairs
* Can be used as environment variables or mounted as a volume
* Secrets are base64 encoded and stored in mem only (never wrote to disk)
* Secret types
  * generic - secret from a file, directory, or literal value.
  * docker-registry - secret for use with a Docker registry.
  * tls - tls secret.
* Ways to create a secret
        
        #Literal values
        kubectl create secret generic foobar --from-literal=passwd
        
        #File with environment variables
        kubectl create secret generic foobar --from-env-file=foobar.env
        
        #SSH key file
        kubectl create secret generic ssh-key --from-file=id_rsa=~/.ssh/id_rsa

        #Declarative (value most be base64)

        apiVersion: v1
        kind: Secret
        metadata:
          name: foobar
        type: Opaque
        data:
          pwd: gfdgfdgfd
* Using a secret

        #env var
        apiVersion: v1
        kind: Pod
        metadata:
        name: foobar
        spec:
        containers:
        - image: busybox:1.36.1
            name: foobar
            envFrom:
            - secretRef:
                name: foobar
        #mounted volume
        apiVersion: v1
        kind: Pod
        metadata:
        name: foobar
        spec:
        containers:
        - image: busybox:1.36.1
            name: foobar
            volumeMounts:
            - name: foobar-volume
            mountPath: /var/foo
            readOnly: true
        volumes:
        - name: foobar-volume
            secret:
            secretName: foobar

#### Security Context

* The security context is not a Kubernetes primitive.
    * Can be set at pod or container level
    * Container settings take precedence

#### Resource Management

* Resource management [info](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#resource-units-in-kubernetes)
* apply to a namespace
* Can set mins and maxs
* Example ResourceQuota object

        apiVersion: v1
        kind: ResourceQuota
        metadata:
        name: foobar-quota
        spec:
        hard:
            pods: 2
            requests.cpu: "1"
            requests.memory: 500m
            limits.cpu: "2"
            limits.memory: 2000m
* View quotas: ```kubectl describe resourcequota awesome-quota --namespace=team-awesome```
* Once a quote exits, you have to specify resource limits and requests within pod def or creation will fail
* Example pod with limits and requests

        apiVersion: v1
        kind: Pod
        metadata:
        name: foobar
        spec:
        containers:
        - image: busybox:1.36.1
            name: busybox
            resources:
            requests:
                cpu: "1"
                memory: "500m"
            limits:
                cpu: "1"
                memory: "1024m"
* If you request multiple pods that exceed limit, pod creation will fail

#### Service Accounts

* A pod uses a Service Account to auth with k8s API server via auth token
* Service Accounts are assigned RBAC to auth to specific apis
* If no specific service account is defined, the default one will be used
* Default service account has same perms as unauthed user
* View service accounts ```kubectl get serviceaccounts```
* View service account token ```kubectl get serviceaccount default -o yaml | grep -A 1 secrets:```
  * This is a k8s secret which can be viewed via ```kubectl get secret secret_name -o yaml```
* View service account associated to pod ```kubectl get pod nginx -o yaml | grep serviceAccountName```
* Create service account ```kubectl create serviceaccount foobar```
* Assigning custom service account
    * --serviceaccount flag
    * serviceAccountName key under spec section of pod

### Multi-Container Pods

* Multiple containers can be in a pod
* Typically want to stick to single service per pod
* Might have multiple containers for pods because you need to do some pre-setup for your service or you have a helper function or security service you want to be able to use along with your service
* Containers within a pod can communicate via localhost with no additional networking config req

#### Init Containers

* run before your normal container starts in a pod and can help initialize things
* You can have multiple init containers that are ran in the order that they appear in the definition file
* if init container fails, whole pod is restarted and init containers are called again
* init containers are defined in the ```spec.initContainers``` section of a pod definition
* If you run ```kubectl get pod <podname>``` and see the prefix ```Init:``` in the status column, that denotes that the init container is still working
* Get logs from init container

        kubectl logs foobar -c init_foobar

#### Sidecar Pattern

* Container that runs along side application container which provides helper functionality or security services

#### Adaptor Pattern

* transform the output of an app and ship it somewhere

#### Ambassador Pattern

* provides a proxy for communicating with other services

### Observability

#### Health Probs

* 3 types
  * Readiness probe - checks if container is ready for traffic
  * Liveness probe - periodic check to make sure container is still working as expected
  * Startup probe - allows for setting delay before liveness probe starts and can kill container if it doesn't start in specific amount of time
* 3 verification methods
  * custom command - checks exit code of command, 0 is success
  * http get request - checks response code, response code in range of 200-399 is success
  * tcp socket connection - if connection is established, success
* Configurable attributes for probes
  * initialDelaySeconds - delay in initial check
  * periodSeconds - interval for checks
  * timeoutSeconds - max timeout
  * successThreshold - threshold for when probe is considered successful after a failure
  * failureThreshold - threshold for when probe is considered failed

#### Troubleshooting Kubernetes objects

* check yaml, specifically pay attention to indentation, spelling, attribute names
  * kubeyaml.com can help with formatting, although not allowed during the exam
* get general info about the pod: ```kubectl get pods```
* get general info about all things: ```kubectl get all```
* common pod error status
  * ImagePullBackOff | ErrImagePull
    * Image could not be pulled from registry.
    * validate image exists and is spelled right
  * CrashLoopBackOff
    * Application or command run in container crashes.
    * check if container is configured correctly and actually works locally
  * CreateContainerConfigError
    * ConfigMap or Secret referenced by container cannot be found.
    * Check that you named and mapped things correctly
* Get details about pod events: ```kubectl describe pod <pod name>```
  * Events can give an indication as to what is going wrong
* Get all events in a namespace: ```kubectl get events```
* Get logs from a pod: ```kubectl logs <pod name>```
  * ```-f``` allows for streaming logs
  * ```--previous``` allows for viewing previous pod version logs (helpful if a restart occurs)
* Get an interactive shell in pod in order to troubleshoot further: ```kubectl exec <pod nam> -it -- /bin/sh```
  * assumes the container in the pod has a shell
 * If pod doesn't have a container with a shell, you cna leverage ephemeral containers: ```kubectl debug -it <pod name> --image=busybox:1.28 --target=<process namespace>```
* debugging services is similar to pods
  * ```kubectl describe service <service name>```
  * View labels on pods: ```kubectl get pods --show-labels```
  * Get endpoints for a service: ```kubectl get endpoints <service name>```
  * Ensure service target port and pod container port match
    * ```kubectl get service <service name> -o yaml | grep targetPort```
    * ```kubectl get pods <pod name> -o yaml | grep containerPort```

#### Monitoring

* Prometheus and Datadog used a lot
* CKAD doesn't expect familiarity with commercial tools
* metrics server is a cluster wid aggregator of resource usage data
* get metrics for cluster nodes: ```kubectl top nodes```
* get metrics for podss: ```kubectl top pod <pod name>```
