# AWS-IAM

![image](https://github.com/InfoSecDion/AWS-IAM/assets/105241007/f34087b4-2aa6-4a57-809c-09821567df75)

<h1>Introduction</h1>
I played the part of an IAM Security Engineer in this project, where my main goal was to transition over 100 IT users from their on-premises setup to the Cloud for a healthcare organization. My responsibilities also included overseeing their IAM resources. We aimed to enhance security by introducing MFA (Multi-Factor Authentication) for their accounts, adhering to best security practices.
To streamline the process and eliminate the need for manual and repetitive tasks within the AWS console, such as creating 100 individual user accounts, I opted for Amazon's CloudShell service to automate these tasks.

Utilizing a combination of GitBash, the AWS CLI, AWS IAM, and a shell script, I successfully established the necessary five user groups for classifying the company's users. Subsequently, I assigned the appropriate policies to each group to ensure they had the correct access to their designated services.
As a next step, I uploaded the Excel spreadsheet containing their names and email addresses, which was provided by the HR team, to the CloudShell. I then executed the script required to place these users into their respective groups.
To bolster security measures, I also implemented an MFA policy, requiring users to enter a code before accessing any services. You can find more details below:

<h1>Prerequisites:</h1>

- AWS Account
- Google Authenticator App


As we are organizing the users into five distinct groups, let's navigate to the AWS IAM service and proceed with their creation, accompanied by their corresponding policies. Here are the details:

-  CloudAdmin: Equipped with the AdministratorAccess policy.
-  LinuxAdmin: Assigned the AmazonEC2FullAccess policy.
-  NetworkAdmin: Endowed with AmazonVPCFullAccess policy.
-  DBA (Database Admin): Empowered with AmazonRDSFullAccess policy.
-  Trainees: Configured with the ReadOnlyAccess policy.

<h1>Deployment and Configuration Steps:</h1>

-  Click on User groups in IAM, then click on Create group:

![image](https://github.com/InfoSecDion/AWS-IAM/assets/105241007/64a2180d-7ffe-4e70-828e-80254c321297)


Name the group as shown above, then check the box of the correct policy:


![image](https://github.com/InfoSecDion/AWS-IAM/assets/105241007/95813e66-aa6e-4034-af3c-3ddb24724b93)


Search for another policy by the name IAMUserChangePassword. This will be explained later on, but for now select it for all of your groups:

![image](https://github.com/InfoSecDion/AWS-IAM/assets/105241007/52263896-2e6d-4597-99aa-1b8f3944d2af)


Afterwards, scroll down and click on Create group, then click on the group name to check and make sure you have the correct permissions policies:

![image](https://github.com/InfoSecDion/AWS-IAM/assets/105241007/c5f1e41d-04fa-40b4-8481-c7a4fc6239f7)


Do these steps for each user group that needs to be created, then let’s move on:

I was sent the spreadsheet of the users that will be migrated to the Cloud, and was able to fine-tune it for more manageability. I had three columns; A, B, and C. The first column was named user, and had their names separated with a period. The second column had their group that would correspond to the AWS user groups. The third column had a password I came up with that matches AWS’ password requirements. Everyone’s password was also the same. Going back to the reasoning behind the IAM password policy we attached to each of the groups, we want them to change their own password the moment they log into the console. Here’s an example of the spreadsheet I was given below. Feel free to create your own:

![image](https://github.com/InfoSecDion/AWS-IAM/assets/105241007/2643f4c1-042b-451e-a574-954fcbcf8976)


Save this file, and make sure it’s saved as a .csv file for better readability in the AWS CloudShell. Now let’s head over to the Amazon CloudShell service:

First, we want to install the dos2unix tool to be able to convert our .csv file into a UNIX-based format for the CloudShell. You can find this command here. Type it in below:

sudo yum install dos2unix -y

![image](https://github.com/InfoSecDion/AWS-IAM/assets/105241007/109899ae-8f4c-442a-b7cc-90d454aac30b)


After that, I created and downloaded the script to the shell that was needed to convert my .csv file into users and groups and gave myself executable permissions like so:

chmod +x <script_you_created>

After that, I uploaded the .csv file to the CloudShell by clicking on the Actions drop-down menu in the top-right and selecting Upload file:

![image](https://github.com/InfoSecDion/AWS-IAM/assets/105241007/77df928f-0c2b-4f61-8d76-15b86341c8c8)


Now that the contents are ready, let’s execute the script with the following command:

./<script_you_created> <name_of_uploaded_file>

![image](https://github.com/InfoSecDion/AWS-IAM/assets/105241007/376c078d-bd35-4799-8a11-0e29424919fe)


It will take a few minutes, but this will create all 100 of your users in the console in real-time. Now let’s head over to IAM and take a look at how it turned out:

![image](https://github.com/InfoSecDion/AWS-IAM/assets/105241007/f101b781-ef49-4581-bf3e-4580cdf9f54f)

![image](https://github.com/InfoSecDion/AWS-IAM/assets/105241007/4cd4e8fc-f0ea-4397-ae41-229b2f7cd8c0)


All users were created and automatically added to their respective groups. Now that this is complete, we want to create a policy to enable MFA for all of the users. Click on Policies, Create policy, and in the JSON tab create a policy that denies user access to any of the services unless they sign in using MFA, and allow access to services once they sign in with it. After that, click Next and name the policy:

![image](https://github.com/InfoSecDion/AWS-IAM/assets/105241007/7dced51c-b9fa-4e11-9f60-130d833de7f5)

Create the policy, then head over to your user groups and associate this policy with each one. We will now try to log in as one of our CloudAdmin users. Copy the IAM user URL on the dashboard of the IAM console, open an incognito window in your browser, and paste the URL into the address bar:

![image](https://github.com/InfoSecDion/AWS-IAM/assets/105241007/11617b38-b78a-403d-a6e7-8027e646dab1)

We will use the account ID shown in the URL, and use the cloud admins’ name along with the password in the Excel sheet, “ChangeMe123456!”:

![image](https://github.com/InfoSecDion/AWS-IAM/assets/105241007/dc447469-2890-40cb-88b1-c2dfba479949)

After you sign in, the request to change your password comes up. This is where the IAMUserChangePassword policy comes into play. We want this to activate the moment a user signs into their account. We’ll update the password then sign in:

![image](https://github.com/InfoSecDion/AWS-IAM/assets/105241007/5e92df75-d590-48cd-8960-b02f6adba074)

After signing in, we can see that we don’t have authorization to access anything. This is because we don’t have MFA set up yet:

![image](https://github.com/InfoSecDion/AWS-IAM/assets/105241007/83870128-8fd4-460a-ab9f-0ad423ef090c)

Click on the account name in the top-right, then click on Security credentials:

![image](https://github.com/InfoSecDion/AWS-IAM/assets/105241007/80d95f7f-e966-4504-810c-562b0884219c)

Let’s click on the Assign MFA button in our credentials tab:

![image](https://github.com/InfoSecDion/AWS-IAM/assets/105241007/bd9cbf3e-f56f-46ea-b56c-de159d5e6a75)

Name your device, and choose which device you want to use for authentication. I chose Google Authenticator as it’s easier for me since I already have accounts set up there. Click Next:

![image](https://github.com/InfoSecDion/AWS-IAM/assets/105241007/e081d4ce-4ac0-4361-b42b-c4bcfaec2283)

Go into the app and scan the QR code, then type in the consecutive codes the account gives you to activate it. Once that’s done, sign out of the AWS management console so the changes to your account take place. Sign in, and this time it should ask you for an MFA code. After that, you should be signed in, and have access to your required services. There are no errors as shown below when pulling up EC2, unlike before:

![image](https://github.com/InfoSecDion/AWS-IAM/assets/105241007/3b175ec5-ef01-479b-ba58-0db70681c8c4)

## Conclusion

I appreciate your time reading this! Inspired by an online blog post, I took on this project to hone my skills in IAM after a recent promotion. It's been a fantastic journey so far. I've learned a ton about the power of automation tools, especially with IAM and CloudShell. Creating lots of user accounts quickly is a game-changer. I'm excited about what's next, and I'll share more updates soon!
