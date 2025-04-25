# AWS DynamoDB Explained

This document provides an easy-to-understand overview of Amazon DynamoDB, a managed NoSQL database service offered by AWS. We'll cover its key features, how it works, and a practical example using a Node.js application.

## ✨ What is DynamoDB?

DynamoDB is a **managed NoSQL database service** by AWS.

* **NoSQL:** Unlike relational databases (like those covered in the previous RDS section) that deal with SQL data in tables, DynamoDB handles **NoSQL data**.
* **Data Format:** NoSQL data in DynamoDB is typically in a **non-tabular format**, often like **JSON documents**. It uses **key-value pairs**.
* **Performance:** Provides **single-digit millisecond performance**.
* **Managed & Serverless:** It's a **fully managed** service, meaning AWS handles the infrastructure, and it's **serverless**, so you don't manage servers.

## 🤔 SQL vs NoSQL

* **SQL:** Uses a structured, tabular format with predefined schemas. Data is organized into tables with rows and columns.
* **NoSQL:** Uses flexible schemas. Data can be stored in various formats like documents, key-value pairs, wide-column stores, or graphs. DynamoDB primarily uses document and key-value models.

## 📊 How DynamoDB Stores Data

DynamoDB stores data in:

* **Tables:** Similar to tables in relational databases, but with flexible schemas.
* **Items:** Equivalent to rows in a relational database. Each item is a collection of attributes.
* **Attributes:** Equivalent to columns in a relational database. They are key-value pairs.

Think of it like adding data for different users. Each user's data is an **item** (a document-like block) containing **attributes** (key-value pairs) like "Name", "Email", "Age", etc.

## 🏢 Real-Life Use Cases

Big companies like **Zoom** and **Dropbox** use DynamoDB for various purposes.

## 💰 AWS Free Tier for DynamoDB

AWS offers a **free tier** for DynamoDB:

* **Storage:** Up to **25GB of storage** is always free.
* **Requests:** Handles up to **200 million requests per month** in the free tier.

This is great if you have a website with moderate traffic and want to use DynamoDB.

## ▶️ Practical Demo: Creating a DynamoDB Table

Let's walk through creating a table in the AWS console.

1.  **Search for DynamoDB:** Go to the AWS console and search for "DynamoDB".
2.  **Managed NoSQL Database:** Click on the managed NoSQL database service.
3.  **Overview Page:** You'll see an overview page with basic information.
4.  **Create Table:** Click on "Create table".
5.  **Table Name:** Give your table a name (e.g., `Contacts`). Keeping the name consistent is helpful if you follow along with application examples.
6.  **Partition Key:** This is like the **primary key** in SQL. It uniquely identifies each item. We'll set the Partition Key as `ID` with the type `String`.

    * The Partition Key is essential for identifying your data. Think of it like an Employee ID, Roll Number, or Account Number.
    * You **must** provide at least a Partition Key.
7.  **Sort Key (Optional):** You can add a Sort Key for more complex data access patterns, but it's not required.
8.  **Default Settings:** We'll use the default settings for now.
9.  **Serverless vs Managed (RDS):**
    * **RDS:** When creating an RDS database, you select instance types and storage types.
    * **DynamoDB (Serverless):** You don't need to select instance or storage types. It's much simpler and straightforward.
10. **Create Table:** Click "Create table".

## ✅ Table Created & Exploring Items

Once created, the table status will show as `Active`, meaning it's ready to use.

* **Explore Table Items:** Click on the table name, then "Explore table items".
* **No Items Initially:** You won't see any items because we've only created the table, not added any data yet.

## ➕ Adding Items Manually

You can manually add items to the table:

1.  **Create Item:** Click on "Create item".
2.  **Partition Key:** Enter a value for the Partition Key (e.g., `01`).
3.  **Add Attributes:** Click "Add new attribute" to add more key-value pairs (e.g., `Username` as String, value `Raju`).
4.  **Create Item:** Click "Create item".

You'll see the added item.

## 🧬 Flexible Schema Example

DynamoDB has a flexible schema. You don't need all items to have the same attributes.

