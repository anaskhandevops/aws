# AWS ECS: Elastic Container Service 🐳📦

This video is about **Amazon Elastic Container Service (ECS)**, which is a **cloud-based container management service**.

## What is ECS? 🤔☁️

* ECS helps you **run and manage Docker containers** on a cluster of virtual servers (or in a serverless way).
* If you build your application using **containers** (like with Docker 🐳) and want to run them in the AWS Cloud, ECS is a primary option.
* It supports Docker containers but can also work with private container registries.

## Why Use ECS? ✨⚙️

* **Container-Focused:** ECS is specifically designed for managing containerized applications.
* **Automation:** It **automatically handles** creating, managing, and updating your applications running in containers.
* **Easier Deployment:** Compared to manually setting up Docker on EC2 instances (installing Docker, running commands, monitoring), ECS simplifies the process. You just tell ECS which container image to run, and it handles the underlying infrastructure and management.

## Key ECS Concepts 🧩🏃‍♀️📝

To use ECS, you'll encounter a few terms:

* **Cluster:** A group of resources (like virtual servers or serverless capacity) where you run your containerized applications. It hosts the infrastructure.
* **Task Definition:** A **blueprint** for your application. It's a text file (like JSON) that describes your container(s) – what Docker image to use, how much CPU/memory they need, what ports to open, environment variables, etc.
* **Task:** A **running instance** of a Task Definition. It's your application container(s) actively running within a cluster.
* **Service:** Manages the running Tasks. A Service ensures that a specified number of Tasks are always running. It handles **scalability** (running more or fewer tasks) and can integrate with load balancing.

**Simple Flow:** You define your app (Task Definition 📝) -> You tell a Service ⚙️ to run a specific number of copies (Tasks 🏃‍♀️) using that definition -> The Service runs the Tasks on a Cluster 🧩.

## Important Note: ECS is Not Free Tier ⚠️💰

* Unlike some other AWS services with extensive free tiers (like parts of EC2 or S3), **ECS is generally a paid service**.
* You pay for the underlying compute resources used by your tasks (Fargate serverless capacity or EC2 instances).
* However, for small tests and learning, the cost is usually very minimal (cents or a few dollars). You can definitely use it to learn without breaking the bank, but **remember to clean up** resources afterward!

## Practical Demo: Deploying Nginx on ECS Fargate ✅🚀

Let's deploy a simple Nginx web server using ECS.

1.  Go to the **AWS Console** and search for **ECS (Elastic Container Service)**.
2.  Click **Clusters** in the left menu.
3.  Click **Create Cluster**.
4.  Give your cluster a **Name** (e.g., `My-Cluster`).
5.  For **Infrastructure**, choose **AWS Fargate (serverless)**.
    > *Fargate means you don't manage the underlying servers. AWS handles that.*
6.  Click **Create**.
    > *Your cluster (the place where tasks will run) is ready.*

## 6. Create a Task Definition 📝⚙️

This defines *what* container image to run and its settings.

1.  In the ECS dashboard, click **Task Definitions** in the left menu.
2.  Click **Create new task definition**.
3.  Give your task definition a **Family** name (e.g., `My-Task`).
4.  For **Infrastructure requirements**, select **AWS Fargate**.
5.  Set **Operating system/Architecture** (e.g., Linux/X86_64).
6.  Set **Task size**: Choose appropriate **CPU** and **Memory** for the task (e.g., 0.5 vCPU and 1GB Memory - depends on task size, larger tasks need more resources).
7.  Scroll down to **Container 1** and click **Add container**.
8.  Give the container a **Name** (e.g., `nginx`).
9.  For **Image URI**, enter the name of the public Docker image (e.g., `nginx:latest`).
10. For **Port mappings**, enter the container port that your application listens on. For Nginx, the default is **80** (TCP). Enter `80`.
11. Review other optional settings (like environment variables, health checks, resource limits if needed).
12. Click **Create**.
    > *You've now created the blueprint for your container.*

