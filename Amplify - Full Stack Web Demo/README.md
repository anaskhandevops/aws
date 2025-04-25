# AWS Amplify: Build, Deploy, Host Full-Stack Apps ⚡🏗️

This section covers **AWS Amplify**, a service that simplifies building, deploying, and hosting websites and mobile applications, including complex full-stack apps. We'll do several practical demos!

## Pricing Note 💰

* AWS Amplify has a **Free Tier** available for building, deploying, hosting, data storage, data transfer, and more. You can experiment comfortably within the free tier limits. Check the pricing page for details.

## What is AWS Amplify? ✨

* Amplify is a **platform** that makes it much easier to **Build, Deploy, and Host** your web and mobile applications.
* It helps with both the **Frontend** (what users see) and the **Backend** (servers, databases, user authentication, etc.).

## Section 1: Hosting a Static Website (Simple & Continuous Deployment) 🌐💾🔄

Let's start with a simple static website hosted on GitHub.

1.  **Prepare your website:** Have your static website files (HTML, CSS, images) in a Git repository (like **GitHub**). The demo used a simple `index.html` and `style.css`.
2.  Go to the **AWS Amplify** console.
3.  Click **Deploy** -> **Host your web app**.
4.  Choose your **Git repository service** (e.g., **GitHub**).
5.  Click **Next**.
6.  **Authorize AWS Amplify to connect to your Git provider** (if it's your first time). You'll be redirected to GitHub to authorize Amplify to access your repositories. You can choose to grant access to all repositories or select specific ones.
7.  Once connected, select the **Repository** and the **Branch** you want to deploy (e.g., `main`).
8.  Click **Next**.
9.  Give your app a **name** (e.g., `My-Static-Website`).
10. Review the settings and click **Save and deploy**.
11. Amplify will automatically pull the code, build the app (though static sites often don't need a build step), and deploy it.
12. Watch the deployment process (Provision -> Build -> Deploy -> Verify).
13. Once deployed, Amplify provides a unique **App URL** (e.g., `https://main.randomstring.amplifyapp.com`). Click this URL to see your deployed website! ✅
    > *This shows how easy it is to get a website from Git to a live URL.*

### Continuous Deployment with Git Push 🚀

* A great feature is **Continuous Deployment**.
* Make a change to your website code **locally**.
* **Commit** and **Push** the changes to your Git repository (the branch connected to Amplify).
* Go back to the Amplify console. Amplify automatically **detects the code change** in your Git repository.
* It starts a new **deployment process** automatically for the updated code.
* Once the new deployment is complete, **refresh your App URL** in the browser. You will see the updated version of your website instantly! 🔄
    > *This saves a lot of manual effort for updates.*

## Section 2: Custom Domains and Certificates 🌐🔒

You don't have to use the `amplifyapp.com` URL. You can use your own domain name.

1.  In your Amplify app settings (left menu), click **Custom domains**.
2.  Click **Add domain**.
3.  If you use **Route 53** for your domain, select it from the dropdown. If your domain is registered in Route 53, it should appear here.
4.  If your domain is registered with a **different provider** (like Hostinger, GoDaddy):
    * You need to go to that provider's website.
    * In their DNS/Name Server settings, change the **Name Servers** to the ones provided by **AWS Route 53**.
    * First, you need to **Create a Hosted Zone** for your domain in Route 53. This Hosted Zone provides the AWS Name Servers you need.
    * Update the Name Servers at your registrar to point to the AWS Route 53 Name Servers. (This tells the internet that Route 53 is the authority for your domain).
    * This change can take 24-48 hours to fully update across the internet.
5.  Once your domain is properly configured to use Route 53 (either by registering with R53 or updating nameservers elsewhere), go back to Amplify -> Custom domains -> Add domain. Your domain should now be recognized.
6.  Follow the steps to configure your domain and subdomains (like `www`). Amplify often handles the **SSL Certificate** (for HTTPS) automatically using Amazon Certificate Manager (ACM), providing a secure connection. 🛡️
7.  After setup and propagation, you can access your website using your **own domain name**! ✅

## Section 3: Hosting a Full-Stack App (React To-Do Demo) 💻💾⚙️

Amplify shines with full-stack applications that include frontend, backend, database, and authentication.

### Getting Sample Code 📄

* AWS provides sample full-stack apps to get started.
* In the Amplify console, you can find options like "Create new app" -> "Host your web app" -> "Use a sample".
* Alternatively, you can often find sample templates on AWS documentation or GitHub (e.g., an Amplify React To-Do template).
* The demo used a template to **Create a repository from a template** directly in GitHub (e.g., `amplify-react-template`).

### Deploying the Full-Stack App 🏗️🚀

1.  In the Amplify console, click **All apps** -> **Create new app** -> **Host your web app**.
2.  Choose **GitHub** (or your Git provider).
3.  **Update Git Permissions** in Amplify (if you created a new repo or need to grant access to the specific repo).
4.  Select the **Repository** you just created from the template (e.g., `amplify-react-template`).
5.  Amplify can often **auto-detect** the frontend framework (like React) and the **build settings** (like `npm run build`). Review or adjust these settings.
6.  Click **Next**.
7.  Give your app a name (e.g., `Amplify-React-App`).
8.  Review and click **Save and deploy**.
9.  Amplify now **deploys both the frontend and the backend resources** needed for the app. This includes provisioning services like DynamoDB, Lambda, and Cognito.
10. Once deployed, click the **App URL**. You'll see the sample To-Do application running! ✅

### Understanding the Backend Resources 💾 Lambda 🔑

* Amplify doesn't just host your frontend; it provisions and manages your **backend** too based on the code and configuration in your repository.
* In your Amplify app details (left menu), look for **Backend environments** -> **Deployed backend resources**.
* This shows you the AWS services that Amplify provisioned for your app:
    * **DynamoDB:** A NoSQL database used to store the To-Do items. You can see the tables created by Amplify.
    * **Lambda:** Serverless functions often used for API logic. Amplify creates functions as needed.
    * **Cognito:** A service for user authentication (signup, login). Amplify uses it to manage users.
    * Other services might be provisioned depending on the template/features used.
* You can see and manage these services directly in the AWS console, but Amplify manages their connection to your app.

## Section 4: Local Development & Connecting to the Cloud 💻🔗☁️

You can work on the app code locally and connect it to the backend resources running in AWS.

### Setting up Local Environment 🛠️

* You need **Node.js** and **Git** installed on your computer.
* Clone the GitHub repository (the one created from the template) to your local machine using `git clone`.
* Open the project in a code editor (like VS Code).
* Open a terminal in the project folder and run `npm install` to download all necessary project dependencies.

### Connecting Local Code to Cloud Backend 🤝

Your local frontend code needs to know how to talk to the DynamoDB, Lambda, and Cognito services in AWS.

1.  In the Amplify console, go to your app (**Amplify-React-App**).
2.  Go to **Backend environments** -> **Deployed backend resources**.
3.  Find and click the **Download `amplify-outputs.json`** button (or similar wording). This file contains the connection details for your deployed backend resources.
4.  Place this downloaded file (`amplify-outputs.json`) in the **root folder** of your local project (the same folder as `package.json` and the `amplify/` folder).

### Running Locally 🏃‍♀️💨

1.  In your local project terminal, run the command to start the local development server (often `npm run dev` or `npm start`).
2.  Open your web browser and go to the **local URL** provided (e.g., `http://localhost:3000` or `http://localhost:5173`).
3.  You'll see the app running locally. **Test it!** Add new To-Do items.
4.  Go back to the Amplify console -> **Backend environments** -> **Data** -> **Manage data**. You should see the items you added from your *local* app appearing in the cloud database (DynamoDB)! ✅
    > *This shows your local code is successfully connected to the backend resources in AWS.*

## Section 5: Adding Features (Delete, Authentication) ✨🔑

Amplify makes it easier to add common features.

### Adding Frontend-Only Logic (Delete) 🗑️

* For simple features like deleting a To-Do item (which the backend might already support via the schema), you might only need to add code to your **frontend**.
* The demo showed editing files like `src/App.tsx` to add a delete button and logic that calls the backend's delete functionality.
* After adding the code locally, running `npm run dev` lets you test the delete functionality in your local browser.

### Adding Authentication (Login/Signup) 🔑🔐

* Adding user authentication (signup, login, password reset) is complex to build from scratch. Amplify integrates with **Cognito** to simplify this.
* Amplify provides **UI Components** (e.g., `Authenticator`) that you can easily add to your frontend code (e.g., in `src/App.tsx`).
* By wrapping your main app code with the `Authenticator` component, Amplify automatically adds the login/signup UI and handles the authentication flow with the backend (Cognito User Pool).
* After adding the Authenticator code locally and running `npm run dev`, when you access `localhost`, you will first see a **login/signup page**.
* You can **Create Account**, enter an email/password, and often receive a verification code via email (Configured by Amplify/Cognito). Enter the code to confirm the account.
* Then you can **Sign In** with the new account credentials. Once logged in, you see your app content.
* A **Sign Out** button is also often provided.
    > *This shows how quickly you can add secure user authentication using Amplify's components and backend integration.*

## Section 6: Testing Backend Changes (Cloud Sandbox) 🧪🏖️

When you want to make changes to your backend logic or data model, testing is crucial without affecting the main deployed app.

* **Why Sandbox?** If multiple developers are working, or you want to test experimental backend features, you don't want to deploy potentially broken changes to the main production or development backend that others are using.
* **Cloud Sandbox:** Provides a **temporary, isolated backend environment** for your local development. It creates *new* temporary DynamoDB tables, Lambda functions, etc., just for you.
* **How to Use Sandbox:**
    1.  Open a terminal in your local project folder.
    2.  Run the command `npx amplify sandbox`.
    3.  Amplify creates your temporary backend resources in the cloud.
    4.  Crucially, it **overrides** your local `amplify-outputs.json` file to point your local code to these *temporary* sandbox resources instead of your main deployed backend resources.
    5.  Run your app locally (`npm run dev`) in a separate terminal. Your local app is now connected to the sandbox backend.
* **Making Backend Changes (Per-User Data Demo):**
    * The demo showed editing a backend schema file (e.g., `amplify/data/resource.ts`).
    * By adding an `@auth` rule to the To-Do data model schema, the backend is configured so that each user only sees *their own* To-Do items.
    * Save the backend schema changes. The sandbox detects the change and updates the temporary backend resources.
    * Also, update the frontend code (e.g., `src/App.tsx`) to show the logged-in user's name and fetch data specific to that user.
    * When running `npm run dev` *while the sandbox is active*, the app now requires login, and the To-Do list is unique for each user who signs up in the sandbox environment.
* **Sandbox Data:** Data added in the app connected to the sandbox is stored in the *temporary* sandbox database and **does not appear** in the Data Manager for your *main* deployed app.
    > *Sandbox is great for safely developing and testing backend features locally before merging and deploying to the main environment.*

## Section 7: Deploying Local Code Changes (Frontend Update) 🚀🔄

Once you're happy with your local code changes (especially frontend ones, and assuming backend changes requiring a full Amplify CLI push are handled separately), you deploy them.

1.  Stop your local development server and the sandbox (if running).
2.  **Commit** all your local code changes to Git (`git add .`, `git commit -m "Your message"`).
3.  **Push** your committed changes to your GitHub repository (`git push`).
4.  Amplify detects the push to the connected branch and automatically starts a **new deployment** for your main app.
5.  Once the deployment is complete, refresh your main **App URL**. The deployed website now has the features you added locally (like the delete button, login/signup flow). ✅

## Section 8: Cleanup 🗑️🧹

To avoid incurring costs, remember to remove the resources you created after you're done.

1.  **Delete Sandbox:** If you created a sandbox, delete it. This can usually be done from the Amplify console under **Manage sandboxes** or by running `npx amplify sandbox delete` in your local project.
2.  **Delete Amplify Apps:** Go to your Amplify console, select an app (e.g., `My-Static-Website` or `Amplify-React-App`), go to **App settings** -> **General** -> **Delete app**. Confirm by typing the app name or `delete`.
    > *Deleting the Amplify app deployed using the 'Host your web app' workflow (including backend environments managed by Amplify CLI) will also automatically delete the associated backend resources (DynamoDB, Lambda, Cognito, etc.).*
3.  **Delete Route 53 Hosted Zone:** If you created a new Hosted Zone in Route 53 for your custom domain, delete it. First, **delete all records** *within* the Hosted Zone (except the default NS and SOA records). Then, go back to the Hosted Zones list and delete the Hosted Zone itself.
    > *Your domain registration remains separate at your registrar.*

This comprehensive look at AWS Amplify shows its power for simplifying the full web and mobile app development lifecycle on AWS! 🎉