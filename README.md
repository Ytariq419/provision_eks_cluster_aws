Provision EKS Cluster 

Use the package manager homebrew to install the AWS CLI.

```
$ brew install awscli
```

When prompted, enter your AWS Access Key ID, Secret Access Key, region and output format.

```
$ aws configure

OUTPUT: AWS Access Key ID [None]: YOUR_AWS_ACCESS_KEY_ID
AWS Secret Access Key [None]: YOUR_AWS_SECRET_ACCESS_KEY
Default region name [None]: YOUR_AWS_REGION
Default output format [None]: json
```

Set up and initialize your Terraform workspace
In your terminal, clone the following repository. It contains the example configuration used in this tutorial.

```
$ git clone https://github.com/hashicorp/learn-terraform-provision-eks-cluster
```

You can explore this repository by changing directories or navigating in your UI.

```
$ cd learn-terraform-provision-eks-cluster
```

Initialize Terraform workspace
Once you have cloned the repository, initialize your Terraform workspace, which will download and configure the providers.

```
$ terraform init
```

Provision the EKS cluster
In your initialized directory, run terraform apply and review the planned actions. Your terminal output should indicate the plan is running and what resources will be created.

``
$ terraform apply
``


**Configure kubectl**

Now that you've provisioned your EKS cluster, you need to configure kubectl.

Run the following command to retrieve the access credentials for your cluster and automatically configure kubectl.

``
$ aws eks --region $(terraform output -raw region) update-kubeconfig --name $(terraform output -raw cluster_name)
``

**Deploy Kubernetes Metrics Server**

The Kubernetes Metrics Server, used to gather metrics such as cluster CPU and memory usage
over time, is not deployed by default in EKS clusters.

Download and unzip the metrics server by running the following command.

``
$ wget -O v0.3.6.tar.gz https://codeload.github.com/kubernetes-sigs/metrics-server/tar.gz/v0.3.6 && tar -xzf v0.3.6.tar.gz
``

Deploy the metrics server to the cluster by running the following command.

``
$ kubectl apply -f metrics-server-0.3.6/deploy/1.8+/
``


Verify that the metrics server has been deployed. If successful, you should see something like this.


``$ kubectl get deployment metrics-server -n kube-system``

Output will show whether your node is ready or not.



``
 kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.0-beta8/aio/deploy/recommended.yaml
``

Now, create a proxy server that will allow you to navigate to the dashboard from the browser on your local machine. This will continue running until you stop the process by pressing CTRL + C.

``
$ kubectl proxy
``

You should be able to access the Kubernetes dashboard here (http://YOUR IP:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/).


**Authenticate the dashboard**
``
$ kubectl apply -f https://raw.githubusercontent.com/hashicorp/learn-terraform-provision-eks-cluster/master/kubernetes-dashboard-admin.rbac.yaml
``


Then, generate the authorization token.

``
$ kubectl -n kube-system describe secret $(kubectl -n kube-system get secret | grep service-controller-token | awk '{print $1}')
``


Select "Token" on the Dashboard UI then copy and paste the entire token you receive into the dashboard authentication screen to sign in. You are now signed in to the dashboard for your Kubernetes cluster.


Navigate to the "Cluster" page by clicking on "Cluster" in the left navigation
bar. You should see a list of nodes in your cluster.

CLEAN UP - 

``terraform destroy`` 












