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

### Pod Design

#### Label

* key value pairs attached to objects that can be used in search queries
* limited to max length of 63 chars
* only allowed to use alphanumeric and separator chars
* can apply 0:n labels to objects
* can modify objects in use
* Can apply labels with run command using ```--labels``` or ```-l``` option followed by ```=key=value,key2=value2```
  * ```kubectl run pickles --impage=busybox --labels=env=prod,cucumber=false```
* Can also apply lables via the metadata.labels section in yaml

        apiVersion: v1
        kind: Pod
        metadata:
        name: pickles
        labels:
            env: prod
            cucumber: false
        spec:
        containers:
        - image: busybox
            name: busybox
* view labels on objects using the ```describe``` or ```get``` kubectl command
* adding ```--show-labels``` command line option allows you to quickly view all object type labels or a specific object type labels, this option adds a new column to the output called "Labels"
* can use the label command to:
  * add labels: ```kubectl label pod pickles spicy=yes```
  * update labels:  ```kubectl label pod pickles spicy=no --overwrite```
    * error if trying to update without the ```-overwrite``` option
  * remove labels: ```kubectl label pod pickles spicy-```
* Label selection
  * command line: use the ```--selector``` or ```-l``` option
    * equality-based: ```=```, ```==```, or ```!=```
      * Can add multiple filters together with ```AND``` and ```OR```
      * ```kubectl get pods -l env=prod --show-labels```
      * ```kubectl get pods -l 'env=prod OR env=dev' --show-labels```
    * set based : ```in```, ```notin```, ```exists```
      * ```kubectl get pods -l 'env in (prod, dev)' --show-labels```
  * combine both search types: ```kubectl get pods -l 'evn in (prod, dev)',spicy=true --show-labels```
  * Some objects allow for filtering pods, but this is specific to each api on how it is done (examples include deployments and services)

#### Annotation

* key value pairs for providing metadata
* can't be used for querying or selecting
* helpful for storing data about itself
* No options via ```kubectl run``` to add annotations via cli at start, must be done via yaml
* Defined in metadata.annotations like:

        apiVersion: v1
        kind: Pod
        metadata:
        name: pickles
        annotations:
            field: 3
            author: 'Dave Gardner'
        spec:
        containers:
        - image: busybox
            name: busybox
* ```kubectl describe``` and ```kubectl get``` will display annotations for objects
* Modify annotation for active things via ```kubectl annotate```
  * add: ```kubectl annotate pod pickles spicy=yes```
  * update:  ```kubectl annotate pod pickles spicy=no --overwrite```
    * error if trying to update without the ```-overwrite``` option
  * remove: ```kubectl annotate pod pickles spicy-```


#### Deployment

* gives ability to specify the number of pods to always run
* Uses ReplicaSet under the hood
* Create deployment via cli: ```kubectl create deployment deploy-the-things --image=busybox:1.31```
* Create deployment via yaml

        apiVersion: apps/v1
        kind: Deployment
        metadata:
          name: deploy-the-things
          labels:
            app: deploy-the-things
        spec:
          replicas: 1
          selector:
            matchLabels:
              app: deploy-the-things
          template:
            metadata:
              labels:
                app: deploy-the-things
            spec:
              containers:
              - name: busybox
                image: busybox:1.31

* list deployments: ```kubectl get deployments```
* get info about deployment: ```kubectl describe deployment.apps/deploy-the-things```
* see history of deployment: ```kubectl rollout history deployment deploy-the-things```
  * by default, 10 revisions will be stored in history
  * can change the default via spec.revisionHistoryLimit
* view detailed info about a specific revision via: ```kubectl rollout history deployment deploy-the-things --revision=3```
* rollback a deployment: ```kubectl rollout undo deployment deploy-the-things --to-revision=1```
* scale a deployment manually: ```kubectl scale deployment deploy-the-things --replicas=10```
  * record of this event can be viewed via describe on deployment
* autoscaling a deployment
  * horizontal pod autoscaler (HPA)
    * scales the number of replicas based on cpu and memory of thresholds
    * standard k8s feature
    * CLI: ```kubectl autoscale deployment example --cpu-percent=80 --min=4 --max=6```
    * Get status: ```kubectl get horizontalpodautoscalers``` or ```hpa``` for short
      * if the status shows ```<unknown>```, usually means the metrics server isn't running, is misconfigured, of pod template doesn't define resource requirements
        * check the events for the hpa: ```kubectl describe hpa example```
  * vertical pod autoscaler (VPA)
    * scales the cpu and memory allocation for existing pods based on historic metrics
    * has to be supported by cloud provider as an add-on or installed manually
  * use k8s metrics server

