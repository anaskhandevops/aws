# AWS CloudFront: Content Delivery Network (CDN) ⚡🌐

This section talks about **Amazon CloudFront**. We've already seen how to host static websites on EC2 or S3. Now, let's improve their **global performance**.

## The Challenge: Global Website Performance 🌍⏱️

* Imagine your website is hosted in just one AWS Region (e.g., Mumbai).
* If a user accesses your website from far away (e.g., Europe), they will experience **performance issues** and **slow loading times** (high latency).
* This is especially true for websites with lots of graphics, high-resolution images (🖼️), videos (📹), detailed styling (CSS), or scripting.
* The challenge is to serve your website content globally with **Low Latency** and provide a better user experience.

## Previous Solution & Its Drawback 🤔💸

* In the last section (Route 53), we saw a solution for low latency by having multiple EC2 instances in different regions (e.g., Mumbai and Stockholm) and using Route 53's Latency routing.
* **Drawback:** Running **more EC2 instances increases your cost** significantly. This might not be the most cost-effective solution, especially for static content.

## Introducing CloudFront: The Solution! ✨

* **CloudFront** is a **Content Delivery Network (CDN)** service by AWS.
* Its main job is to **Speed Up** the delivery of your website content.
* How? By **Caching** (storing temporary copies of) your website content at **Edge Locations**.
* **Edge Locations** are data centers located all over the world, closer to your users than your main server (Origin).

## How CloudFront Works (Caching at Edge Locations) 💾📍

