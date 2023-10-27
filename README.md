# GitOps using ArgoCD, Github Actions, Kubernetes

![image](https://github.com/agcaekrem/Node_API/assets/64022432/10f1736d-8b4f-43b9-a5f4-2e7b9555fcee)

## Cluster Details
I used Oracle Cloud Infrastructure Container Engine for Kubernetes (OKE) with two nodes for Kubernetes. I deployed ArgoCD on this cluster. I used Kubernetes **v1.27.2.**

If you want to set up a Kubernetes cluster from scratch on your VMs, you can check my repository here: [https://github.com/agcaekrem/HelloWorld-k8s.git](https://github.com/agcaekrem/HelloWorld-k8s.git)

## Containerization
I created two containers, one for the application and one for the PostgreSQL database. Using Docker Compose for PostgreSQL is a common practice as it simplifies the configuration and management of the database, allows the application and the database to work together, and facilitates quick deployment and testing. Depending on your needs and use case, you can use Dockerfile for PostgreSQL, but Docker Compose can reduce complexity and make management easier.

## Continuous Integration
For Continuous Integration (CI), I used GitHub Actions. With every commit to the Dev branch, I can track progress from the Actions section. I save my image to Docker Hub, and I keep Docker Hub access credentials as secrets on GitHub. 

**You can find the `ci.yaml` in the ".github/workflows" directory.**

**In addition, I added rules to the ci.yaml file for two different branches. After every commit to the development branch, this pipeline will be triggered, while for the production branch, it will be triggered after a pull request.**

### Advantages of Automated Image Updater
- **Faster Updates:** Quick updates when new versions are released.
- **Consistency:** Ensures the same image version in every environment.
- **Reduced Human Errors:** Minimizes human errors during updates.
- **Security:** Checks for security vulnerabilities and applies patches.
- **Efficiency:** Saves time and allows the team to focus on more valuable tasks.

Here are some examples of tools and practices that can be used in the CI step:

- **GitHub Actions:** A powerful and flexible service provided by GitHub, allowing you to automate your software workflows.
- **Jenkins:** An open-source automation server used to set up continuous integration and continuous delivery pipelines.
- **GitLab CI/CD:** Integrated into GitLab, it provides built-in CI/CD capabilities, making it easier to manage your software development lifecycle.
- **Travis CI:** A cloud-based CI service that integrates with your GitHub repositories for automated builds and testing.
- **CircleCI:** A platform that automates the software development process, allowing you to build, test, and deploy your code.


**Automated image updaters accelerate software development, make it more secure, and reduce the likelihood of errors.**

## Helm
I deploy my objects in Kubernetes using Helm. You can view the templates in the **`"./my-app/"`** directory.

**values.yaml File:** This file is the foundational configuration file for your Helm chart. Its content determines how various parts of your Helm chart will be configured. Values guide how your application and PostgreSQL database will be deployed and configured. For example, the {{ .Values.app.image.repository }} expression represents the location and version of your application's container image on Docker Hub. Defining values in this file is useful for adapting the Helm chart to different environments.

**Chart.yaml File:** This file contains basic information about your Helm package. It defines the name, version, description, and other metadata of your package. These details guide the sustainability and distribution of your Helm package and chart. The version number, in particular, is crucial for updates and version transitions of your chart.

**app-deployment.yaml:** This YAML file defines how your application is deployed in Kubernetes. The Deployment resource specifies the number of replicas, the container image to use, and other deployment settings. It controls the deployment of your application.

**app-service.yaml:** This file defines a Kubernetes Service resource, making your application accessible from the outside. The Service accepts incoming requests and routes them to the pods where your application is running. This YAML file defines the external service for your application.

**postgres-deployment.yaml:** This file defines how the PostgreSQL database is deployed in Kubernetes. Similar to the application deployment, the PostgreSQL Deployment resource specifies the number of replicas, the container image, and the database configuration. It controls the deployment of the PostgreSQL database.

**postgres-secret.yaml:** This file defines a Kubernetes Secret resource that stores sensitive information for the PostgreSQL database, such as the username, password, and database name. The information is encoded for security reasons.


## Continuous Deployment
**You can find this configuration in the ./argocd/application.yaml file.**

For Continuous Deployment (CD), I used ArgoCD. I installed it on my cluster following the instructions at [https://argo-cd.readthedocs.io/en/stable/getting_started/](https://argo-cd.readthedocs.io/en/stable/getting_started/) and set the service type of the argocd-server to "NodePort."

- After installation, I connected to the UI via **"https://[EXTERNAL_NODE_IP]:[SERVICE_IP]."** For the password, you need to check the secret.
- To find [Service_IP], you can use:

  `kubectl get svc -n argo cd.`
- To find [EXTERNAL_NODE_IP], you can use:

  `kubectl get nodes -o wide.`

You can get the password by running the following command:

`kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo`

**You should paste the output as the password. For the username, use "admin."**

"You can later deploy your repository through the "New Application" tab on the user interface. Additionally, you can achieve this by using the ArgoCD CLI. After creating the "application.yaml" file in the **"./argocd/"** directory in my GitHub repository using Vim or Nano on your server, you can apply it by using the **`kubectl apply -f application.yaml `** command

### To summarize
If you have a Kubernetes cluster and have already installed Argo CD, simply running the argocd.yaml file should be sufficient.
