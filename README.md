## ask-airwatch

# Step-by-Step Guide to Build your very own Ask AirWatch Skill

Before we jump right in, here is a list of requirements that you need to build this skill and point it to your own AirWatch environment (We recommend first setting this up in your UAT AirWatch environment. However it is pretty harmless, just a bunch of GET commands currently. We have not designed any action commands yet.)

# Requirements Overview

* AirWatch environment
* AirWatch API URL
* AirWatch API Key
* AirWatch Admin Username and Password
* AirWatch Device Services URL (optional)
* AirWatch Secure Email Gateway URL (optional)
* Amazon Developer Account (free)
* Amazon Web Services, AWS
* Lambda service

# Let's Get Started

## Step 1. Setting up Your Alexa Skill in the Developer Portal

Skills are managed through the Amazon Developer Portal. You’ll link the Lambda function you to a skill defined in the [Developer Portal](https://developer.amazon.com/).

 1. Navigate to the Amazon Developer Portal. Sign in or create a free account (upper right). You might see a different image if you have registered already or the page may have changed. If you see a similar menu and the ability to create an account or sign in, you are in the right place.

  ![](https://s3.amazonaws.com/lantern-code-samples-images/how-to/devsignin.png)

 2. Once signed in, navigate to Alexa and select **"Getting Started"** under Alexa Skills Kit.

  ![](https://s3.amazonaws.com/lantern-code-samples-images/how-to/Getstartedask.png)

 3. Here is where you will define and manage your skill. Select **"Add a New Skill"**

  ![](https://s3.amazonaws.com/lantern-code-samples-images/how-to/AddnewSkill.png)

 4. Select an initial language you want to support, and then optionally add additional languages later if needed (in Step 7). Make sure the radio button for the custom interaction model is selected for “Skill Type”. Add the name of the skill. You can use “Ask AirWatch". Remember, when you create a skill that you will publish, you will use a name that you define for your skill. That name will be the one that shows up in the Alexa App. Add the invocation name.

   ![](https://s3.amazonaws.com/lantern-code-samples-images/how-to/skill_information.png)

 5. Now, notice you're in the Interaction Model section.

   ![](https://s3.amazonaws.com/lantern-code-samples-images/how-to/interactionmodel.PNG)

 6. TODO - Next, we need to define our skill’s Interaction Model. Let’s begin with the intent schema. In the context of Alexa, an intent represents an action that fulfills a user’s spoken request. Intents can optionally have arguments called slots. Note: You will need to define both custom slot type values and sample utterances in language that matches current language tab.

* Review the intent schema below. This is written in JSON and provides the information needed to map the intents we want to handle programmatically.  Copy/Paste the intent schema in the [GitHub repository here](https://github.com/alexa/skill-sample-nodejs-howto/blob/master/speechAssets/IntentSchema.json) to Intent Schema section in Developer Portal.

* You will see the intents for getting a recipe, and then a collection of built-in intents to simplify handling common user tasks. Help intent will handle any time the user asks for help, stop and cancel are built-in intents to make it easier for you to handle when a user wants to exit the application. For more on the use of built-in intents, go [here](https://developer.amazon.com/public/solutions/alexa/alexa-skills-kit/docs/implementing-the-built-in-intents).

* You will notice that the RecipeIntent has a custom slot containing "Items". This allows you to let the user provide a parameter and you can customize the response based on which "item" they choose. For example, for an Aromatherapy recipe skill, items could be "focus", "stress relief" or "relaxation".

```JSON
{
  "intents": [
    {
      "intent": "RecipeIntent",
      "slots": [
      {
        "name" : "Item",
        "type": "LIST_OF_ITEMS"
      }
     ]
    },
    {
      "intent": "AMAZON.RepeatIntent"
    },
    {
      "intent": "AMAZON.HelpIntent"
    },
    {
      "intent": "AMAZON.StopIntent"
    },
    {
      "intent": "AMAZON.CancelIntent"
    }
  ]
}
```
![](https://s3.amazonaws.com/lantern-code-samples-images/how-to/intent_schema.PNG)

 7. TODO - The next step is to build the utterance list.

    Given the flexibility and variation of spoken language in the real world, there will often be many different ways to express the same request. Providing these different phrases in your sample utterances will help improve voice recognition for the abilities you add to Alexa. It is important to include as wide a range of representative samples as you can -– all the phrases that you can think of that are possible in use (though do not include samples that users will never speak). Alexa also attempts to generalize based on the samples you provide to interpret spoken phrases that differ in minor ways from the samples specified.

    Now it's time to add the utterances. Select and copy/paste the sample utterances from [GitHub](https://github.com/alexa/skill-sample-nodejs-howto/tree/master/speechAssets) with your initial language. For example, if your select English (US) as initial language above, then you will need to copy/paste SampleUtterances_en_US.txt in previous link. An example of utterances is listed below. Once they are copied, the screen should look similar to the following image:

![](https://s3.amazonaws.com/lantern-code-samples-images/how-to/sample_utterances_us.PNG)

 8. TODO - For a How-to skill, we should also add the custom slot. Select “Add Slot Type”.
![](https://s3.amazonaws.com/lantern-code-samples-images/how-to/add_custom_slot_type.png)
In "Enter Type" type in "LIST_OF_ITEMS". We will also copy the 516 acceptable values based on this sample from our GitHub sample. Go to [GitHub](https://github.com/alexa/skill-sample-nodejs-howto/tree/master/speechAssets/customSlotTypes) and copy the list of values in your initial language. For example, choose **LIST_OF_ITEMS_en_US** for skill in English(U.S.).
![](https://s3.amazonaws.com/lantern-code-samples-images/how-to/custom_slot_types.png)


 9. Select **Save**. You should see the interaction model being built (this might a take a minute or two). If you select next, your changes will be saved and you will go directly to the Configuration screen. After selecting Save, it should now look like this:

    ![](https://s3.amazonaws.com/lantern-code-samples-images/how-to/interactionmodel_build_success.png)

Next we will configure the AWS Lambda function that will host the logic for our skill.

## Step 2: Creating Your Skill Logic Using AWS Lambda

### Installing and Working with the Alexa Skills Kit SDK for Node.js (alexa-sdk)

### Create an AWS Account

 ![](https://s3.amazonaws.com/lantern-code-samples-images/how-to/aws_home.png)

  1. Open [aws.amazon.com](https://aws.amazon.com/) and then choose **‘Create a Free Account’**

* Follow the online instructions. Do not worry about the IAM role, we will do that later.
* You will need a Valid Credit Card to set up your account (note the AWS Free Tier will suffice however. You can find out more about the free tier [here](https://aws.amazon.com/free/)).
* Part of the sign-up procedure involves receiving a phone call and entering a PIN using the phone keypad.

 2. Sign in to the AWS Console

  ![](https://drive.google.com/open?id=0B2IA2Y2pnQJzOFl4NVZxSnVvc1k)

 3. It can sometimes take a couple of minutes for your new AWS account to go live. You will receive an e-mail when your account is active.

### Create an AWS Lambda Function

AWS Lambda lets you run code without provisioning or managing servers. You pay only for the compute time you consume - there is no charge when your code is not running. With Lambda, you can run code for virtually any type of application or backend service - all with zero administration. Just upload your code and Lambda takes care of everything required to run and scale your code with high availability.

**Note: If you are new to Lambda and would like more information, visit the [Lambda Getting Started Guide](https://docs.aws.amazon.com/lambda/latest/dg/getting-started.html).**

 1. **IMPORTANT**: For Regions (upper right), select **US East (N. Virginia)** for US skills and **EU (Ireland)** for UK/DE skills. These are the only two regions currently supported for Alexa skill development on AWS Lambda, and choosing the right region will guarantee lower latency.

 ![](https://s3.amazonaws.com/lantern-code-samples-images/how-to/aws_region.png)

 2. Select **Lambda** from Compute services (upper left)

 ![](https://s3.amazonaws.com/lantern-code-samples-images/how-to/aws_lambda.png)

 3. Select **“Create a Lambda Function”** to begin the process of defining your Lambda function.

 4. TODO - In the **‘Select Blueprint’** page, filter on **'Alexa'**, select **“alexa-skill-kit-sdk-howtoskill”**

 5. Now, you need to configure the event that will trigger your function to be called. As we are building skills with the Alexa Skills Kit, click on the gray dash-lined box and select Alexa Skills Kit from the dropdown menu.

 ![](https://s3.amazonaws.com/lantern-code-samples-images/how-to/aws_lambda_ask.png)

 6. Choose **Next** to continue.

 ![](https://s3.amazonaws.com/lantern-code-samples-images/how-to/aws_next.png)

 7. You should now be in the **"Configure Function"** section. Enter the Name, Description, and select "Node 4.3" as the Runtime for your skill as in the example.

 ![](https://s3.amazonaws.com/lantern-code-samples-images/how-to/aws_config_function.PNG)

 8. Set your handler and role as follows:

    * Keep Handler as ‘index.handler’
    * Drop down the “Role” menu and select **“Create a new custom role”**. (Note: if you have already used Lambda you may already have a ‘lambda_basic_execution’ role created that you can use.) This will launch a new tab in the IAM Management Console.

 ![](https://s3.amazonaws.com/lantern-code-samples-images/how-to/aws_role.png)

 9. You will be asked to set up your Identity and Access Management or “IAM” role if you have not done so. AWS Identity and Access Management (IAM) enables you to securely control access to AWS services and resources for your users. Using IAM, you can create and manage AWS users and groups, and use permissions to allow and deny their access to AWS resources. We need to create a role that allows our skill to invoke this Lambda function. In the Role Summary section, select "Create a new IAM Role" from the IAM Role dropdown menu. The Role Name and policy document will automatically populate.

 ![](https://s3.amazonaws.com/lantern-code-samples-images/how-to/aws_role.png)

 10. Select **“Allow”** in the lower right corner and you will be returned to your Lambda function.


 ![](https://s3.amazonaws.com/lantern-code-samples-images/how-to/allowrole.png)

 11. Keep the Advanced settings as default. Select **‘Next’** and review. You should see something like below. Then select **‘Create Function’**:

 ![](https://s3.amazonaws.com/lantern-code-samples-images/how-to/CreateFunctionbuitton.png)

 12. Congratulations, you have created your AWS Lambda function. **Copy** the ARN for use in the Configuration section of the Amazon Developer Portal.

![](https://s3.amazonaws.com/lantern-code-samples-images/how-to/ARN.png)

## Step 3: Add Your Lambda Function to Your Skill

 1. Navigate back to [developer.amazon.com](https://developer.amazon.com/) and select your skill from the list. You can select the skill name or the edit button.

 ![](https://s3.amazonaws.com/lantern-code-samples-images/how-to/edit.png)

 2. Select the Configuration section. Add the ARN from the Lambda function you created in the AWS Console earlier. Select the **Lambda ARN (Amazon Resource Name)** radio button and tick the corresponding region. Then, select **“No”** for account linking since we will not be connecting to an external account for this tutorial. Paste the ARN you copied earlier into the Endpoint field. Then select **Next**. **Note:** the region(s) here should match the region(s) of your Lambda function(s).

 ![](https://s3.amazonaws.com/lantern-code-samples-images/how-to/configuration.png)

 3. You have now completed the initial development of your skill. Now it's time to test.

## Step 4: TODO - Testing Your Skill

 1. In the Test area, we are going to enter a sample utterance in the Service Simulator section and see how Alexa will respond. In this example, we have called the skill ‘Minecraft Helper’. This is the ‘Invocation Name’ we set up in the “Skill Information” section.

    * In the Service Simulator, type **‘how can I build a map’** and select **“Ask Minecraft Helper”**.

  ![](https://s3.amazonaws.com/lantern-code-samples-images/how-to/ask_minecraft.png)

 2. You should see the formatted JSON request from the Alexa Service and the response coming back. Verify that you get a correct Lambda response, and notice the card output. You will want to customize this output later.

  ![](https://s3.amazonaws.com/lantern-code-samples-images/how-to/servicessimulator.PNG)

 3. (Optional) Testing with your device. This is optional as you can do all the testing in the portal. Assuming your Alexa device is on-line (and logged in with the same account as your developer account), you should now see your skill enabled in the Alexa app and ask Alexa to launch your skill. For more information on testing an Alexa skill and registering an Alexa-enabled device, [check here](https://developer.amazon.com/public/solutions/alexa/alexa-skills-kit/docs/testing-an-alexa-skill).

### Not working (getting an invalid response)?
* Do you have the right ARN copied from your Developer Portal/Skill into your your Lambda function?
* Are you calling the right invocation name?
* Are you saying launch, start or open?
* Are you sure you have no other skills in your accounts with the same invocation name?
* For this template specifically, you should have a minimum of 20 recipes for a good customer experience.

## Step 5: TODO - Make it Yours - using the Lambda environment variables details



 11. Log back into your AWS console and upload the changes you have just made. First you will need to zip up the files into a new archive. You can do this by selecting the files that you need in the src directory (the node_modules directory and your updated index.js) into a new archive. Be sure that you compress the files in the folder, **not the folder itself**.

 12. Select your Lambda function and on the Code tab, select “Upload” to add the archive you just created.

 ![](https://s3.amazonaws.com/lantern-code-samples-images/how-to/upload_zip.png)

## Gathering Requirements

 1. To get your AirWatch API URL
    a. Log into the Admin Console at the Customer OG
    b. Navigate to Groups & Settings -> All Settings
    c. After Settings pop up, Navigate to System -> Advanced -> Site URLs
    d. Look for the REST API URL and copy just the https://sub.domain.com
    e. Do not copy the /API piece

 2. To get your AirWatch API Key
    a. Navigate to Groups & Settings -> All Settings
    b. After Settings pop up, Navigate to System -> Advanced -> API -> REST API
    c. Look for the API Key which corresponds with an Admin Service.
    d. AirWatch Admin Username and Password
    e. Create or use an existing AirWatch Admin Username that has at least read only rights to all OGs. 
  3. To get your AirWatch Device Services URL (optional, used just for health check purposes)
    a. Log into the Admin Console at the Customer OG
    b. Navigate to Groups & Settings -> All Settings
    c. After Settings pop up, Navigate to System -> Advanced -> Site URLs
    d. Look for the Device Services URL and copy just the https://sub.domain.com
    e. Do not copy anything after the .com (or .net, .org)

  4. AirWatch Secure Email Gateway URL (optional, used just for Health Check Purposes
    a. Log into the Admin Console at the Customer OG
    b. Navigate to Groups & Settings -> All Settings
    c. After Settings pop up, Navigate to Email -> Configuration
    d. Look for the Secure Email Gateway URL and copy just the https://sub.domain.com
    e. Do not copy anything after the .com (or .net, .org)


# Appendix 'Supported Phrases'

| Phrase | Input |  Output |
--- | :---: | ---
Ask AirWatch what is my device summary 
Ask AirWatch what's my device summary
Ask AirWatch device summary
Ask AirWatch environment summary
Ask AirWatch what is my environment summary
Ask AirWatch what's my environment summary
Ask AirWatch tell me about my environment | n/a | A high level overview of device counts in your environment.
Ask AirWatch How many {DeploymentDetail} devices I have 
Ask AirWatch Tell me the number of {DeploymentDetail} devices I have
Ask AirWatch Tell me how many {DeploymentDetail} devices I have
Ask AirWatch Give me the number of {DeploymentDetail} devices I have
Ask AirWatch Give me how many {DeploymentDetail} devices I have
Ask AirWatch count how many {DeploymentDetail} devices I have
Ask AirWatch What is the number of {DeploymentDetail} devices in the environment
Ask AirWatch What's the number of {DeploymentDetail} devices in the environment
Ask AirWatch what is the total number of {DeploymentDetail} devices
Ask AirWatch what's the total number of {DeploymentDetail} devices | total , employee owned , employee , corporate dedicated , corporate shared , byo , byod , compromised , no passcode , enrolled , valid , unenrolled , registered , undefined , not encrypted , android , iOS , apple , apple iOS , windows pc , mac os , mac , apple tv , windows mobile , windows phone 8 , windows phone rt , windows phone , blackberry 10 , chromebook | Detailed count of the {input} type of devices 
Ask AirWatch What is the status of the internal application {InternalAppDetail}
Ask AirWatch What is the status of the internal app {InternalAppDetail}
Ask AirWatch What's the status of the internal application {InternalAppDetail}
Ask AirWatch What's the status of the internal app {InternalAppDetail}
Ask AirWatch How is the deployment of the internal application {InternalAppDetail}
Ask AirWatch How is the deployment of the internal app {InternalAppDetail}
Ask AirWatch Tell me about the internal application {InternalAppDetail}
Ask AirWatch Tell me about the internal app {InternalAppDetail}
Ask AirWatch internal application {InternalAppDetail}
Ask AirWatch internal app {InternalAppDetail}  | Internal App Name | Number of apps with that name, their version, and how many devices they are assigned to and current installed on.
Ask AirWatch What is the status of the public application {PublicAppDetail}
Ask AirWatch What is the status of the public app {PublicAppDetail}
Ask AirWatch What's the status of the public application {PublicAppDetail}
Ask AirWatch What's the status of the public app {PublicAppDetail}
Ask AirWatch How is the deployment of the public application {PublicAppDetail}
Ask AirWatch How is the deployment of the public app {PublicAppDetail}
Ask AirWatch Tell me about the public application {PublicAppDetail}
Ask AirWatch Tell me about the public app {PublicAppDetail}
Ask AirWatch public application {PublicAppDetail}
Ask AirWatch public app {PublicAppDetail} | Public App Name | Number of apps with that name, their version, and how many devices they are assigned to and current installed on.
Ask AirWatch What is the status of the purchased application {VPPAppDetail} 
Ask AirWatch What is the status of the purchased app {VPPAppDetail}
Ask AirWatch What's the status of the purchased application {VPPAppDetail}
Ask AirWatch What's the status of the purchased app {VPPAppDetail}
Ask AirWatch How is the deployment of the purchased application {VPPAppDetail}
Ask AirWatch How is the deployment of the purchased app {VPPAppDetail}
Ask AirWatch Tell me about the purchased application {VPPAppDetail}
Ask AirWatch Tell me about the purchased app {VPPAppDetail}
Ask AirWatch purchased application {VPPAppDetail}
Ask AirWatch purchased app {VPPAppDetail} | Purchased(VPP) App Name | Number of apps with that name, their version, and how many devices they are assigned to and current installed on.
Ask AirWatch what version am I
Ask AirWatch what version are you running
Ask AirWatch what version am I running
Ask AirWatch what version is AirWatch
Ask AirWatch what version are you  | n/a | The current running version of AirWatch
Ask AirWatch how many devices have checked in the past {Days} days
Ask AirWatch how many devices have checked in the past {Days} days
Ask AirWatch how many devices have checked in the last {Days} day
Ask AirWatch how many devices have checked in the last {Days} days
Ask AirWatch how many devices have been seen in the past {Days} day
Ask AirWatch how many devices have been seen in the past {Days} days
Ask AirWatch how many devices have been seen in the last {Days} day
Ask AirWatch how many devices have been seen in the last {Days} days  | Any number greater than 0 | Number of devices checked in the past {input} days
Ask AirWatch is the Device Services server up
Ask AirWatch is the DS server up
Ask AirWatch is the DS healthy
Ask AirWatch is the Device Services server healthy
Ask AirWatch is the Device Services healthy
Ask AirWatch is the Device Services up | n/a | Health of DS endpoint with http code
Ask AirWatch is the API up
Ask AirWatch is the API server up
Ask AirWatch is the API healthy
Ask AirWatch is the API server healthy | n/a | Health of API endpoint with http code
Ask AirWatch is the Secure Email Gateway server up
Ask AirWatch is the SEG server up
Ask AirWatch is the SEG healthy
Ask AirWatch is the Secure Email Gateway server healthy
Ask AirWatch is the Secure Email Gateway healthy
Ask AirWatch is the Secure Email Gateway up  | n/a | Health of SEG endpoint with http code

## If you find yourself craving more Skills, check out these other great resources

* [Alexa Skills Kit (ASK)](https://developer.amazon.com/ask)
* [Developing Skills in Multiple Languages](https://developer.amazon.com/public/solutions/alexa/alexa-skills-kit/docs/developing-skills-in-multiple-languages)
* [Cody De Arkland's Integrating Amazon Echo and VMware APIs](https://www.thehumblelab.com/integrating-echo-vmware/)

