# AWS Route 53: Scalable DNS and Domain Management 🌐⚙️

This section talks about **Amazon Route 53**, an AWS service for **Domain Name System (DNS)**. We'll cover its uses and see a practical example.

## Use Case: Custom Domain Names ✨

* Before, when we deployed websites on EC2 or S3, you had to use an **IP address** in the browser to access them.
* We want to use a **Custom Domain Name** (like `mywebsite.com`) instead of an IP address.
* **Route 53** helps us achieve this. It's a **scalable DNS service by AWS**.

## What is DNS? 🤔

* **DNS translates Domain Names (like `mywebsite.com`) into IP Addresses (like `192.168.1.1`)**.
* Think of it like a phone book for the internet. You look up a name to find a number (IP).
* **Why "Route 53"?** The default port for DNS is **port 53**.

## How Route 53 Works (Basic Flow) 🔄

* When you type a **Domain Name** in your browser (🌐), the request goes to a **DNS Server**.
* The DNS server looks up the domain name and finds the associated **IP Address**.
* The IP address is sent back to your browser.
* Your browser then uses that IP address to connect to the server (e.g., your EC2 instance) where your website is hosted.
* This is the setup we'll build practically.

## Important Note: Route 53 is a Paid Service 💸⚠️

* Route 53 is a **billable service**.
* You pay monthly fees for **Hosted Zones**.
* You pay for **DNS Queries** (each time someone looks up your domain).
* You pay yearly fees for **Domain Registration** (if you register your domain through AWS).
* If you are concerned about costs, you can just follow along conceptually to understand how it works.

## Practical Demo 1: Setting up a Custom Domain (Simple Routing) ✅💻

### Prerequisite: A Website Hosted on EC2 (or elsewhere)

* We need a simple website running on an EC2 instance first.
* **Steps taken in the demo:**
    * Launched an EC2 instance (`t3.micro`, Amazon Linux 2 AMI).
    * Configured a Security Group to allow SSH (port 22) and HTTP (port 80). Note: the demo script used port 80 for HTTP.
    * Used **User Data** (a startup script) to:
        * Update the server packages.
        * Install a web server (Apache - `httpd`).
        * Start the web server service.
        * Create a simple `index.html` file with content like "Welcome to Apache Web Server from Europe".
    * After launching, verified the website was accessible using the instance's **Public IP Address**.

### Setting up Route 53 for the Custom Domain

1.  Go to the **Route 53 Dashboard** in the AWS Console.
2.  You need a **Domain Name**.
    * **Option A (Register with AWS):** Click "Register domain". Search for your desired domain name. Check availability and yearly price. Checkout to register it (this incurs a cost). If you register with AWS, the Hosted Zone is created automatically.
    * **Option B (Use a Domain Registered Elsewhere):** If you already registered a domain with another provider (like Hostinger, GoDaddy, etc. - demo used Hostinger), you'll use this existing domain.
3.  If using Option B (domain registered elsewhere), you need to **Create Hosted Zone**.
    * Click **Create hosted zone**.
    * Enter your **Domain Name** (e.g., `myexamplewebsite.xyz`).
    * Choose **Type: Public hosted zone**.
    * Click **Create hosted zone**.
