# Practical Demo: Building Your Custom VPC in AWS 💻🏗️

This section is a step-by-step guide showing you how to create your own **Virtual Private Cloud (VPC)** and its related networking parts like Subnets, Internet Gateway, and Route Tables in the AWS console.

## 1. Create Your VPC 🔒☁️⚙️

1.  Search for **VPC** in the AWS console search bar and click on the VPC service.
2.  Click the **Create VPC** button.
3.  Choose the option **VPC only** (this gives you more control over creating components step-by-step).
4.  Give your VPC a **Name tag** (e.g., `My-VPC`).
5.  For **IPv4 CIDR block**, enter your desired IP range (e.g., `10.0.0.0/16`).
    > *Recall: This is the big range of IP addresses for your VPC. `/16` gives you over 65,000 IPs. The range must be between /16 and /28.*
6.  Leave IPv6 CIDR block option as "No IPv6 CIDR block".
7.  Click **Create VPC**.
    > *You have now created your private network with a large pool of IP addresses!*

## 2. Create Subnets within Your VPC 🔪🧱⚙️

Subnets divide your VPC's IP range for different purposes (like public vs private).

1.  In the VPC dashboard (left-hand menu), click **Subnets**.
2.  Click **Create subnet**.
3.  For **VPC ID**, select the VPC you just created (choose `My-VPC`). The console shows its CIDR range.
4.  **Subnet 1: Public Subnet** 🌐
    * Give it a **Subnet name** (e.g., `Public-Subnet`).
    * For **Availability Zone**, select one from the list (e.g., `eu-north-1a` or `ap-south-1a`, depending on your region).
    * For **IPv4 CIDR block**, define a smaller range *within* your VPC's CIDR (e.g., `10.0.1.0/24`).
        > *Recall: `/24` gives you 256 IP addresses for this subnet.*
5.  **Subnet 2: Private Subnet** 🔒
    * Click **Add new subnet**.
    * Give it a **Subnet name** (e.g., `Private-Subnet`).
    * For **Availability Zone**, you can choose the same AZ or a different one.
    * For **IPv4 CIDR block**, define another range within your VPC's CIDR (e.g., `10.0.2.0/24`).
6.  Click **Create subnet**.
    > *You now have two smaller network segments (subnets) inside your VPC.*

## 3. Understand the Default Route Table 🚦✨

* When you create a new VPC, AWS automatically creates a **Default Route Table** for it.
* Initially, **all subnets** in your VPC are associated with this Default Route Table.
* By default, this route table usually only has a route allowing traffic to stay *within* the VPC's IP range. It typically does **not** have a route to the internet configured yet for a *new* custom VPC.

## 4. Create an Internet Gateway (IGW) 🚪🌍⚙️

This allows your VPC to connect to the internet.

1.  In the VPC dashboard, click **Internet Gateways**.
2.  Click **Create internet gateway**.
3.  Give it a **Name tag** (e.g., `My-IGW`).
4.  Click **Create internet gateway**.
5.  **Attach the IGW to your VPC:**
    * Select the IGW you just created (`My-IGW`).
    * Click the **Actions** button.
    * Select **Attach to VPC**.
    * Choose your VPC (`My-VPC`) from the list.
    * Click **Attach internet gateway**.
    > *Your VPC is now capable of connecting to the internet, but subnets aren't configured to use this connection yet.*

## 5. Create a Custom Route Table for Public Subnet 🚦🌐⚙️

We need a separate route table for the subnet that *should* access the internet.

1.  In the VPC dashboard, click **Route Tables**.
2.  Click **Create route table**.
3.  Give it a **Name tag** (e.g., `My-Public-Route-Table`).
4.  For **VPC**, select your VPC (`My-VPC`).
5.  Click **Create route table**.

## 6. Associate the Public Subnet with the Custom Route Table 🔗🤝

The Public Subnet needs to use `My-Public-Route-Table` instead of the Default one.

1.  Select the custom Route Table you just created (`My-Public-Route-Table`).
2.  Click the **Subnet associations** tab.
3.  Click **Edit subnet associations**.
4.  Find your `Public-Subnet` in the list and **check the box** next to it.
5.  Click **Save associations**.
    > *Now, `Public-Subnet` uses `My-Public-Route-Table`, and `Private-Subnet` still uses the Default Route Table.*

## 7. Add an Internet Route to the Public Route Table 🚦🚪

Tell the Public Route Table to send internet traffic to the IGW.

1.  Select your custom Route Table (`My-Public-Route-Table`).
2.  Click the **Routes** tab.
3.  Click **Edit routes**.
4.  Click **Add route**.
5.  For **Destination**, enter `0.0.0.0/0` (this means "all IP addresses outside the VPC").
6.  For **Target**, click the input box, select **Internet Gateway**, and then select your IGW (`My-IGW`).
7.  Click **Save changes**.
    > *Now, any traffic from `Public-Subnet` going to the internet will be sent to `My-IGW`.*

## 8. Visualize Your VPC Network 🗺️🧐

* Go back to the VPC dashboard.
* Click on **Your VPCs**.
* Select `My-VPC`.
* Look at the **Resource map**.
* You should see:
    * Your VPC (`My-VPC`).
    * Two subnets (`Public-Subnet` and `Private-Subnet`) inside it.
    * The `Public-Subnet` connected to `My-Public-Route-Table`.
    * `My-Public-Route-Table` connected to `My-IGW`.
    * `My-IGW` connected to the Internet icon.
    * The `Private-Subnet` connected to the Default Route Table (which does *not* have a route to the IGW).
    > *This map clearly shows how your network is set up and which subnets have internet access!*

## 9. Launch an EC2 Instance in Your Custom VPC/Subnet 🏢💻✅

Finally, let's launch a resource inside the network we built.

1.  Go to the **EC2 Console** and click **Launch instance**.
2.  Give your instance a name (e.g., `My-Web-Server`).
3.  Keep default settings for AMI, instance type, etc.
4.  Scroll down to **Network settings** and click **Edit**.
5.  For **VPC**, select your custom VPC (`My-VPC`) instead of the default.
6.  For **Subnet**, choose the subnet where you want the instance (e.g., `Private-Subnet` as shown in the demo).
7.  For **Auto-assign Public IP**, leave it disabled if you chose a Private Subnet (instances in private subnets don't get public IPs automatically). Enable it if you chose the Public Subnet and want a public IP.
8.  Configure Security Group or select an existing one (explained in the previous section).
9.  Configure key pair, storage, etc.
10. Click **Launch instance**.
11. After the instance launches, view its details.
12. Check the **Private IPv4 addresses**. The IP will be within the CIDR range of the **Subnet** you selected (e.g., `10.0.2.x` if you picked the Private-Subnet `10.0.2.0/24`). This confirms the instance was successfully launched inside your custom VPC and the correct subnet! 🎉

This practical guide shows you how to create and connect the fundamental components of your own private network in AWS.