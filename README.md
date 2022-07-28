## Transcribing 1800s Women Diary Using AWS Textract, Lambda, and S3
### Summary
Hand writing recognition has been a hot topic in the recent years. Transcribing hand writings on hand-filled documents or even historical manuscripts is immensively helpful to many scholars in the humanity field. This summer, I have participated in Data+ program at Duke University to help the library build a tool to transcribe women diaries in their digital archive. I have chosen to utilize Amazon's Textract as my way to approach this problem. Amazon Textract is trained on millions of data and is desgined to tackle the HTR problem. There are four advantages to incorporate cloud computing into your project:   
- (1) The pipeline is automated, so once the end user uploads an image or pdf, the model will be triggered to run and the result will be sent back to them  
- (2) The user does not need to set up the required environment or install any packages, which is helpful to non-technical users  
- (3) Machine learning model is easily deployed to the cloud so that the end user does not need to worry about forking the files and training the model on their end. They will just either log into the AWS console or open up an URL.  
- (4) Textract asynchronous mode allows user to upload up to 10GB of pdf files for transcription all at once.

**Here is a high level strucutre of this asynchronous machine learning pipeline:**
![image](https://user-images.githubusercontent.com/90075179/180444326-652c8576-4217-4c17-bc5d-5e181a580447.png)
A pdf file is uploaded to Amazon S3, then the first Lambda function will initiate the process and send the input file to the Textract service. The Textract will then send the output (JSON payload) back to another Lambda function which is responsible for cleaning the JSON payload, turning the JSON file to CSV, and storing the output in Amazon S3.  
This repository guides you to build an automated tool on Amazon's AWS to fully automate the HTR process.

### Step 1: Register Your Free AWS Account
First of all, it is necessary for you to have an AWS account in order to set up correctly for this project.  
The free tier will be available for 12 months and you will need to have a credit card on file in order to enjoy all the free tier benefits.
Follow this link to register your free tier account: [Register Here](https://aws.amazon.com/account/sign-up)
Once you have signed up, log into your console by clicking this link [Log In Here](console.aws.amazon.com).

### Step 2: Create Your S3 Bucket
Search S3 in the top search bar and then follow the instruction to create a new bucket named ***data-plus-textract*** leaving everything on default. Inside the S3 bucket you created, create three new folders named ***"async-doc-text"***, ***"csv"***, and ***"textract-output"***. The first ***async-doc-text*** folder will be the folder for you to upload your file, the second csv folder will be the output folder. You will be able to download the output csv files in this folder.  

### Step 3: Create SNS Topic
Search for ***Simple Notification Service*** in the console search bar at the top. Click ***Topic*** from the left panel. Then click ***Create Topic***. Choose ***Standard*** as the type, and put ***textract-async-notification*** in the name blank. Then click ***create***. In the later step, we will create subscription for this notification service.

### Step 4: Create IAM Roles for Granting Services Permissions
#### Create IAM Role for S3 and Textract
Search ***IAM*** in the search bar at the top. Click on ***Roles*** on the left panel, then click ***Create Role***. Head to ***Choose a use case*** and select ***Lambda***. Click Next and search ***AWSLambdaExecute*** and check the box. Also search for ***AmazonTextractFullAccess*** and check the box. Doing the above steps will give your Lambda functions permissions to access S3 and Textract. Click through till the review page. Name the role as ***lambda_textract_async*** then ***create the role***.  
#### Create IAM Role for SNS
Search ***IAM*** in the search bar at the top. Click on ***Roles*** from the left panel, then click ***Create Role***. Find ***Textract*** under ***Or select a service to view the case*** and click on it. Click ***Next Permission*** to proceed. Click through the next step buttons until the review page. Type ***textract_sns_asunc*** in the ***Role Name*** and create role. Once the role is created, open the role and click on ***Attach Policies***. Search ***AmazonSNSFullAccess*** and check the box. Then click ***Attach Policy***.  

### Step 5: Create your First Lambda function
Search ***lambda*** in the search bar and navigate to lambda in your console. Click ***Layers*** on the left panel. Click ***create layer*** and name the layer as ***pandas_3_9_textract*** and ***upload*** the pandas-39.zip file that can be found and downloaded [here](https://github.com/srcecde/aws-tutorial-code/tree/master/lambda-layers-package/python3.9). Choose ***x86_64*** as your compatible architectures and Python 3.9 as your compatible runtimes and create the layer. Go back to Lambda and click Create Functions to create functions. For the first function, name it ***textract_async_job_creation***, select python 3.9 as runtime, ***x86_64*** as architecture. Within ***Permission***, select ***Using an existing role***, and select ***lambda_textract_async*** that you created and then create the function. Copy and paste the code I provided in a Github file named after **lambda1_async_job_creation.py**. Then click ***Deploy***. Then you will need to click on ***Configurations -> Environment variables -> Edit -> Add environment variables*** and type ***OUTPUT_BUCKET_NAME*** in Key, ***textract_async_process*** in Value. Add another variable and type ***OUTPUT_S3_PREFIX*** in key, ***textract-output*** in Value. Save this configuration. Another environment variable key to add is ***SNS_TOPIC_ARN***. The value is the ***ARN*** you can find when you open the topic created before.  ![image](https://user-images.githubusercontent.com/90075179/181606258-c2740aee-5593-4f61-9d3a-95976f4961af.png) Finally, under the ***Function Overview***, click on ***Add trigger***, add ***S3***, and select the bucket name ***data-plus-textract***, type ***aync-doc-text/*** to ***Prefix***, and ***.pdf*** to ***suffix*** and click ***add***.  

### Step 6: Create the second Lambda function
Name this function as "textract-response-process" and select same runtime, architecture and permission as step 4. Create the function and copy and paste code in the Github file named "lambda2_textract_response_process.py" and click deploy. To add the pandas package, scroll down to "Layers" and click on "Add a layer". Select "Custom Layers" and select "pandas_3_9_textract" and add. Add environmental variables following the step 4. "Key: BUCKET_NAME, Value: textract-async-process, Key: PREFIX, Value : csv". Voila, you have successfully created your HTR machine learning pipeline on AWS!

### Performance and Demo
To use the tool that you just built, log into Amazon console once again and head to S3 bucket. Find the "async-doc-text" folder under the "data_plus_textract" bucket and upload your pdf file to be transcribed. Wait for a couple of minutes and head to the "csv/" folder to find your transcription excel sheet and download. Your transcription file is nicely named as the date and time you created this transcription job. [Here](https://www.youtube.com/watch?v=kJVBVuc06kI) is a demo video showcasing how you can use this AWS tool.  

Down below are the average metrics tested on all the documents that we transcribed. The **Character Error Rate (CER) is 19.83%**. The **Word Error Rate (WER) is 42.13%**. The **Fuzz Ratio is 87%**. Metric wise, Textract is performing well. Performance wise, it would take AWS 4 minutes and 12 seconds to transcribe a full diary that is about 200 pages sized 250 mb. 
