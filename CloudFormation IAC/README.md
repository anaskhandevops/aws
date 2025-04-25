# AWS CloudFormation: Infrastructure as Code 🏗️📝

This section explains AWS CloudFormation, which is an **IaC (Infrastructure as Code) Service** for AWS. This means you can **use code to create and manage your AWS resources**.

## Manual vs. Automated Infrastructure 🖱️🤖

* Before, we did everything manually in the AWS console. For example, creating an EC2 instance involved clicking, filling out details like the image, name, instance type, storage, security groups, etc. This was a lot of **manual work**.
* To **automate** this same work, using code is much more efficient.
* **CloudFormation helps us do this automation**.

## How CloudFormation Works: Templates and Declarative Approach 📝✨

* You create a file called a **Template**.
* This code can be in **JSON or YAML format**.
* CloudFormation uses a **Declarative Approach**.

### What is Declarative? 🤔

* It means you just tell CloudFormation the **desired result** you want, not the exact steps to get there.
* **Example:** In the simple YAML code shown, you declare:
    * "I want to create **Resources**."
    * "I want a resource of **Type**: EC2 Instance."
    * "It should have these **Properties**: t3.micro InstanceType, a specific Image ID, and a name (e.g., My Simple Instance)."
* You just declare *what* you want, and CloudFormation figures out *how* to create it.

## Practical Demo 1: Creating a Single EC2 Instance ✅💻

1.  Go to the **AWS Console** and search for **CloudFormation**.
2.  Click **Create Stack**.
3.  Choose how to specify the template. We used an **Existing Template** by uploading a file. You can also use sample templates or build with the Infrastructure Composer.
4.  The template file is typically created **locally** (e.g., `ec2-create.yaml`). The screen showed the same simple EC2 YAML code.
5.  Choose **Upload a file** and select your `.yaml` or `.yml` file.
6.  Optional but useful: Click **Infrastructure Composer** to **Visualize** your template. You'll see a block diagram of your resources (e.g., an EC2 instance block). You can click on blocks for details and **Validate** the template here.
7.  Continue to CloudFormation console, click **Next**.
8.  Give your Stack a **Name** (e.g., `Demo-Stack`).
9.  Click **Next**, keep defaults for now, click **Next** again.
10. **Review** the details before creating.
11. Click **Submit**. CloudFormation starts building.
12. Watch the **Stack Status**. It changes from `CREATE_IN_PROGRESS` (🟡) to `CREATE_COMPLETE` (🟢).
13. Go to the **Resources tab** in your stack. You'll see the resource that was created (your EC2 instance) with its logical ID and physical ID.
14. Click the Physical ID link to go directly to the EC2 console.
15. In the EC2 console, you will see the instance running with the name and type you specified in the template (e.g., `My Simple Instance`, `t3.micro`). CloudFormation created it for you!

## Updating a CloudFormation Stack 🔧🔄

* Let's say you want to **change something** in your EC2 instance (based on the template).
* Select your stack (`Demo-Stack`) and click **Update**.
* You have options: replace the current template, modify, or **Edit in Infrastructure Composer**. Let's use the Composer again.
* In the Composer, click on the EC2 block, go to the template code.
* Change the name of the instance (e.g., from `My Simple Instance` to `My Demo Instance`).
* Click **Update Template**, confirm and continue.
* Click **Next**.
* CloudFormation shows a **Change Set Preview** (🔍). This is very useful! It shows **what changes will be made** before you apply them.
* The preview shows `Modify` the resource and `Replacement: false`. This means CloudFormation will **modify the existing instance** without deleting and recreating it.
* Click **Submit**.
* The stack status changes to `UPDATE_IN_PROGRESS` (🟡) and then `UPDATE_COMPLETE` (🟢).
* Go back to the EC2 console and **Refresh**. You will see the instance name has changed to `My Demo Instance`.

## Handling Update Failures (Rollback) ❌🔙

* Let's try another update, changing the instance type from `t3.micro` to `t2.nano`.
* Follow the update process (Select stack -> Update -> Edit in Composer -> Change template -> Update Template -> Next -> Next -> Submit).
* This time, the update might **fail** (`UPDATE_FAILED` 🔴).
* You can check the **Events tab** in the stack details to see why it failed (e.g., incompatible instance type change).
* The good thing is that CloudFormation automatically performs a **Rollback** (`UPDATE_ROLLBACK_IN_PROGRESS`, `UPDATE_ROLLBACK_COMPLETE` 🟢).
* What does the rollback do? It **reverts the stack to its previous working state**.
* Check the EC2 console. The instance is **still running** and is back to its previous state (t3.micro, name `My Demo Instance`).
* Check the template in the Composer again. It has also been **rolled back** to show `t3.micro`.
* This shows CloudFormation's **State Management** capabilities – it keeps track of the desired state from the template and the actual state of your resources.

