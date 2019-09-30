# Tekton Hello World Demo

The demo sources used as part of Red Hat DevNationLive Webinar **Plumbing Kubernetes builds | deploy with Tekton**.

## Pre-req

- A [Kubernetes cluster](https://kubernetes.io/docs/setup/learning-environment/minikube/) or [OpenShift](try.openshift.com) with [Tekton](https://tekton.dev) installed
- If minikube have the internal container registry deployed; check [Registry Helpers](https://github.com/kameshsampath/minikube-helpers/tree/master/registry)
- Download and add [Tekton CLI](https://github.com/tektoncd/cli) to the path

[IMPORTANT]

If you are tying to use local container registry for image build and push then its recommended to use minikube version v1.1.1 because of an existing container registry issue https://github.com/kubernetes/minikube/issues/4604

## Demo Sources

The application uses a simple Java HelloWorld application built using [Quarkus](https://quarkus.io), https://github.com/kameshsampath/pipeline-helloworld

```shell
git clone https://https://github.com/kameshsampath/pipeline-helloworld && \
cd pipeline-helloworld
```

## Running Demo

- Create Service Account called `pipeline` and make it as cluster-admin  

> **Kubernetes**

  ```shell
  kubectl create sa pipeline && \
  kubectl create clusterrolebinding pipeline-cluster-admin-binding --clusterrole=cluster-admin --serviceaccount=default:pipeline
  ```

> **OpenShift**

   ```shell
   oc new-project tutorial && \
   oc create serviceaccount pipeline && \
   oc adm policy add-scc-to-user privileged -z pipeline && \
   oc adm policy add-role-to-user edit -z pipeline
   ```

- Create Pipeline Resources

   ```shell
    kubectl create -f build-resources.yaml
   ```

- Create Pipeline Task

   ```shell
    kubectl create -f app-build-task.yaml
   ```

- Run the Pipeline Task

   ```shell
    kubectl create -f app-build-task-run.yaml
   ```

- Create the Pipeline

   ```shell
    kubectl create -f app-deploy.yaml
   ```

- Run the Pipeline

   ```shell
    tkn pipeline start   --resource="app-source=git-source" --resource="app-image=helloworld-image" --serviceaccount='pipeline' app-deploy
   ```