## 7. Create a Service ⚙️🏃‍♀️

This tells ECS to run and manage your task definition within the cluster.

1.  Go back to the **Clusters** list and click on your cluster (`My-Cluster`).
2.  Click the **Services** tab.
3.  Click **Create**.
4.  For **Launch type**, select **Fargate**.
5.  For **Service configuration**:
    * **Task definition**: Select the **Family** you just created (`My-Task`) and the latest **Revision**.
    * **Service name**: Give your service a name (e.g., `My-Service`).
    * **Desired tasks**: Specify how many copies (replicas) of your container you want running (e.g., `1` or `2`). Let's start with `2` as shown in the demo.
6.  Scroll down to **Networking**.
7.  Select your **VPC** and **Subnets**.
8.  For **Security group**, click **Create a new security group**. Configure its **inbound rules**.
    * Add a rule: **Type**: HTTP, **Source**: Anywhere (`0.0.0.0/0`). This allows internet traffic on port 80.
9.  For **Public IP**, ensure **Turned ON** is selected. This gives your running tasks a public IP address so you can access them from the internet.
10. Review other settings (like Load Balancing - optional, but common for services).
11. Click **Create**.
    > *You've created a service that will keep 2 copies of your Nginx task running.*

## 8. Monitoring Tasks 🧐🔄

* Go back to the **Cluster** details page (`My-Cluster`).
* Click the **Tasks** tab.
* You'll see your tasks starting. Their status will change from `PENDING` (🟡) to `RUNNING` (🟢). You should see the number of running tasks reach the "Desired" number you set in the service (e.g., 2).

## 9. Accessing the Application 🌐✅

1.  In the **Tasks** tab, click on one of the **Task IDs** that is in `RUNNING` status.
2.  Scroll down to the **Configuration** section.
3.  Find the **Public IP** address listed under Network Binding.
4.  **Copy** this Public IP address.
5.  Open a **web browser** and paste the Public IP address into the address bar.
6.  You should see the **"Welcome to nginx!"** page! 🎉 Your containerized web server is running on ECS and accessible from the internet.

## 10. Scaling Your Service (Updating) ⬆️⬇️

You can easily change the number of running tasks.

1.  Go back to your **Cluster** details -> **Services** tab.
2.  Select your service (`My-Service`).
3.  Click **Update**.
4.  Change the **Desired tasks** number (e.g., change from `2` to `1`).
5.  Click **Update**.
6.  Go back to the **Tasks** tab for the cluster. Watch as one of the tasks stops, and the number of running tasks drops to 1.
    > *This shows simple scaling managed by the service.*

## 11. Checking Costs (Optional) 💰

* Go to the AWS Billing Dashboard. You can see your estimated costs. For simple testing, the cost for Fargate tasks running for a short time is usually very low (cents).

## 12. Cleanup Time! 🗑️🧹

To stop incurring costs, you must **delete the resources** you created.

1.  Go back to your **Cluster** details (`My-Cluster`).
2.  Click the **Services** tab.
3.  Select your service (`My-Service`).
4.  Click **Delete**. You might need to confirm the deletion. If tasks are running, it might offer "Force delete" which also stops the tasks.
    > *Deleting the service stops the running tasks.*
5.  Go to **Task Definitions** in the left menu.
6.  Select your task definition (`My-Task`).
7.  Click the **Actions** button and select **Deregister**. Confirm.
    > *Deregistering removes the task definition blueprint.*
8.  Go back to the **Clusters** list.
9.  Select your cluster (`My-Cluster`).
10. Click **Delete Cluster**. You must type the cluster name to confirm.
11. Click **Delete**.
    > *Your cluster and all associated resources created by the cluster are now deleted.*

Remember to verify in the respective sections (Services, Tasks, Clusters) that everything is gone. This completes the cleanup for the ECS part.

This practical guide shows you the basic flow of deploying a containerized application using AWS ECS Fargate! 👍