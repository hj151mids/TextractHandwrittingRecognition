## Transcribing 1800s Women Diary Using AWS Textract, Lambda, and S3
### Summary
Hand writing recognition has been a hot topic in the recent years. Transcribing hand writings on hand-filled documents or even historical manuscripts is immensively helpful to many scholars in the humanity field. This summer, I have participated in Data+ program at Duke University to help the library build a tool to transcribe women diaries in their digital archive. I have chosen to utilize Amazon's Textract as my way to approach this problem. Amazon Textract is trained on millions of data and is desgined to tackle the HTR problem. There are three advantages to incorporate cloud computing into your project:   
- (1) The pipeline is automated, so once the end user uploads an image or pdf, the model will be triggered to run and the result will be sent back to them  
- (2) The user does not need to set up the required environment or install any packages, which is helpful to non-technical users  
- (3) Machine learning model is easily deployed to the cloud so that the end user does not need to worry about forking the files and training the model on their end. They will just either log into the AWS console or open up an URL.  
- (4) Textract asynchronous mode allows user to upload up to 10GB of pdf files for transcription all at once.

**Here is a high level strucutre of this asynchronous machine learning pipeline:**
![image](https://user-images.githubusercontent.com/90075179/180444326-652c8576-4217-4c17-bc5d-5e181a580447.png)
A pdf file is uploaded to Amazon S3, then the first Lambda function will initiate the process and send the input file to the Textract service. The Textract will then send the output (JSON payload) back to another Lambda function which is responsible for cleaning the JSON payload, turning the JSON file to CSV, and storing the output in Amazon S3.  
This repository introduces you to build an automated tool on Amazon's AWS to fully automate the HTR process.

### Step 1: Register your free AWS account
First of all, it is necessary for you to have an AWS account in order to set up correctly for this project.  
The free tier will be available for 12 months and you will need to have a credit card on file in order to enjoy all the free tier benefits.
Follow this link to register your free tier account: [Register Here](https://aws.amazon.com/account/sign-up)
Once you have signed up, log into your console by clicking this link [Log In Here](console.aws.amazon.com).

### Step 2: Create your S3 bucket
Search S3 in the top search bar and then follow the instruction to create a new bucket named ***"data-plus-textract"*** leaving everything on default. Inside the S3 bucket you created, create three new folders named ***"async-doc-text"***, ***"csv"***, and ***"textract-output"***. The first "async-doc-text" folder will be the folder for you to upload your file, the second csv folder will be the output folder. You will be able to download the output csv files in this folder.  

### Step 3: Create IAM roles for granting services permissions
Search IAM in the search bar at the top. Click on roles on the left panel, then click create role. Head to "Choose a use case" and select Lambda. Click Next and search "AWSLambdaExecute" and check the box. Also search for "AmazonTextractFullAccess" and check the box. Doing the above steps will give your Lambda functions permissions to access S3 and Textract. Click through till the review page. Name the role as "lambda_textract_async" then create the role.  

### Step 4: Create your Lambda function
Search lambda in the search bar and navigate to lambda in your console.                                                                                                                   
