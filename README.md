# HELM
Helm-101

## Create Helm Repositories for Services: Service1 & Service2

We deploy and manage two demo services: Service1 & Service2. To streamline deployment and management, we will create a Helm repository containing Helm charts for both services. We will use a dummy BusyBox image for both services to simulate their functionality.

---

### Prerequisites

*  Git and Helm installed on your system.
*  GitHub account for hosting the Helm repo.
*  A running Kubernetes cluster.
*  kubectl configured to interact with the cluster.

---
To work from scratch follow all steps to create helm package and deploy on kubernetes cluster using helm.  
or  
If you want to skip helm package creation: use this repo and Jump directly to step 6.    

## Perform following steps on your system:

## Step 1: Create the Helm Charts

1. **Create the directory:**

   ```bash
   mkdir helm
   cd helm
   ```

2. **Generate Helm charts for both services:**

   ```bash
   helm create service1
   helm create service2
   ```

 > The helm create command is used to generate a new Helm chart scaffold (i.e., a basic directory structure and template files) for your Kubernetes application.
  >  This will generate a directory structure like:
   ```
      myapp/
      ├── charts/             # For dependencies
      ├── templates/          # Kubernetes manifest templates (YAML)
      │   ├── deployment.yaml
      │   ├── service.yaml
      │   ├── ingress.yaml
      │   └── ...
      ├── Chart.yaml          # Chart metadata
      ├── values.yaml         # Default values for templates
      ├── .helmignore         # Files to ignore during packaging
  ```

---

## Step 2: Clean the repository for files not required

```bash
cd service1
cd templates/
rm hpa.yaml ingress.yaml serviceaccount.yaml service.yaml _helpers.tpl NOTES.txt
rm -r templates/tests
```

```bash
cd service2/
cd templates/
rm hpa.yaml ingress.yaml serviceaccount.yaml service.yaml _helpers.tpl NOTES.txt
rm -r templates/tests
```
---

## Step 3: Customize the Helm Charts

### BusyBox Deployment Template:

Here is the complete deployment template file for the BusyBox service:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ .Release.Name }}-{{ .Chart.Name }}
  labels:
    app: {{ .Chart.Name }}
spec:
  replicas: 1
  selector:
    matchLabels:
      app: {{ .Chart.Name }}
  template:
    metadata:
      labels:
        app: {{ .Chart.Name }}
    spec:
      containers:
        - name: {{ .Chart.Name }}
          image: {{ .Values.image.repository }}:{{ .Values.image.tag }}
          command: ['sh', '-c', 'echo {{ .Values.appMessage }}; sleep 3600']
```

### Service1 Chart:

```bash
cd service1 
```

1. Update the deployment template (`templates/deployment.yaml`):

   Use BusyBox deployment template

2. Update the `values.yaml` file in the service1 chart:

   ```yaml
   image:
     repository: 
     tag: latest
   appMessage: "Welcome to Service1"
   ```
3. Verify the added values

   ```bash
   helm lint
   ```
   > The helm lint command is used to validate the syntax and structure of a Helm chart. It's a great way to catch common mistakes before deploying your chart to a Kubernetes cluster.

### Service2 Chart:

```bash
cd service2 
```

1. Update the deployment template (`templates/deployment.yaml`):

   Use BusyBox deployment template

2. Update the `values.yaml` file in the service chart:

   ```yaml
   image:
     repository: busybox
     tag: latest
   appMessage: "Welcome to Service2"
   ```
3. Verify the added values

   ```bash
   helm lint
   ```

---

## Step 4: Package the Charts

1. Package the service1 chart:

   ```bash
   helm package service1
   ```

2. Package the service2 chart:

   ```bash
   helm package service2
   ```

3. Create an index file:

   ```bash
   helm repo index .
   ```
   or
   when publish it on github

   ```bash
   helm repo index ./ --url https://abcofdevops.github.io/helm
   ```
   > Here we specify the base URL for the chart download links.

---

## Step 5: Host the Helm Repo on GitHub

1. **Create a new GitHub repository:**

   * Name: `helm`

2. **Initialize a Git repo:**

   ```bash
   git init
   git remote add origin https://github.com/username/helm-repo.git
   git remote -v
   ```
> *e.g* git remote add origin https://github.com/abcofdevops/helm.git

3. **Push the Helm charts to GitHub:**

   ```bash
   git add .
   git commit -m "Initial commit"
   git branch

   // Change branch from master to main
   git branch -M main
   git branch

   git pull origin main --rebase  //Incase it says head is above for branch main
   git push -u origin main
   ```

4. **Configure GitHub Pages:**

   * Go to the repo settings.
   * Enable GitHub Pages from the `main` branch.


> GitHub Pages (often abbreviated as GitPages) is a static site hosting service provided by GitHub. It’s commonly used to host project documentation, personal websites, and Helm chart repositories.
   
---

## Step 6: Using the Helm Repo

1. **Add the Helm repo:**

   ```bash
   helm repo add my-repo https://abcofdevops.github.io/helm
   helm repo update
   helm repo list
   ```

2. **Search for charts:**

   ```bash
   helm search repo myrepo
   ```
## Step 7: Install service

1. **Check the kubernetes cluster:**

   ```bash
   kubectl config current-context
   ```
   It will return the current selected cluster, your service will be deployed on this cluster

2. **Install a service (service1):**

   ```bash
   helm install my-service my-repo/service1
   helm status
   helm list
   ```
   my-service is release-name

3. **Check logs of service**
   
   ```bash
   kubectl logs <pod_name>
   ```
   You will see message like "Welcome to Service1"

4. **Install second service (service2):**

   ```bash
   helm install my-service2 my-repo/service2
   helm status
   helm list
   ```
   You can check log for this service it will show "Welcome to Service2"

## Step 8: Update service

1. **Update the Service with some changes (deploy service2 in service1)**

   ```bash
   helm upgrade my-service my-repo/service2
   helm status
   helm list
   ```

## Step 9: Rollback service

1. **Rollback the deployment**

   ```bash
   helm rollback my-service
   ```

2. **Rollback to Specific version**

   ```bash
   helm rollback my-service 1
   ```
   
## Step 10: Uninstall service

1. **Uninstall the services**

   ```bash
   helm uninstall my-service
   helm uninstall my-service2
   ```

##Bonus: To check the values used in the charts

  ```bash
  helm show values my-repo/service1
  ```
---

> ### Understand [chart.yaml](./chart.md)