* Create another item with Partition Key `02`.
* Add a `Username` attribute (`Shyam`).
* Add a new attribute `Age` (Number, value `30`).

When you view the items, you'll see that the first item (`01`) only has `Username`, while the second item (`02`) has both `Username` and `Age`. The schema isn't rigid like in SQL.

## 💡 Key Benefits of DynamoDB

* **Serverless:** No need to provision, manage, or maintain servers. You don't worry about instance types or storage.
* **Automatic Scaling:** Automatically scales up or down based on the number of read and write requests to your database. Handles sudden traffic spikes.
* **Zero Downtime:** Generally provides zero downtime for scaling and maintenance.
* **On-Demand Pricing:** Pay only for the actual read and write requests your application performs. Ideal for fluctuating workloads.
* **Idle Cost Saving:** Scales down to zero during inactivity, meaning **no cost** when your database isn't being used. This is a significant advantage!

## ⚖️ Understanding Read and Write Capacity

DynamoDB uses Read Capacity Units (RCUs) and Write Capacity Units (WCUs) to manage throughput.

* **Dashboard:** In the table details, you'll see information about Read/Write Capacity.
* **Auto Scaling:** By default, auto-scaling is usually enabled.
* **Minimum/Maximum Units:** You can configure minimum and maximum capacity units. If there's no activity, it scales down to the minimum (e.g., 1 unit). If traffic increases, it scales up to the maximum.
* **Provisioned vs On-Demand:**
    * **Provisioned:** You provision a fixed amount of read/write capacity beforehand. You pay for this provisioned capacity regardless of usage.
    * **On-Demand:** You pay only for the actual read and write requests. This is simpler billing and scales automatically with your traffic.
* **Controlling Throughput:** You can manually adjust provisioned capacity or set maximum limits in on-demand mode (e.g., up to 40,000 read request units per second).

## 📊 Monitoring

You can easily monitor your table's activity, including auto-scaling events, in the DynamoDB dashboard. You can see when it scales down due to inactivity.

## 🧠 Indexes

Indexes help speed up read operations (queries).

* **Primary Key Index:** The Partition Key (and Sort Key, if used) acts as the primary index.
* **Creating Indexes:** You can create additional indexes on other attributes (like `Username`) if you frequently query based on those attributes. This improves query performance.

## 💾 Backup and Recovery

Backup is crucial for databases, and DynamoDB provides this:

* **On-Demand Backup:** Create backups whenever you need them.
* **Scheduled Backup:** Automate backups at regular intervals.
* **Point-in-Time Recovery (PITR):** A very important feature! Allows you to restore your table to any point in time within the last 35 days. This is useful for recovering from accidental deletions or writes. You can restore to a specific second!

## ⚙️ Additional Settings

* **Estimated Cost:** View an estimated cost based on your usage.
* **Delete Protection:** **Highly recommended!** Turn this on to prevent accidental deletion of your table, which would mean losing all your data.
* **Time to Live (TTL):** Automatically delete items from the table after a specified time. Useful for data that expires.
* **Encryption:** Data is encrypted for security.

## 🚀 Practical Demo: Connecting a Node.js App

Let's see how to connect a Node.js application to our DynamoDB table.