* Without CloudFront: Users access your website content directly from your **Origin** (where it's stored, e.g., an EC2 instance or S3 bucket).
* With CloudFront:
    * You deploy your website to your **Origin** (e.g., S3 bucket).
    * You create a **CloudFront Distribution** for that Origin.
    * When a user accesses your website for the **First Time** (or the first time in a while from a specific location):
        * Their request goes to the **closest Edge Location**.
        * The Edge Location **does not have the content cached** yet.
        * It fetches the content from your **Origin** (e.g., your S3 bucket in Mumbai).
        * It serves the content to the user **AND stores a copy** (caches it) at that Edge Location.
    * When **Subsequent Users** access your website from the *same general area* near that Edge Location:
        * Their requests also go to the closest Edge Location.
        * The Edge Location **already has the content cached**.
        * It serves the content directly from the **cache** at the Edge Location, which is much faster because it's geographically closer.
* This process significantly **reduces latency** and improves performance.

### What Kind of Content is Cached? 🤔

* CloudFront primarily caches **static content** like:
    * Images (🖼️)
    * CSS files (🎨)
    * JavaScript files (💻)
    * Videos (📹)
* It can also cache **dynamic content** like HTML files or API responses, depending on configuration.
* **By default**, sensitive or user-specific data and backend logic are **not cached** for security reasons.

## CloudFront's Global Network (Edge Locations) 🗺️📍

* CloudFront has a vast network of **Points of Presence (POPs)**, which are the Edge Locations where content is cached.
* It also has **Regional Edge Caches**, which are larger caches located between POPs and Origins. They hold more content than individual POPs.
* There are also **Embedded POPs** within Internet Service Providers (ISPs) networks for even better performance.
* This distributed network ensures content is delivered from locations very close to almost any user in the world.

## Practical Demo: Setting up CloudFront for an S3 Website ✅🏗️

### Prerequisite: Website on S3

* We used a simple static website with many images.
* **Steps to put website on S3:**
    * Created an **S3 Bucket** (e.g., `mywebsite-30122024`).
    * In Bucket **Permissions**, turned off **"Block Public Access"** settings (⚠ Be careful with this, ensure you understand the risks if not using CloudFront).
    * **Uploaded** all website files (index.html, images, CSS, etc.) to the bucket.
    * Note: Directly accessing the S3 object URL showed the HTML but not images because the *objects themselves* weren't publicly accessible, which is okay when using CloudFront as the access method.

### Creating a CloudFront Distribution

1.  Go to the **CloudFront Console**.
2.  Note the **Free Tier** benefits (1TB data transfer out per month).
3.  Click **Create CloudFront Distribution**.
4.  For **Origin domain**, click the input box. CloudFront will list available AWS resources like S3 buckets. Select your S3 bucket (`mywebsite-30122024.s3.amazonaws.com`).
5.  For **Origin access**, choose **Recommended (Origin access control)**.
6.  Click **Create new OAC** (Origin Access Control). Keep the recommended settings and click Create.
7.  CloudFront will tell you to **Update the bucket policy** for the S3 bucket. It provides the policy text you need to copy.
8.  Keep the rest of the distribution settings mostly default (e.g., Allowed HTTP Methods, Caching Policy).
9.  For **Price class**, select **Use All Edge Locations (Best performance)** for global reach.
10. For **Default root object**, enter `index.html` (this is the file CloudFront should request when someone accesses the root of your domain/distribution URL).
11. Click **Create distribution**.

### Updating the S3 Bucket Policy 🛡️📂

1.  Go to your **S3 Bucket** in the S3 console.
2.  Go to the **Permissions tab**.
3.  Scroll down to **Bucket policy** and click **Edit**.
4.  **Paste** the bucket policy text that CloudFront provided into the editor. This policy gives your specific CloudFront distribution permission to read objects from this S3 bucket.
5.  Click **Save changes**. (You should see "Successfully edited bucket policy").

### Deployment and Verification 🚀✅

1.  Go back to the **CloudFront Distributions** list.
2.  Your new distribution status will be **Deploying** (🟡). This takes some time (5-20 minutes or more) as AWS configures the distribution across its Edge Locations.
3.  Once the status changes to **Deployed** (🟢), you can test it.
4.  Note the **Distribution domain name** (e.g., `d123example.cloudfront.net`). This is the URL you'll use to access your website via CloudFront.
5.  Open a browser and go to the **Distribution domain name**. (Demo used a VPN to simulate accessing from a specific region like the US).
6.  **First Access:** Open your browser's developer tools (usually F12) and look at the **Network tab**. Load the page and observe the loading times for the resources (images, etc.). Note the initial time it takes (e.g., 640 milliseconds in the demo). This is a "cache MISS" from the Edge Location, so it had to fetch from the Origin.
7.  **Subsequent Access:** **Refresh** the page multiple times. Watch the loading times in the Network tab. They should be significantly **faster** (e.g., dropping to 200 milliseconds or less in the demo). This is a "cache HIT" from the Edge Location, serving the content from a nearby cache. **This demonstrates the performance improvement!** ⚡
8.  You can also look at the **Monitoring** and **Analytics** tabs in the CloudFront distribution details to see cache hit/miss ratios and viewer locations over time (📊).

## CloudFront Concepts (More Detail) 🤔

* **Edge Location**: The primary place where content is cached and served to users. These are geographically distributed.
* **Regional Edge Cache**: A larger cache layer behind multiple Edge Locations in a region. Content less frequently accessed at POPs might be cached here.
* **Embedded Point of Presence (POP)**: Located within ISP networks to improve performance on the "last mile" to the user.

## Browser Caching vs. CloudFront Caching 🔄💾

* **Browser Caching:** Your web browser stores copies of visited websites. This helps *only you* on *your specific device* when you revisit a site.
* **CloudFront Caching:** The Edge Location caches content. This helps *all users* accessing the website from the geographical area covered by that Edge Location. It provides a better *first-time* experience for users in that area compared to just browser caching.

## Pricing for CloudFront 💰📊

* The main cost is for **Data Transfer Out** from CloudFront Edge Locations to your viewers. This is typically charged per GB.
* There are also costs for the number of **HTTP/S Requests** served by CloudFront.
* Data transfer from your Origin (e.g., S3) to CloudFront Edge Locations is also billable but usually at a lower rate.
* The CloudFront service itself has a **Free Tier** that is sufficient for testing and low-traffic sites.

## Cleanup is Essential! 🗑️🧹

* CloudFront distributions and S3 buckets cost money! **Remember to clean them up** after you're done experimenting. 💸
* **Steps to Clean Up:**
    1.  Go to the **CloudFront Distributions** list.
    2.  Select your distribution.
    3.  Click **Disable**. (This takes several minutes).
    4.  Wait until the status changes from `Disabling` to `Deployed` again, but it will show as Disabled.
    5.  Select the Disabled distribution and click **Delete**. Confirm deletion.
    6.  Go to your **S3 Bucket** in the S3 console.
    7.  Click on the bucket name.
    8.  Click **Empty**. Follow the steps to permanently delete all objects inside the bucket.
    9.  Go back to the S3 bucket list.
    10. Select the empty bucket and click **Delete**. Follow the steps to confirm the bucket name and delete the bucket.

This summarizes the CloudFront section and the practical steps shown! 🎉