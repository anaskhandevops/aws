# Understanding AWS ELB & ASG: Load Balancing & Auto Scaling ⚖️⬆️⬇️

In this section, we'll explore AWS Elastic Load Balancing (ELB) and Auto Scaling Groups (ASG). These services are crucial for building scalable and highly available applications in the cloud.

## **Scalability vs. High Availability: What's the Difference? 🤔**

Before diving in, let's understand these two important concepts:

* **Scalability:** The ability of your application to handle increased traffic or load.
    * **Vertical Scaling (Scaling Up):** Increasing the resources (CPU, RAM, etc.) of a single instance. For example, upgrading a `t2.micro` instance to an `m5.large`.
    * **Horizontal Scaling (Scaling Out):** Adding more instances to handle the load. Instead of one powerful server, you have multiple servers working together.
* **High Availability (HA):** Ensuring your application remains accessible with minimal downtime, even if individual components fail. This often involves running resources in multiple Availability Zones.

## **Elasticity: Automatic Resource Adjustment ⚙️**

Elasticity is the ability to automatically adjust resources based on demand. Auto Scaling Groups (ASG) are key to achieving elasticity in AWS.

## **How Load Balancing Works 🚦**

Imagine you have multiple EC2 instances running your website. A Load Balancer acts as a traffic director:

1.  **Single Point of Access:** Users access your website through the Load Balancer's address, not directly through individual instance IPs.
2.  **Distributes Traffic:** The Load Balancer distributes incoming requests across your multiple healthy EC2 instances. This prevents any single instance from being overwhelmed.
3.  **Improves Availability:** If one instance fails, the Load Balancer stops sending traffic to it and directs requests to the remaining healthy instances.

## **Types of Load Balancers in AWS 🏢**

AWS offers different types of Load Balancers:

* **Application Load Balancer (ALB):** Best for HTTP and HTTPS traffic. Operates at the application layer (Layer 7 of the OSI model). Provides advanced routing based on content.
* **Network Load Balancer (NLB):** Best for TCP, UDP, and TLS traffic where high performance and low latency are critical (e.g., gaming, financial applications). Operates at the transport layer (Layer 4 of the OSI model).
* **Gateway Load Balancer (GWLB):** Helps you deploy, scale, and manage third-party virtual appliances like firewalls and monitoring solutions.

## **Practical: Setting Up an Application Load Balancer (ALB) 🛠️**

To demonstrate, we'll set up an ALB to distribute traffic across two EC2 instances running a simple web server.

### **Prerequisites:**

* Two running EC2 instances with a web server installed (we'll use a script for this).
* HTTP (port 80) should be allowed in the security groups of the EC2 instances.

### **Steps:**

1.  **Launch EC2 Instances:** Launch two EC2 instances. In the "User data" section during launch, add a script to install and start a web server and display the instance's hostname:

    ```bash
    #!/bin/bash
    sudo yum update -y
    sudo yum install -y httpd
    sudo systemctl start httpd
    sudo systemctl enable httpd
    echo '<h1>Welcome to my Awesome Web Server on EC2 - Host: $(hostname -f)</h1>' > /var/www/html/index.html
    ```

    Launch two instances with this script.
2.  **Create a Security Group for the Load Balancer:**
    * Go to the EC2 Management Console -> "Security Groups."
    * Click "Create security group."
    * Give it a name (e.g., `my-web-server-lb-sg`).
    * Allow inbound HTTP traffic (port 80) from "Anywhere."
    * Create the security group.
3.  **Create a Target Group:**
    * Go to the EC2 Management Console -> "Load Balancing" -> "Target Groups."
    * Click "Create target group."
    * Choose "Instances" as the target type.
    * Give it a name (e.g., `my-web-server-tg`).
    * Select HTTP as the protocol and 80 as the port.
    * Select your VPC.
    * Click "Next."
    * Register your two EC2 instances to this target group. Click "Include as pending below" and then "Create target group."
4.  **Create the Application Load Balancer:**
    * Go to the EC2 Management Console -> "Load Balancing" -> "Load Balancers."
    * Click "Create Load Balancer."
    * Choose "Application Load Balancer" and click "Create."
    * Give it a name (e.g., `my-web-server-lb`).
    * Choose "Internet-facing" for the scheme.
    * In "Network mapping," select all the Availability Zones in your region.
    * In "Security groups," select the security group you created for the Load Balancer.
    * In "Listeners and routing," keep the default HTTP listener on port 80. For the default action, select the target group you created (`my-web-server-tg`).
    * Click "Create load balancer."
5.  **Test the Load Balancer:** Once the Load Balancer is active, copy its **DNS name** (found in the Load Balancer's details) and paste it into your web browser. You should see the "Welcome..." message, and refreshing the page might show the hostname of the other instance, demonstrating load distribution.

## **Auto Scaling Groups (ASG) ⬆️⬇️**

Auto Scaling Groups automatically adjust the number of EC2 instances in your application based on demand or defined schedules.

### **Benefits of ASG:**

* **Automatic Scaling:** Adds or removes instances based on traffic.
* **Maintains Availability:** Replaces unhealthy instances automatically.
* **Uses Scaling Policies:** You can define rules for scaling based on metrics like CPU utilization or request count.
* **Ensures Desired Capacity:** Always keeps a specified number of instances running.
* **Scheduled Scaling:** Scale instances based on预先确定的时间表 (pre-determined schedules).
* **Multi-AZ Deployment:** Can launch instances across multiple Availability Zones for higher availability.
* **Integrates with ELB:** Attaches instances to a Load Balancer to distribute traffic.
* **Cost Optimization:** Scales down during low demand to save money.

### **How ASG Works (Visual):**

Imagine setting up an ASG with:

* **Minimum Capacity:** 1 instance
* **Desired Capacity:** 2 instances (ASG will aim to keep 2 running)
* **Maximum Capacity:** 3 instances (ASG will not scale beyond 3)

Based on scaling policies (e.g., if CPU usage goes above 50%), the ASG can automatically launch a third instance (up to the maximum). When the load decreases, it can terminate an instance (down to the minimum).

### **Practical: Setting Up an Auto Scaling Group with ELB 🛠️**

We'll create an ASG that uses the ALB we just set up.

1.  **Create an AMI:** Create an AMI from one of your configured EC2 instances running the web server (as described in the AMI section).
2.  **Create a Launch Template:**
    * Go to the EC2 Management Console -> "EC2" -> "Launch Templates."
    * Click "Create launch template."
    * Give it a name (e.g., `my-web-server-template`).
    * Select the AMI you created.
    * Choose an instance type (e.g., `t3.micro`).
    * Select the security group for the instances (allow HTTP).
    * Create the launch template.
3.  **Create an Auto Scaling Group:**
    * Go to the EC2 Management Console -> "EC2" -> "Auto Scaling Groups."
    * Click "Create Auto Scaling group."
    * Give it a name (e.g., `my-web-server-asg`).
    * Select the launch template you created.
    * Click "Next."
    * In "Network settings," select your VPC and multiple Availability Zones.
    * Click "Next."
    * In "Load balancing," choose "Attach to an existing load balancer" and select your ALB and target group.
    * Configure health checks to use the ELB health checks.
    * Click "Next."
    * In "Group size," set your desired, minimum, and maximum capacities (e.g., desired: 2, minimum: 1, maximum: 3).
    * (Optional) Configure scaling policies based on CPU utilization or ALB request count. For simplicity, we can choose "No scaling policy" for now.
    * Click "Next" (for notifications, which we'll skip).
    * Review your configuration and click "Create Auto Scaling group."