* **Application Type:** We'll use a Docker-based Node.js application. This can run on an EC2 instance or even locally.
* **EC2 Instance:** We'll use an EC2 instance for this demo.
* **Steps:**
    1.  **Create DynamoDB Table:** (Already done).
    2.  **Create EC2 Instance:** Launch a new EC2 instance. We'll use default settings but ensure HTTP traffic is allowed.
    3.  **Connect to EC2:** Connect to the EC2 instance using the EC2 Instance Connect terminal.
    4.  **Install Docker:** Install Docker on the EC2 instance using `sudo yum install docker -y`.
    5.  **Start Docker Service:** Start the Docker service: `sudo systemctl start docker`. Verify the status is active and running.
    6.  **Pull Docker Image:** Pull the pre-built Node.js application Docker image from Docker Hub: `sudo docker pull <image_name>`. The image is publicly available.
    7.  **Run Docker Container:** Run the Docker container, providing AWS credentials and region as environment variables.

        * You'll need:
            * `AWS_REGION`: The region where your DynamoDB table is located.
            * `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY`: These are credentials to allow your application to interact with AWS services.
        * **Generating Access Keys:** You can generate these keys in the IAM section of the AWS console under Users -> Security credentials -> Create access key. Choose "Command Line Interface (CLI)" as the use case. **Be careful with these keys; treat them like passwords.**
        * **Docker Run Command:** The command will look something like:
            ```bash
            sudo docker run -p 80:3000 -e AWS_REGION=<your_region> -e AWS_ACCESS_KEY_ID=<your_access_key_id> -e AWS_SECRET_ACCESS_KEY=<your_secret_access_key> <image_name>
            ```
            (This binds port 80 on the EC2 instance to port 3000 on the container where the Node.js app is listening).
    8.  **Verify Container:** Use `sudo docker ps` to see if the container is running.
    9.  **Access the Application:** Get the Public IP of your EC2 instance from the AWS console and access it in your browser. You should see the contact application.
    10. **Add Data via App:** Use the application to add new contacts (e.g., `baburao@email.com`).
    11. **Verify Data in DynamoDB:** Go back to the DynamoDB console, explore the table items, and refresh. You should see the new items added by the application with unique IDs.

## 🔒 Connecting Applications Securely (IAM Roles)

Instead of using Access Keys directly in your application (which is less secure), a better approach when running on EC2 is to use **IAM Roles**.

* **How it Works:**
    1.  Go to the IAM console -> Roles -> Create role.
    2.  Choose `AWS service` and `EC2` as the use case.
    3.  Grant permissions: Search for "DynamoDB" and attach policies like `AmazonDynamoDBFullAccess` to the role. This gives the EC2 instance permission to access DynamoDB.
    4.  Create the role.
    5.  Attach the role to your EC2 instance: In the EC2 console, select the instance, go to Actions -> Security -> Modify IAM role, and select the role you created.

* When your application runs on the EC2 instance with this role, it automatically gets the necessary permissions to interact with DynamoDB without needing explicit access keys.

## ⏩ DynamoDB Accelerator (DAX)

* **What it is:** An **in-memory caching service** for DynamoDB. Similar to Elastic Cache for RDS.
* **Benefit:** Provides **microsecond latency** for reads, a **10x performance boost** compared to the already fast single-digit millisecond performance of DynamoDB.
* **Use Cases:** Ideal for applications requiring extremely low read latency, like financial applications.
* **How it Works:** DAX sits between your application (e.g., on an EC2 instance) and DynamoDB. It caches frequently accessed data. When your application requests data, DAX checks its cache first. If found, it returns the data very quickly. If not, it fetches it from DynamoDB and caches it for future requests.
* **High Availability and Scalability:** Can be deployed across multiple Availability Zones.
* **Integrated with DynamoDB:** Specifically designed to work with DynamoDB.
* **Access in Console:** You can create DAX clusters from the DynamoDB dashboard under the "DAX" section.

## 🌍 DynamoDB Global Tables

* **What it is:** A feature for building **multi-region, multi-active databases**.
* **Use Case:** Perfect for global applications where users are spread across different geographical regions (America, Europe, Asia, etc.) and need low-latency access to data.
* **How it Works:** Creates **replicas** of your DynamoDB table in different AWS regions. Changes made to one replica are automatically replicated to other regions.
* **Access in Console:** In the DynamoDB table settings, under the "Global Tables" tab, you can create replicas in other regions.

## 🧹 Cleanup

It's important to clean up resources after practicing to avoid incurring costs.

* **Delete DynamoDB Table:** Go to the DynamoDB table settings and delete the table. Make sure to confirm and choose whether to create a final backup.
* **Terminate EC2 Instance:** Go to the EC2 console, select the instance, and terminate it.
* **Delete IAM Access Keys (if created):** Go to the IAM console, Users -> Security credentials, and delete or deactivate any access keys you created for practice. Deactivating is an option if you might need them again later, but deleting is safer if you don't.

* **Monitor Costs:** Keep an eye on the Cost & Usage report in the AWS console to ensure there are no unexpected charges.

That's a comprehensive overview of AWS DynamoDB! 🎉