## Deleting Resources: Delete the Stack 🔥💸

* One of the biggest benefits for **Cost Saving** and cleanup is deleting the stack.
* When you delete a stack, CloudFormation **deletes ALL the resources** that were created by that stack.
* Select your stack (`Demo-Stack`) and click **Delete**.
* Confirm the deletion.
* Watch the status change to `DELETE_IN_PROGRESS` (🟡) and then `DELETE_COMPLETE` (🟢).
* Check the EC2 console. The instance created by this stack is **shutting down** or **terminated**.
* This prevents you from accidentally leaving resources running and incurring costs.

## Practical Demo 2: Creating Multiple Resources (Web Server) 🌐💾🛡️

* CloudFormation isn't just for single resources. You can create **Multiple Resources** with a single template.
* This example template creates:
    * An **EC2 Instance** (`t3.micro`).
    * Includes **User Data** (a startup script) to: install a web server (Nginx or HTTPD), create a simple HTML file, and start the web service.
    * A **Security Group** to allow inbound HTTP traffic (on port 80 or 8084 based on the template).
    * An **Elastic IP** to give the instance a static public IP address.
    * The template uses **References** (`Ref` function) to link resources (e.g., the Security Group is referenced by the EC2 instance, the Elastic IP is referenced by the EC2 instance).
* Create a new stack, upload this new template (`ec2-nginx-group.yaml`).
* Use the **Visualizer** again. You'll see blocks for the EC2 Instance, Security Group, and Elastic IP, with lines showing their connections.
* Create the stack (e.g., name it `Web-Server-Stack`).
* Once `CREATE_COMPLETE` (🟢), go to the **Resources tab**. You'll see three resources listed: the EC2 instance, the Security Group, and the Elastic IP.
* Click the EC2 Physical ID link. Note the instance is running.
* Copy the **Public IPv4 Address** of the instance.
* Paste the IP address into a web browser (and add the port `:<port_number>` if it's not the default 80).
* You should see the **sample web page** that was created by the startup script!

## Key Benefits of CloudFormation ✨🏆

* **Consistency:** Ensures your infrastructure is created the same way every time.
* **Automation:** Automate infrastructure creation and management. Can be triggered based on conditions or schedules (e.g., using AWS CLI).
* **Repeatable & Reproducible:** Easily create identical environments for development, testing, or production.
* **Cost Savings:** Simplifies cleanup by deleting all related resources with a stack deletion.
* **State Management:** Tracks the desired state from your template and the actual state of your resources, handles rollbacks on failure.
* **Stack Sets:** Deploy stacks across multiple AWS accounts and regions. (The stacks we created were region-specific).
* **Cost Free Tool:** The CloudFormation service itself is free! You only pay for the AWS resources it creates. 💸
* **Drift Detection:** Helps identify if someone manually changed a resource outside of CloudFormation, ensuring your deployed infrastructure matches your template.

## Other Use Cases (What else can you build?) 🚀

You can use CloudFormation to provision a wide range of AWS services, including:

* S3 Buckets with HTTP endpoints (simple websites)
* Custom VPCs, Subnets, and Route Tables
* RDS Databases (with automatic backups)
* Lambda Functions
* API Gateways
* Elastic Load Balancers (ELB)
* Auto Scaling Groups for web applications
* IAM Users, Roles, and Policies

## Using CloudFormation with AWS CLI 💻🤖

* You can use the **AWS Command Line Interface (CLI)** to work with CloudFormation templates for greater automation.
* Make sure your AWS CLI is **configured** (refer to the IAM section for setting up credentials and default region).
* You can run a command like this from your local machine:
    ```bash
    aws cloudformation create-stack --stack-name YourNewStackName --template-body file://your-template-file.yaml --region your-aws-region
    ```
* This command tells CloudFormation to create a stack using the specified local template file. You can try this yourself!

## Finding Resource Information: The Documentation 📚🔍

* How do you know what code to write in your template? **AWS Documentation** is your best friend!
* Specifically, look at the **AWS CloudFormation User Guide**.
* Navigate to the **Template Reference**.
* Then find the **Resource and Property Reference**.
* You can find documentation for every AWS service you want to provision (e.g., `AWS::EC2::Instance`, `AWS::S3::Bucket`, `AWS::RDS::DBInstance`).
* Click on the resource type (like `AWS::EC2::Instance`) to see all the possible **Properties** you can define in your template (InstanceType, ImageId, SecurityGroupIds, Tags, UserData, etc.) and their valid values/formats.
* This reference is crucial for writing your CloudFormation templates.

This overview should give you a solid understanding of what AWS CloudFormation is, how it works, and why it's useful for managing your AWS infrastructure! 🎉