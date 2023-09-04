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