4.  Once the Hosted Zone is created, note the **Name Servers (NS Records)** that Route 53 provides. There will be several server names listed (ending in `awsdns-XX.org`, etc.).
5.  Go to your **Domain Registrar's website** (e.g., Hostinger) where you registered the domain.
6.  Find the **DNS Settings** or **Name Servers** section for your domain.
7.  **Change the Name Servers** listed there to the ones provided by Route 53. (This tells the global internet that Route 53 is now the authority for your domain's DNS).
8.  **Save the changes** at your registrar. (Note: This change can take **up to 24-48 hours to fully propagate** across the internet).
9.  Go back to your **Hosted Zone** in Route 53. You'll see the NS and SOA records already present.
10. Click **Create Record** to map your domain name to your EC2 instance's IP.
11. For the **Record name**:
    * Leave it blank to use the **Root Domain** (e.g., `myexamplewebsite.xyz`).
    * Enter `www` to use `www.myexamplewebsite.xyz`.
    * Enter any other subdomain (e.g., `blog` for `blog.myexamplewebsite.xyz`).
12. For **Record type**: Select **A - Routes traffic to an IPv4 address...** (📄). This is because our EC2 instance has an IPv4 public IP.
13. For **Value**: Paste the **Public IPv4 address** of your EC2 instance.
14. For **Routing policy**: Keep the default, **Simple routing**.
15. Click **Create records**.
16. **Verify:** After propagation (might take a few minutes or hours), open a browser and type your **Custom Domain Name** (e.g., `myexamplewebsite.xyz` or `www.myexamplewebsite.xyz`). It should now load your website! ✅

## Use Case 2: Global Service with Low Latency (Latency-Based Routing) 🗺️⏱️

* **Problem:** If you only have one server in one region (e.g., Stockholm, Europe), users physically close to that region (like in Europe) get a fast response (low latency). Users far away (like in Mumbai, India) will experience slower loading times (higher latency) because the data has to travel further.
* **Solution:** Deploy copies of your application (e.g., EC2 instances) in **multiple AWS Regions** (e.g., Europe and Asia).
* **Goal:** Route each user's request to the server located in the **closest region** to them to minimize latency.
* **Route 53 Routing Policy:** **Latency routing** helps achieve this.

### Practical Demo 2: Setting up Latency-Based Routing

1.  **Launch a second EC2 instance** in a **different AWS Region** (e.g., Mumbai/ap-south-1).
2.  Install a web server on this new instance, perhaps with content indicating its location (e.g., "Welcome to Apache Web Server from **Mumbai**").
3.  Go back to your **Hosted Zone** in Route 53.
4.  Edit the **A Record** you created earlier (or create new ones for the same domain name).
5.  Change the **Routing policy** from Simple to **Latency**.
6.  For the first IP address (e.g., the Europe instance's IP):
    * Enter its IP address in the Value field.
    * Select the **Region** where this instance is located (e.g., `eu-north-1` for Stockholm).
    * Give it a **Record ID** (e.g., `EU-Record`).
7.  Click **Add another value** or create a new record for the same domain name.
8.  For the second IP address (the Mumbai instance's IP):
    * Enter its IP address in the Value field.
    * Select the **Region** where this instance is located (e.g., `ap-south-1` for Mumbai).
    * Give it a **Record ID** (e.g., `Asia-Record`).
9.  Click **Create records** (or Save changes).
10. **Verify:** Access your domain name from different geographical locations (or use online tools that test from various locations). Route 53 will automatically route requests to the server in the region with the lowest latency for the user. 🗺️⏱️

## Adding Health Checks for High Availability (Disaster Recovery) 🩺🚨

* **Problem:** What happens if one of your regional servers (in the Latency setup) goes down? Route 53's Latency routing might still try to send users there.
* **Solution:** Use **Route 53 Health Checks** to monitor the health of your endpoints. If an endpoint becomes unhealthy, Route 53 can stop sending traffic to it, even with Latency routing.
* This is key for **Disaster Recovery** and providing **Zero Down Time** (or near-zero).

### Practical Demo 3: Setting up Health Checks & Failover

1.  Go to **Health Checks** in the Route 53 dashboard.
2.  **Create health check**.
3.  Give it a **Name** (e.g., `EU-Web-Server-Health-Check`).
4.  Choose **Monitor endpoint**.
5.  Specify the **Endpoint**: **IP address** of your first EC2 instance (e.g., Europe).
6.  Specify the **Protocol**: **HTTP**.
7.  Choose the **Region** where the health checker should perform the check (best to choose a region close to the endpoint, or select "Global" health checkers).
8.  (Optional: Adjust **Advanced configuration** like checking interval, e.g., 10 or 30 seconds).
9.  Click **Create health check**.
10. Repeat steps 2-9 to create a separate health check for your second EC2 instance (e.g., `Asia-Web-Server-Health-Check` for the Mumbai instance's IP).
11. Go back to your **Hosted Zone** -> **Records**.
12. **Edit** your Latency-based A records.
13. For each record (EU and Asia), find the option to **Associate Health Check**.
14. Select the corresponding **Health Check ID** you just created (e.g., select `EU-Web-Server-Health-Check` for the Europe record, and `Asia-Web-Server-Health-Check` for the Asia record).
15. Click **Save changes**.
16. **Demonstrate Failover:** Manually **Terminate** one of your EC2 instances (e.g., the Europe one).
17. Go back to the **Health Checks** list in Route 53. Wait a few seconds/minutes (depending on your check interval). The status of the health check associated with the terminated instance should change from `Healthy` (🟢) to `Unhealthy` (🔴).
18. **Verify Failover:** Access your domain name from a location that would normally route to the failed server (e.g., from Europe). Route 53 detects the endpoint is unhealthy via the health check and **automatically routes the traffic** to the other healthy endpoint (the Mumbai server), even though Mumbai might have slightly higher latency for that user. The website is still accessible! 🚀

## Types of DNS Records in Route 53 📄📋

Route 53 supports various record types:

* **A (Address) Record**: Maps a domain name to an **IPv4 address**. (Used in our demos).
* **AAAA (IPv6 Address) Record**: Maps a domain name to an **IPv6 address**.
* **CNAME (Canonical Name) Record**: Maps one domain name to **another domain name**. (e.g., `www.example.com` -> `example.com`). **Cannot be used for the root/apex domain** (`example.com`).
* **Alias Record**: An AWS-specific extension. Maps a domain name to **select AWS resources** (ELB, CloudFront distribution, S3 website endpoint, API Gateway, or *another record in the same hosted zone*). A major benefit is that it **can be used for the root domain** (unlike CNAME) and often provides free queries and automatic updates if the resource's underlying IP changes. (Demo showed example of selecting an S3 website endpoint alias).
* **MX (Mail Exchanger) Record**: Specifies the **mail servers** for your domain.
* **TXT (Text) Record**: Contains **text information** (e.g., for domain verification, SPF records).
* **NS (Name Server) Record**: Delegates a DNS zone to a list of authoritative **name servers**. (Automatically created by Route 53 for your hosted zone).
* **SRV (Service Location) Record**: Specifies location of services (e.g., for VOIP, instant messaging).

## Route 53 Use Cases (Summary) ✨🎯

* **Hosting Websites:** Point your custom domain to your web servers (EC2, S3, Load Balancers).
* **Load Balancing:** Distribute traffic across multiple endpoints using Weighted or Latency routing policies.
* **Disaster Recovery:** Automatically reroute traffic to a healthy backup site or server if the primary fails, using Failover routing and Health Checks.
* **Multi-Region Deployments:** Direct users to the closest server for the best performance using Latency or Geolocation routing.
* **Traffic Management:** Advanced routing policies to control how traffic is directed.

## Billing Summary for Route 53 💰📊

* **Billable Components** include:
    * **Hosted Zones:** Monthly fee per hosted zone.
    * **DNS Queries:** Charged per query (pricing tiers apply).
    * **Health Checks:** Monthly fee per health check.
    * **Domain Registration:** Yearly fee per registered domain.
    * Traffic Flow/Traffic Policies (if used).
* **No Free Tier:** Charges begin as soon as you start using the service.
* **Cost Varies:** Depends on your usage (number of queries, number of hosted zones, number of health checks) and the complexity of your configuration (e.g., traffic policies).

## Route 53 Offerings 🎁

* **Domain Name Registration**
* **Hosted Zones**
* **Health Checks**
* **Traffic Flow** (advanced routing policies)
* **Resolver** (for DNS resolution within VPCs and between on-premises networks and AWS)

## Cleanup is Important! 🗑️🧹

* If you were practicing and are done, **DON'T FORGET TO CLEAN UP** to avoid unexpected costs! 💸
* **Steps to Clean Up:**
    1.  **Delete Health Checks:** Go to Health Checks, select the ones you created, and delete them.
    2.  **Terminate EC2 Instances:** Go to the EC2 console in *each* region where you launched instances (e.g., Stockholm, Mumbai). Select the instances and terminate them.
    3.  **Delete Records in Hosted Zone:** Go to your Hosted Zone in Route 53. Select any records *you created* (A, AAAA, CNAME, etc. - NOT the NS and SOA records) and delete them. You must delete custom records *before* you can delete the hosted zone.
    4.  **Delete Hosted Zone:** Go back to the list of Hosted Zones, select the one you created, and delete it.
    * **Tip:** Use the EC2 **Global View** (under EC2 Dashboard) to see running instances across all regions to help find and terminate them.

This covers the key aspects of AWS Route 53 discussed in the transcript! 👍