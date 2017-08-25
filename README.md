# ask-airwatch

# Step-by-Step Guide to Build your very own Ask AirWatch Skill

For additional details and sample utterances, check out the [Virtual JPR blog post](http://www.virtualjpr.com/2017/08/ask-airwatch.html) on this effort.  Before we jump right in, here is a list of requirements that you need to build this skill and point it to your own AirWatch environment (We recommend first setting this up in your UAT AirWatch environment. However it is pretty harmless, just a bunch of GET commands currently. We have not designed any action commands yet.)

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

# Step 1: Gathering your AirWatch Environnment details

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

## Step 2. Setting up Your Alexa Skill in the Developer Portal

Skills are managed through the Amazon Developer Portal. You’ll link the Lambda function you to a skill defined in the [Developer Portal](https://developer.amazon.com/).

 1. Navigate to the [Amazon Developer Portal](https://developer.amazon.com/). Sign in or create a free account (upper right). 

  ![](https://drive.google.com/uc?export=&id=0B2IA2Y2pnQJzOFl4NVZxSnVvc1k)

 2. Once signed in, navigate to Alexa and select **"Getting Started"** under Alexa Skills Kit.

  ![](https://drive.google.com/uc?export=&id=0B2IA2Y2pnQJzMTZfREwyVXVqYU0)

 3. Select **"Add a New Skill"** to begin configuration of Amazon's Skill Wizard.

  ![](https://drive.google.com/uc?export=&id=0B2IA2Y2pnQJzYkF0NVdRZGVJczA)

 4. Select an initial language you want to support, and make sure the radio button for the custom interaction model is selected for “Skill Type”. Add the name of the skill - this will be the one that shows up in the Alexa App. Add the invocation name - this will be the one that you say to your Alexa devices. Click "Save" and "Next" to move to the Interaction Model configuration.

   ![](https://drive.google.com/uc?export=&id=0B2IA2Y2pnQJzY1dISFJsa1Z1NWs)

 5. Next, we will define the skill’s Interaction Model, which consists of 3 parts: intent schema, slot types, and sample utterances. These 3 definitions provide Alexa with the context of what to expect from a user's voice interaction. Let's start with the Intent Schema. Copy/Paste the [intent schema that we have provided for you](https://github.com/AskAirWatch/ask-airwatch/blob/master/intent-schema.json) to the Intent Schema section. You will see the intents for different functions that a user interaction will map to, as well as a collection of built-in intents. 

   ![](https://drive.google.com/uc?export=&id=0B2IA2Y2pnQJzTWNIU0FXU2FaZ1U)

 6. To allow the Skill to return user defined variables back to our Lambda processing function, we will need to configure a few custom slot types. Select “Add Slot Type”, then go to [GitHub](https://github.com/AskAirWatch/ask-airwatch/blob/master/custom-slot-detail.txt) and copy and paste each Custom Slot Type and accompanying Values from the file. ![](https://drive.google.com/uc?export=&id=0B2IA2Y2pnQJzMzBobTV1anJNOUk)
![](https://drive.google.com/uc?export=&id=0B2IA2Y2pnQJzal9YajEtRnZKNGs) 

 7. The next step is to build the utterance list to improve voice recognition of the functions we are configuring for Alexa. These samples funcntion as the training data set for Alexa to match what a user asks for and which intent Alexa maps that request to. Copy/paste the sample utterances [we have provided](https://github.com/AskAirWatch/ask-airwatch/blob/master/sample-utterances.txt). 

![](https://drive.google.com/uc?export=&id=0B2IA2Y2pnQJzNnVJRlhVbU5iZ28)

 8. Select **Save** and then **Next**. You should see the interaction model being built. Your changes will be saved and you should now see the Configuration screen. Now the skill is asking to link to the Service Endpoint, which will handle the processing and action sequence after Alexa returns the intent and slot values from the user's request.

    ![](https://drive.google.com/uc?export=&id=0B2IA2Y2pnQJzSmZPMEpISVdrb2s)

Next we will configure the AWS Lambda function that will host the logic for our skill.

## Step 3: Using AWS Lambda to Power the Logic behind the Skill

### Create an AWS Account

 1. Open [aws.amazon.com](https://aws.amazon.com/) and then **Sign In** or choose **‘Create a Free Account’**

 ![](https://drive.google.com/uc?export=&id=0B2IA2Y2pnQJzc2NLUm1vSWlfRVU)

 2. Sign in to the AWS Console

  ![](https://drive.google.com/uc?id=0B2IA2Y2pnQJzYTN5dGxKWEppN2M)

 3. You will receive an e-mail when your account is active.

### Create an AWS Lambda Function

**Note: If you are new to Lambda and would like more information, visit the [Lambda Getting Started Guide](https://docs.aws.amazon.com/lambda/latest/dg/getting-started.html).**

 1. **IMPORTANT**: For Regions (upper right), select **US East (N. Virginia)** for US skills and **EU (Ireland)** for UK/DE skills. These are the only two regions currently supported for Alexa skill development on AWS Lambda, and choosing the right region will guarantee lower latency.

 2. Select the **Services** menu and find **Lambda** under Compute services

 ![](https://drive.google.com/uc?id=0B2IA2Y2pnQJzQnlGZmhtYkhPTEE)

 3. Select **“Create function”** to begin the process of defining your Lambda function.

 ![](https://drive.google.com/uc?id=0B2IA2Y2pnQJzdWs3bGZSWHQ1NjQ)

 4. Because we have prepared the code for you, it is easy enough to start from scarth, so select **Author from scratch**

  ![](https://drive.google.com/uc?id=0B2IA2Y2pnQJzVVhxQTdkVS1Majg)
 
 5. Now, you need to configure the event that will trigger your function to be called. As we are building skills with the Alexa Skills Kit, click on the gray dash-lined box and select Alexa Skills Kit from the dropdown menu. Then choose **Next** to continue.

![](https://drive.google.com/uc?id=0B2IA2Y2pnQJzN2FObnVaVXU4MXM)

 6. You should now be in the **"Configure Function"** section. Enter the Name, Description, and select "Node 6.10" as the Runtime for your skill as in the example. For the **Lambda function code** select the **Upload a .zip file** option. 

 ![](https://drive.google.com/uc?id=0B2IA2Y2pnQJzMXhYeVBTUnVJcmM)

 7. [Go here](https://github.com/AskAirWatch/ask-airwatch/blob/master/AskAirWatch0.1.0.zip) and download the the zip folder of the code we have provided. Once downloaded from GitHub, navigate back to your Lambda function and upload the zip directly. 

 8. Set your handler and role as follows:
 
![](https://drive.google.com/uc?id=0B2IA2Y2pnQJzOVZNakJLblVZNW8)

 9. Now, you will be able to configure your AirWatch enviornment variables that were collected in **Step 1**. This will load the code that Lambda is running with your specific environment details. After this, you can link the Lambda function you just configured to the Alexa Skill.

![](https://drive.google.com/uc?id=0B2IA2Y2pnQJzM3FzQkctSTY3WjA)

 11. Keep the Advanced settings as default. Select **‘Next’** and review. You should see something like below. Then select **‘Create Function’**:

## Step 3: Add Your Lambda Function to Your Skill

 1. Navigate back to [developer.amazon.com](https://developer.amazon.com/) and select your skill from the list. You can select the skill name or the edit button.

 2. Select the Configuration section. Add the ARN from the Lambda function you created in the AWS Console earlier. Select the **Lambda ARN (Amazon Resource Name)** radio button and tick the corresponding region. Then, select **“No”** for account linking since we will not be connecting to an external account for this tutorial. Paste the ARN you copied earlier into the Endpoint field. Then select **Next**. **Note:** the region(s) here should match the region(s) of your Lambda function(s).

![](https://drive.google.com/uc?id=0B2IA2Y2pnQJzbEt4WWVycVh6NEE)

 3. You have now completed the initial development of your skill. You should be in the **Test** section of the setup, so take some time to do a little unit test and enter a test phrase in the **Service Simulator**. 
 
![](https://drive.google.com/uc?id=0B2IA2Y2pnQJzRXpNSGdWX2h3c1k)

### If all tests well, it's time to add this skill to your Alexa-enabled devices (including your Amazon Shopping App) and **AskAirWatch**! 

## If you find yourself craving more Skills, check out these other great resources

* [Alexa Skills Kit (ASK)](https://developer.amazon.com/ask)
* [Developing Skills in Multiple Languages](https://developer.amazon.com/public/solutions/alexa/alexa-skills-kit/docs/developing-skills-in-multiple-languages)
* [Cody De Arkland's Integrating Amazon Echo and VMware APIs](https://www.thehumblelab.com/integrating-echo-vmware/)

