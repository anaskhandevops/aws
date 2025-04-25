# AWS EKS: Elastic Kubernetes Service ☸️🏗️

This video is about **Amazon Elastic Kubernetes Service (EKS)**. EKS makes it easy to **deploy, manage, and scale containerized applications** using **Kubernetes** on AWS.

## What is Kubernetes (K8s)? 🤔🐳🎯

* **Kubernetes (often called K8s)** is an **open-source platform** for **automating** the **deployment, scaling, and management** of your **containerized applications** (like those built with Docker).
* **Why use it?** When you run many containers for a complex application, managing them manually is hard. Kubernetes helps with challenges like:
    * **Scaling:** Automatically running more copies of your application when traffic increases, or fewer when it decreases.
    * **Load Balancing:** Distributing network traffic across multiple container copies.
    * **Management:** Restarting failed containers, rolling out updates, managing configuration.
    * **High Availability & Backup:** Ensuring your application stays available even if a server fails.

## Kubernetes Architecture: Master & Workers 🧠💪💻

A Kubernetes cluster has two main types of components:

* **Master Node (Control Plane):** This is the **brain** of the cluster. It manages the cluster state, decides where to run containers, handles scaling, updates, etc. It's responsible for the overall orchestration.
* **Worker Nodes:** These are the servers (virtual machines) where your actual application containers (running inside **Pods**, Kubernetes' smallest deployable units) are hosted and run.

## How EKS Manages Kubernetes 🏗️🧠

* **EKS is a managed service.** When you use EKS, AWS **manages the Kubernetes Master Node (Control Plane) for you**.
* **Benefit:** AWS handles the complexity, availability, and security of the control plane. Your Master node runs across multiple AWS Availability Zones (AZs), preventing a single point of failure for the brain of your cluster. AWS takes care of its health and updates.
* The EKS Control Plane runs in an AWS-managed VPC, separate from your applications.

## Worker Node Options in EKS 💪💻 Fargate

While AWS manages the Master node, you still need worker nodes to run your applications. EKS supports two main ways to provide worker nodes:

1.  **EC2 Instances (Self-Managed or Managed Node Groups):**
    * You provision **EC2 virtual servers** that join the EKS cluster as worker nodes.
    * You have full control over the instance type, scaling, and underlying operating system.
    * You can manage these EC2s yourself (Self-Managed) or use AWS **Managed Node Groups**, where AWS helps automate their lifecycle (updates, patching, scaling).
2.  **AWS Fargate (Serverless):**
    * This is a **serverless** option for worker nodes. You **don't provision or manage any EC2 instances**.
    * You define your application's containers and required resources (CPU, memory), and Fargate runs them directly as Tasks that join your EKS cluster as worker nodes.
    * **Benefit:** Simpler management (no servers to patch/update) and you only pay for the compute resources your containers actually use.

## Important Note: EKS is a Paid Service! ⚠️💰

* **You pay for the EKS Control Plane:** There's a cost per hour for running the Kubernetes control plane managed by AWS (around $0.10 per hour is mentioned as a standard rate).
* **You pay for the Worker Nodes:** You also pay for the compute resources used by your worker nodes. This is either the cost of the **EC2 instances** you run or the cost of **Fargate** based on the resources used by your containers.
* The total cost depends on how many worker nodes you run and their size/type, plus the control plane cost. For small tests, it can be relatively low, but costs add up if you run larger clusters for longer periods.

## Practical Demo: Creating an EKS Cluster and Deploying an App 🛠️💻🚀

Creating an EKS cluster manually can be complex. Tools like `eksctl` simplify this.

### Prerequisites (Tooling) 🛠️

To follow this demo, you need command-line tools installed and configured on your local machine:

1.  **AWS CLI:** Install and configure the AWS Command Line Interface with your AWS credentials. Ensure your user has sufficient permissions (Admin access simplifies the demo, but specific EKS permissions are needed in production). Verify with `aws sts get-caller-identity`.
2.  **`eksctl`:** A simple CLI tool for creating and managing EKS clusters. Install it (e.g., using Homebrew on Mac or Chocolatey on Windows).
3.  **`kubectl`:** The standard Kubernetes command-line tool for interacting with any Kubernetes cluster. Install it (e.g., using Homebrew, Chocolatey, or specific OS instructions). Verify with `kubectl version --client`.

### Creating the EKS Cluster 🏗️⚙️

We'll use the `eksctl` tool to create the cluster with a single command.

1.  Open your terminal.
2.  Run the command:
    ```bash
    eksctl create cluster --name=my-cluster --enable-auto --region=<your-aws-region>
    ```
    > *Replace `my-cluster` with your desired cluster name and `<your-aws-region>` with your chosen AWS region (e.g., `eu-north-1`, `ap-south-1`).*
    > *`--enable-auto` tells `eksctl` to automate much of the setup, including creating default worker nodes.*
3.  Press Enter. `eksctl` will start creating the cluster using AWS CloudFormation.
4.  **⚠️ Be Patient!** This process takes a **significant amount of time**, usually between 15 and 45 minutes.
5.  Observe the output in your terminal. It shows steps like creating VPC, subnets, security groups, the EKS control plane, and worker nodes.
6.  (Optional) While it's running, go to the **AWS EKS console**. You should see your cluster listed with a status of "Creating".
7.  Wait until the `eksctl` command finishes and reports that the cluster is ready.

### Interacting with the Cluster (`kubectl`) 🧠🤝

`eksctl` usually configures your local `kubectl` automatically to communicate with your new cluster.

1.  In your terminal, run a `kubectl` command to check the cluster info:
    ```bash
    kubectl cluster-info
    ```
2.  You should see output showing that the Kubernetes control plane is running, along with its address. This confirms `kubectl` can connect to your EKS cluster. ✅

### Deploying a Sample Application (`kubectl apply`) 🚀🐳

Let's deploy a simple containerized application to your cluster using `kubectl` and a Kubernetes manifest file (YAML). The demo uses a manifest for the 2048 game.

1.  Get the application's YAML manifest file. (The demo references a link to a file named `2048-game.yaml` in the AWS documentation/quick start). You'll need to download or copy this file's content.
    > *This YAML file typically defines the required Kubernetes objects: a Namespace (to logically group resources), a Deployment (telling K8s how to run your container, including the container image and number of replicas), a Service (to allow network access to your running containers), and an Ingress (to expose the service to the internet).*
2.  In your terminal, use `kubectl apply` to create the resources defined in the YAML file:
    ```bash
    kubectl apply -f <path-to-your-yaml-file> -n <namespace>
    ```
    > *Replace `<path-to-your-yaml-file>` with the actual path to the `2048-game.yaml` file. Replace `<namespace>` with the namespace defined in the YAML (e.g., `game-2048`).*
3.  Observe the output. `kubectl` reports that it created the specified resources (namespace, deployment, service, ingress, etc.).

### Accessing the Deployed Application (using Ingress) 🌐✅

The Ingress rule you applied exposes your application to the internet.

1.  Get the public address of your Ingress. Run this command:
    ```bash
    kubectl get ingress -n <namespace>
    ```
    > *Replace `<namespace>` with the namespace you used (e.g., `game-2048`).*
2.  Look at the output. Under the **ADDRESS** column, you will find a public IP address or a hostname (like a Load Balancer DNS name).
3.  **Copy** this address.
4.  Open a **web browser** and paste the address into the address bar.
5.  You should see the **2048 game** loading and running! 🎉 Your containerized application is running on your EKS cluster and accessible from the internet.

### Verifying Created Resources 🧐📊

You can check the AWS console to see resources `eksctl` created for your cluster (especially with `--enable-auto`).

* **EKS Console:** Your cluster status should be "Active".
* **EC2 Console:** Go to the EC2 dashboard -> Instances. You should see **EC2 instances** running. These are your **Worker Nodes** that `eksctl` created automatically as part of the cluster. Note their instance type (e.g., `t3.large`).
* **EC2 -> Load Balancers:** You might see a Load Balancer that was created by the Ingress resource you applied.

## Cleanup: Deleting Your EKS Cluster 🗑️🧹

**Crucially**, to stop incurring costs, you must delete your EKS cluster and all its associated resources.

1.  Open your terminal.
2.  Use the `eksctl delete cluster` command, specifying the name and region:
    ```bash
    eksctl delete cluster --name=my-cluster --region=<your-aws-region>
    ```
    > *Replace `my-cluster` and `<your-aws-region>` with the name and region you used when creating the cluster.*
3.  Press Enter. `eksctl` starts deleting the resources, using CloudFormation.
4.  **⚠️ Be Patient!** Deletion also takes a **significant amount of time** (similar to creation time).
5.  Observe the terminal output as `eksctl` deletes the control plane, worker nodes, load balancers, and other related resources.
6.  Wait until the command finishes and confirms that all cluster resources were deleted.
7.  Verify in the AWS EKS console that your cluster is gone. You can also check the EC2 console to ensure the worker node instances have been terminated and the Load Balancers are deleted.

## Customizing Cluster Creation (`eksctl` Options) ⚙️✍️

`eksctl` offers many options to customize your cluster and worker nodes instead of using just `--enable-auto`:

* `--nodes=<count>`: Specify the initial number of worker nodes (default is often 2 if not using `--enable-auto`).
* `--nodes-min=<min>`, `--nodes-max=<max>`: Configure autoscaling for your worker nodes.
* `--region=<region>`: Explicitly set the AWS region for the cluster.
* `--node-type=<instance-type>`: Choose the EC2 instance type for your worker nodes (e.g., `t3.medium`, `m5.large`). Using smaller types can save cost for testing.
* `--fargate`: Create a cluster that uses Fargate for worker nodes instead of EC2 instances.

By understanding these concepts and tools, you can start leveraging the power of Kubernetes on AWS with EKS! 👍