#### Job

* runs for a specific number of completions
* not automatically cleaned up once complete
* can use the spec.ttlSecondsAfterFinished attribute for auto-cleanup
* create a job ```kubectl create job hello-world --image=busybox -- /bin/sh -c 'echo "Hello World"'```
* you can use ```kubectl get``` and ```kubectl logs``` for getting info about jobs
* deploy job via yaml

        apiVersion: batch/v1
        kind: Job
        metadata:
          name: hello-world
        spec:
          template:
            spec:
              containers:
              - name: hello-world
                image: busybox
                command:
                - /bin/sh
                - -c
                - echo "hello world"
            restartPolicy: Never

* default behavior is to run workload in a single pod and expect one successful completion, which is called non-parallel job
  * can tweak the spec if you want more completions via spec.template.spec.completions
  * if you want multiple pods to be used, tweak the spec.template.spec.parallelism value
* spec.backoffLimit determines the number of retries allowed (default is 6)
* job template must declare the restart policy via spec.template.spec.restartPolicy
  * Must be either "OnFailure" or "Never"

#### CronJob

* schedule jobs
* can be scheduled via cron-expression
* create a cronjob ```kubectl create cronjob hello-world --schedule="* * * * *"--image=busybox -- /bin/sh -c 'echo "Hello World"'```
* you can use ```kubectl get``` and ```kubectl logs``` for getting info about cronjobs
* deploy cronjob via yaml

        apiVersion: batch/v1beta1
        kind: CronJob
        metadata:
          name: hello-world
        spec:
          schedule: "* * * * *"
          jobTemplate:
            spec:
              template:
                spec:
                  containers:
                  - name: hello-world
                    image: busybox
                    command:
                    - /bin/sh
                    - -c
                    - echo "hello world"
                restartPolicy: Never

* by default cronJob maintains history of last 3 successful pods and the last failed pod
* use spec.successfulJobsHistoryLimit and spec.failedJobsHistoryLimit to check attributes for retention

### Services and Networking

* pods ips are virtual and will change to random values over time
  *  a restart will automatically assign a new ip
* Services can be used to assign a fixed ip to pods based on labels and act as a front door to pods
  * services allow for internal and external exposure
* By default, k8s doesn't restrict inter-pod communication
* A Network policy can can be implemented to attempt to mitigate security risks
  * These policies describe access rules for incoming and outgoing network traffic

#### Services

* provide discoverable names and load balancing for pods
* uses label selection
  * if defined without a label selector, you need to map service to something via endpointslice object
* Service types
  * clusterIP: exposes service on cluster internal ip. Only reachable within the cluster
  * NodePort: exposes the service on each node's IP at a specific port. Accessible outside the cluster
  * LoadBalancer: Exposes the service externally using a cloud provider's load balancer
  * ExternalName: Maps a service to a dns name
* Create a service via cli: ```kubectl create service clusterip cucumber-aging-service --tcp=80:80```
* Can also create a service when doing a pod run with the ```--expose``` option: ```kubectl run pickles --image=busy-box --restart=Never --port=80 --expose```
  * This will create a service on the pod's behalf
* Can also create a service when deploying a deployment: ```kubectl expose deployment example-deployment --port=80 --target-port=80```
* Services can be defined via yaml as well:

        apiVersion: v1
        kind: Service
        metadata:
          name: example-deploy
        spec:
          type: ClusterIP
          selector:
            app: example-deploy
          ports:
          - port: 80
            targetPort: 80

* Listing services: ```kubectl get services```
* Get info about a service: ```kubectl describe service <service name>```
* Port mapping
  * incoming traffic is defined via the ports.port attribute
  * the port you want the traffic to go to for the pods is defined via the ports.targetPort attribute
* ClusterIP
  * is default service and only exposes things internal to the cluster
  * Can use the proxy command to establish a direct connection to the k8s api server from you local system: ```kubectl proxy --port=25000```
    * Should now be able to access things in the cluster via localhost like: ```curl -L localhost:25000/api/v1/namespaces/default/services/example/proxy```
* NodePort
  * exposes access through the node's ip and specific port
  * Available ports: 30000-32767, assigned automatically if not provided in the spec
  * find nodeport for service: ```kubectl get service <service name>```
    * find ip of node: ```kubectl describe node <node name>```

