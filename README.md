# **Build & Repeat: CI & CD with Twilio Studio Flows and Serverless Runtime Projects**


## **This repo and its accompanying instructions outline the pathway to setting up an end-to-end testing, integration and deployment workflow. We will be using bash scripts, the twilio CLI, APIs and some Node.js code.**

In our example, we will be starting a new Twilio project from scratch. The project will build a Customer Satisfaction Survey via SMS. You can see the architecture diagram below. 

![Screen Shot 2020-09-30 at 12 11 35 AM](https://user-images.githubusercontent.com/34189788/94625854-ae335a00-02b1-11eb-8479-a4ba4c58a566.png)

When using this repo as a reference for your future working, you can substitute the placeholder values for the account SID and auth token for your development, staging and production environments. 

In this instance, we’ve opted to use three subaccounts to simulate the movement from environment to environment. 

Note regarding external services: We will not be developing from the Strapi-side. Strapi’s headless CMS service will serve as an example for an external API-enabled database containing customer details i.e. name, job number etc. You can find more about Strapi in [their website](https://strapi.io/)


## **Download code repository**

Download your own local copy of this repo to your machine using `[.Zip ] `

We will call this copy's root folder 'csat-form-master' from now on.

Unzip the file into your local user directory. 

Navigate to the file in your directory and open in VSCode:


```
$ cd csat-form-master
$ code
```



## **Installation**

Firstly, start by installing the latest versions of Twilio CLI and Serverless Plugins:


```
$ npm install twilio-cli -g

$ twilio plugins:install @twilio-labs/plugin-serverless
```


If you already have Twilio CLI installed, run the following:


```
$ brew upgrade twilio-cli
```


Or if you’re a node developer running a version later than 10.0


```
$ npm i twilio-cli
```



## **Setup**

First up, login to you Twilio dev account via the command line:


```
$ twilio profiles:list
$ twilio profiles:use 
```


If you need to create a new profile from scratch in which to host your dev environment (recommended) then use the following command:


```
$ twilio profiles:create
```


You will need your Twilio Account SID and Auth Token for the above action

Before running the bash command from the directory’s root, make sure that you’ve copied the .env example files, and replaced them with the correct user credentials for both your originating account and your target account. 

Here’s a quick guide to which credentials/SIDs/tokens you should use in each instance:


<table>
  <tr>
   <td>Key
   </td>
   <td>Reference
   </td>
  </tr>
  <tr>
   <td><code>NGROK_TOKEN=1avsgIzuHYUIKppdmP6rG6gN2hy_ECXsCMBQUkLb7hsW49Kk</code>
   </td>
   <td>This is the token for the Ngrok account that allows us to set subdomain - note that this is not essential. You can configure your own non-custom ngrok tunnel for this process to work
   </td>
  </tr>
  <tr>
   <td><code>TESTER_URL=testerSignal2020</code>
   </td>
   <td>Ngrok subdomain that’s surfacing your testing server - change to suit your project name [for free account holders, see note above]
   </td>
  </tr>
  <tr>
   <td><code>flowSid/</code>
<p>
<code>flowSidSource=FWxxxxxxxxxxx</code>
   </td>
   <td>Unique identifier of the Studio flow that you’re trying to replicate
   </td>
  </tr>
  <tr>
   <td><code>accountSIDFlow=ACxxxxxxxxx</code>
   </td>
   <td>Unique identifier of the originating account that you’re cloning your Studio flow from
   </td>
  </tr>
  <tr>
   <td><code>authTokenFlow=xxxxxxxxxxx</code>
   </td>
   <td>Auth token for the originating account that you’re cloning your Studio flow from 
   </td>
  </tr>
  <tr>
   <td><code>targetAccountSid=ACxxxxxx</code>
   </td>
   <td>Unique account SID identifier of the project you’ve created for your dev environment 
   </td>
  </tr>
  <tr>
   <td><code>targetAccountToken=xxxxxxxxxxx</code>
   </td>
   <td>Auth token for the project you’ve created for your dev environment 
   </td>
  </tr>
  <tr>
   <td><code>LOGGER_SERVICE_URL=<a href="https://csat.ngrok.io/logs?token=abcdefghijk">https://csat.ngrok.io/logs?token=abcdefghijk</a></code>
   </td>
   <td>Can remain in lieu of your own logger service
   </td>
  </tr>
</table>


Save your newly updated “.env.dev” file and delete the example. 

Initialise the serverless plugin and create your new project to kick off the project structure creation. 

`$ twilio serverless:init myproject` **← Feel free to name your project whatever you want**

Navigate towards said project:


```
$ cd myproject
```


And initialise the server:


```
$ npm start
```


Now if you navigate to [http://localhost:3000/](http://localhost:3000/index.hmtl), your Twilio Serverless project should be live!

![Screen Shot 2020-09-22 at 6 59 51 PM](https://user-images.githubusercontent.com/34189788/94625884-c4411a80-02b1-11eb-9f68-beda12520f2c.png)

Next, you’ll want to delete the pre-made assets (_myproject > assets > _delete _index.html, message.private.js _and _style.css_), Function Templates (_myproject > functions > hello-world.js _and _private-message.js_) and the .twilio-functions file. 

With your project environment cleared up, it’s time to install your dependencies. In this instance, we’ll be using the following services:


```
$ npm install concurrently
$ npm install custom-env
$ npm install ngrok
$ npm install axios 
```


Then navigate back to the root directory, enter into the functions folder and copy the logger.js function into the functions folder of your newly created project.

Next, copy the folder bash_scripts to your new project folder, navigate to the root of the project, and then run this command: 


```
$ bash bash_scripts/deployDevEnv.sh
```


The above command will have deployed the logger function to your Twilio dev environment. 

Next, step into your Twilio console, to view your newly deployed service!

<img width="1230" alt="Screen Shot 2020-09-24 at 11 33 32 PM" src="https://user-images.githubusercontent.com/34189788/94625904-cefbaf80-02b1-11eb-8eaf-641dbf7167ee.png">

Jump into the Studio flow and assign the logger service to the endpoint within your Studio flow. You’ll need to make sure that your Studio Flow is completely free of any external URLs. 

<img width="827" alt="Screen Shot 2020-09-24 at 11 41 17 PM" src="https://user-images.githubusercontent.com/34189788/94625909-d0c57300-02b1-11eb-8122-e8e32d6028bc.png">

While you’re there, also update your environment, and function usage:

<img width="761" alt="Screen Shot 2020-09-24 at 11 44 22 PM" src="https://user-images.githubusercontent.com/34189788/94625914-d1f6a000-02b1-11eb-9880-7afca883c1bb.png">

And as always, hit **save **and **publish. **

With your function deployed to your dev environment, credentials updated and dependencies installed, you’ll lastly need to configure the phone number that you’ll be testing from (your one key static asset in this process) to the desired Studio flow that you’d like to alter. 

This can be done [in the console, under the numbers icon](https://www.twilio.com/console/phone-numbers/incoming) or via the CLI. 


## **Creating your Twilio testing account**

Now, in order to simulate user behaviour within your development environment, you’ll firstly need to create an additional account, purely for programmatic testing. 

You can do this by running the create command through the CLI:


```
$ twilio profiles:create testing
```


Next, head to the console to purchase a number. 

 \
As always, there’s a CLI shortcut for that! Run the below command replacing the placeholder with a number available to purchase. 


```
$ twilio api:core:incoming-phone-numbers:create \
  --phone-number="+[available phone number in e.164 format]"
```


You’ll need these details in the following section on testing to create a functioning test environment from which to alter your flow. 


## **Testing**

Copy across the testing service folder to your project.

At your first phase of testing, you’ll need to install the corresponding testing Library, which is in this case, Cypress. 

Once installed, you will have to clean the cypress structure from the example tests and copy tests from the original:


```
$ npm install cypress --save-dev
```


Copy across the testing service folder, located in the root directory to your new project.` `

Next, we need to import our pre-made tests, also stored in the root directory. Copy the contents of both the **cypress > integration **and **cypress > support **folders. 

These scripts will enable you to harness cypress’ GUI to test the functionality of your SMS flow and newly implemented logger service. 

 \
Before you run these tests, make sure to check the phone number variables in your code and ensure that the number referenced in your code matches the number configured to your flow within the numbers section of the console of your Twilio development account. 

And lastly, head to the package.json file within the repo, and copy across the below line of code to the scripts package within the package.json file within your project - and of course, hit save!


```
"test-dev": "concurrently --kill-others \"node test_service/server.js\""
```


 

The above script will use concurrently and custom-dev to run two separate Twilio instances. While one of these will of course be your flow, the other will be your newly-live testing service. 

For the following steps, you’ll need to have copied across the **test_service** folder to your project environment. 

This contains a file that spins up a server that will write the message body of the SMS messages that your Twilio testing phone number receives from your flow, and print them to the console. These console messages are surfaced using ngrok to make the testing service externally accessible. This externally accessible URL is referenced as the TESTER_URL throughout the dependency documentation. 

The correct deployment of your testing service is one of the central pillars of your automated testing, so make sure to go over this section carefully to ensure that you’re establishing a strong foundation for the rest of your project’s automated testing flow. 

Now, for Cypress to be able to test against your flow, you’ll need to copy the cypress.env example from the root and update it based on the below guide:


<table>
  <tr>
   <td>Key
   </td>
   <td>value
   </td>
  </tr>
  <tr>
   <td><code>accountSID</code>
   </td>
   <td>Unique account SID identifier of the project you’ve created for your testing environment account
   </td>
  </tr>
  <tr>
   <td><code>authToken</code>
   </td>
   <td>Unique auth token for your  testing environment account
   </td>
  </tr>
  <tr>
   <td><code>testAccountNumber</code>
   </td>
   <td>Phone number tied to testing account
   </td>
  </tr>
  <tr>
   <td><code>flowSid</code>
   </td>
   <td>Unique Flow SID identifier 
   </td>
  </tr>
  <tr>
   <td><code>accountSIDFlow</code>
   </td>
   <td>Account SID for the development account hosting the flow
   </td>
  </tr>
  <tr>
   <td><code>authTokenFlow</code>
   </td>
   <td>Auth token for the development account hosting the flow
   </td>
  </tr>
  <tr>
   <td><code>flowNumber</code>
   </td>
   <td>Phone number configured to the flow
   </td>
  </tr>
  <tr>
   <td><code>csatDB</code>
   </td>
   <td>External ngrok tunnel to the strapi database
   </td>
  </tr>
  <tr>
   <td><code>loggerDb</code>
   </td>
   <td>External ngrok tunnel to logger function
   </td>
  </tr>
</table>


Save the newly updated file within your project as **cypress.env.json**

Once you’ve incorporated these tests, run the below command to open cypress and commence the testing sequence:


```
$ node_modules/.bin/cypress open  
```


You’re now ready to run your tests. 

Your tests should show your testing number interacting with your studio flow and automate the entire process of a user going through both the HAPPY and UNHAPPY customer experience paths. 

This is the environment for you to develop your tests according to your user stories for the project at hand, and then work to develop your flow in a manner that meets your testing criteria. Remember - think testing and automation first. This will make your final build more robust and give it the strong foundation it needs to be continually repeated, improved and redeployed. 


## **Pushing to staging environment**

Now we’ve reached the staging environment, it’s important to remember that we’re working from the minimal amount of dependencies, to the maximum. 

In order to push to the staging environment, you’ll need to add the **studio **folder and its contents to your project. 

You’ll also need to update the **.env.staging **file using the previously provided key/value tables for reference in filling out the credentials and add this to your project folder. 

Firstly, ensure that your terminal is using your appropriate Twilio staging profile, and in doing so, surface the account details for this account.

In order to do this, run the following command: ` `


```
$ twilio profiles:list
```


And if you see the account you’d like to use for the staging portion of the build, use the below command:


```
$ twilio profiles:use <staging-env>
```


If not, you can go ahead and create this now by running the below:


```
$ twilio profiles:create staging
```


You can head to your [console](https://www.twilio.com/console) to grab your auth token and account SID for your staging environment and add these into your staging .env file, or alternatively use bash to export these variables within your programme:


```
$ export ACCOUNTSID_STAGING= 
$ export TOKENSID_STAGING=
```


Once you’re all ready, deploy your bash script to push you to the staging environment:


```
$ bash bash/deployStagingEnv.sh 
```


This is your most complex bash script. The script will select the correct Twilio profile to use, call fetchStudioFlow.js to push the flows and services from development to staging, and lastly, attach these to a phone number (you’ll see +12056198563 used throughout the example). The output of this script, along with the resultant SIDs, auth tokens and credentials are written to **logger-staging.out. **

Before going ahead to testing in your staging environment, you’ll have to make sure that your testing credentials listed in your project’s personal cypress.env.json file are all up to date. 

Next, head into the package.json folder, and copy the below line to the package.json file within your project folder:


```
"test-staging": "concurrently --kill-others \"node test_service/server.js\""
```


You should now have two testing scripts for each of your environments. 

Perform your staging environment tests. 


## **Pushing to the production environment**

For the final stage of your build, you’ll be pushing production-ready code. 

First up, if you don’t have your production profile set-up and ready to go, here’s a refresher on how to create that:


```
$ twilio profiles:list
$ twilio profiles:create production
```


Next, you’ll need to surface both the account SID and auth token and get these added to your new environment file. 

Use the .**env.production.example **as a guide:


<table>
  <tr>
   <td>Key
   </td>
   <td>value
   </td>
  </tr>
  <tr>
   <td><code>TESTER_URL</code>
   </td>
   <td>Externally accessible URL for your testing server 
   </td>
  </tr>
  <tr>
   <td><code>accountSID</code>
   </td>
   <td>Unique account SID identifier of the project you’ve created for your testing environment account
   </td>
  </tr>
  <tr>
   <td><code>authToken</code>
   </td>
   <td>Unique auth token for your  testing environment account
   </td>
  </tr>
  <tr>
   <td><code>testAccountNumber</code>
   </td>
   <td>Phone number tied to testing account
   </td>
  </tr>
  <tr>
   <td><code>flowSid</code>
   </td>
   <td>Unique Flow SID identifier 
   </td>
  </tr>
  <tr>
   <td><code>accountSIDFlow</code>
   </td>
   <td>Account SID for the staging account hosting the flow
   </td>
  </tr>
  <tr>
   <td><code>authTokenFlow</code>
   </td>
   <td>Auth token for the staging account hosting the flow
   </td>
  </tr>
  <tr>
   <td><code>flowNumber</code>
   </td>
   <td>Phone number configured to the flow
   </td>
  </tr>
  <tr>
   <td><code>csatDB</code>
   </td>
   <td>External ngrok tunnel to the strapi database
   </td>
  </tr>
  <tr>
   <td><code>loggerDb</code>
   </td>
   <td>External ngrok tunnel to logger function
   </td>
  </tr>
</table>


With your environment ready to go, navigate to the root of your project and run the final deployment script to push to production 😎


```
$ bash bash_scripts/deployProductionEnv.sh
