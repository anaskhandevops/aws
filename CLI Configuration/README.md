# Setting Up AWS CLI 💻

This section explains how to install and briefly configure the AWS Command Line Interface (CLI) on your local computer.

## **Installing AWS CLI on Windows 🪟**

1.  Go to the official AWS CLI website: [https://aws.amazon.com/cli/](https://aws.amazon.com/cli/)
2.  Click on the link relevant to your operating system (e.g., "Install AWS CLI on Windows").
3.  Download the AWS CLI MSI installer (usually for 64-bit systems).
4.  Once the download is complete, double-click the `.msi` file to start the installation.
5.  Follow the on-screen instructions. In most cases, you can keep the default settings.
6.  Accept the terms and conditions.
7.  Click "Next" through the setup wizard and then "Install."
8.  Once the installation is finished, click "Finish."
9.  Open Command Prompt (CMD) by typing `cmd` in the Windows search bar and pressing Enter.
10. In the CMD window, type `aws` and press Enter. If the CLI is installed correctly, you will see a help message showing the basic usage of the `aws` command. This means your AWS commands are now working! 🎉

## **Verifying AWS CLI Installation ✅**

After installation, running the `aws` command in your terminal should display help information, confirming that the CLI is ready to be used.

## **Configuring AWS CLI Credentials 🔑**

To allow the AWS CLI on your computer to interact with your AWS account, you need to provide it with your AWS credentials. It's best practice to use the credentials of an IAM user, **not your root account**.

1.  Go to the IAM service in the AWS Management Console.
2.  Click on "Users" in the left-hand menu.
3.  Select the IAM user you want to use for CLI access (e.g., "Alex").
4.  Go to the "Security credentials" tab.
5.  Scroll down to the "Access keys" section and click "Create access key."
6.  AWS will ask for the use case. Select "Command Line Interface (CLI)" and acknowledge the recommendation about security best practices. Click "Next."
7.  You can add an optional description tag and then click "Create access key."
8.  You will now see your **Access key ID** and **Secret access key**. **This is the only time you will see the secret access key.**
9.  **Important:** Click "Download .csv file" to save these credentials in a file. You can also copy them and store them securely.
10. Open your terminal or command prompt.
11. Type the command `aws configure` and press Enter.
12. The CLI will prompt you for the following information:
    * **AWS Access Key ID:** Paste the access key ID you copied or found in the CSV file. Press Enter.
    * **AWS Secret Access Key:** Paste the secret access key. Press Enter.
    * **Default region name:** Enter the AWS region you will be working with most often (e.g., `us-east-1` for US East (N. Virginia)). You can find the region codes in the AWS documentation. Press Enter.
    * **Default output format:** You can enter `json`, `text`, or `table`. `json` is a common choice. Press Enter.

Your AWS CLI is now configured! 🚀

## **Testing AWS CLI 🧪**

Let's try a simple command to list your IAM users:

1.  In your terminal, type `aws iam list-users` and press Enter.
2.  If your credentials are configured correctly, you should see a JSON output containing information about your IAM users. If you encounter an error like "Unable to locate credentials," double-check your `aws configure` settings.

You have now successfully set up and tested your AWS CLI! You can use this powerful tool to manage various AWS services from your local environment.

## **Best Practices for IAM (Summary) 👍**

Here's a quick recap of important IAM best practices:

* **Avoid using the root account** for daily tasks. Use it only for initial account setup.
* **Add users to groups** and **assign permissions to groups** for easier management.
* **Create custom policies** to grant specific and least-privilege permissions.
* **Enforce password policies** to ensure strong passwords. You can configure these in the IAM console under "Account settings" -> "Password policy."
* **Enable Multi-Factor Authentication (MFA)** for all users, especially the root user, to add an extra layer of security.
* **Use access keys for CLI and SDK access** for programmatic interaction with AWS.
* **Never share access keys or passwords** and store them securely. Avoid committing them to version control systems.
* **Audit permissions regularly** using the IAM Credentials Report. You can download this report from the IAM console. It provides a snapshot of your users and their credentials status.
* Review the **Last Accessed** information for users and services to ensure permissions are still necessary. You can find this information on the user's details page.

By following these best practices, you can significantly improve the security of your AWS environment. 🛡️