#### Network Policies

* within a cluster, pod to pod communication isn't restricted when using ip or dns name, even across namespaces
* Network policies define rules/acls for traffic
* ingress: incoming traffic
* egress: external traffic
* Elements of a network policy
  * podSelector: selects the pods in the namespace to apply the network policy to
  * policyType: defines the type of traffic (ingress/egress) that the policy applies to
  * ingress: lists the rules for incoming traffic, each rule can define from and ports
  * egress: lists the rules for outbound traffic, each rule can define to and ports
  * cannot create network policies with create command, must be done via yaml
  * Example yaml (deploy via ```kubectl create -f <filename>.yaml```)

        apiVersion: networking.k8s.io/v1
        kind: NetworkPolicy
        metadata:
          name: test
          namespace: default
        spec:
          podSelector:
            matchLabels:
              role: example
          policyTypes:
            - Ingress
            - Egress
          ingress:
            - from:
                - ipBlock:
                    cidr: 172.17.0.0/16
                    except:
                      - 172.17.10.0/24
                - namespaceSelector:
                    matchLabels:
                      project: example
                - podSelector:
                    matchLabels:
                      role: frontend
              ports:
                - protocol: TCP
                  port: 9999
          egress:
            - to:
                - ipBlock:
                    cidr: 10.0.0.0/24
              ports:
                - protocol: TCP
                  port: 7316

* network policies need a network policy controller to be enforced
  * need a network overlay solution that provides a controller
  * Example: Cilium
* list network policies: ```kubectl get networkpolicy```
* get info about a specific policy: ```kubectl describe networkpolicy <policy name>```
* Recommend to add a deny-all policy to start (curly brackets mean apply to all):

        apiVersion: networking.k8s.io/v1
        kind: NetworkPolicy
        metadata:
          name: default-deny-all
        spec:
          podSelector: {}
          policyTypes:
          - Ingress
          - Egress

* Should consider limiting network policies to specific ports when possible

#### State Persistence

* volume
  * by default containers have temporary/ephemeral storage
  * Volumes can be used to persist data
  * volume types
    * determine the medium that backs the volume
    * long list of types
    * volumes to know for the exam
      * emptyDir - Empty directory in Pod with read/write access. Only persisted for the lifespan of a Pod.
      * hostPath - File or directory from the host node’s filesystem
      * configMap, secret - Provides a way to inject configuration data
      * nfs - An existing NFS (Network File System) share
      * persistentVolumeClaim - Claims a Persistent Volume
  * using volumes takes 2 steps:
    * declare the volume via spec.volumes then mount the volume via spec.containers.volumeMounts

            apiVersion: v1
            kind: Pod
            metadata:
              name: example
            spec:
              volumes:
              - name: logs
                emptyDir: {}
              containers:
              - image: busybox
                name: busybox
                volumeMounts:
                - mountPath: /var/logs
                  name: logs

* persistent volumes
  * storage device in k8s that is decoupled from a pod and can be managed separately
  * PersistentVolumeClaim requests the persistent volume for the pod
  * storageclass - abstraction concept that defines a class of storage device
    * view storage classes: ```kubectl get storageclass```
  * Most be defined via manifest
  * needs to define the storage capacity using spec.capacity and an access mode via spec.accessModes
  * access mode types
    * ReadWriteOnce - r/w by a single node
    * ReadOnlyMany - r by many noeds
    * ReadWriteMany - r/w by many nodes
  * persistent volume creation example

        apiVersion: v1
        kind: PersistentVolume
        metadata:
          name: example-pv
        spec:
          capacity:
            storage: 5Gi
          accessModes:
            - ReadWriteMany
          hostPath:
            path: /data/example
  
  * persistent volume claim creation example

        kind: PersistentVolumeClaim
        apiVersion: v1
        metadata:
          name: example-pvc
        spec:
          accessModes:
            - ReadWriteMany
          resources:
            requests:
              storage: 512m

  * mounting persistent volume claim in a pod

        apiVersion: v1
        kind: Pod
        metadata:
          name: example-pvc
        spec:
          volumes:
            - name: example-v
              persistentVolumeClaim:
                claimName: example-pvc
          containers:
          - image: busybox
            name: example
            command: ["/bin/sh"]
            args: ["echo", "Hello"]
            volumeMounts:
              - mountPath: "/mnt/data"
                name: example-v
    