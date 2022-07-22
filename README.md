# Transcribing 1800s Women Diary Using AWS Textract, Lambda, and S3
## Summary
Hand writing recognition has been a hot topic in the recent years. Transcribing hand writings on hand-filled documents or even historical manuscripts is immensively helpful to many scholars in the humanity field. This summer, I have participated in Data+ program at Duke University to help the library build a tool to transcribe women diaries in their digital archive. I have chosen to utilize Amazon's Textract as my way to approach this problem. Amazon Textract is trained on millions of data and is desgined to tackle the HTR problem. There are three advantages to incorporate cloud computing into your project:   
- (1) The pipeline is automated, so once the end user uploads an image or pdf, the model will be triggered to run and the result will be sent back to them  
- (2)The user does not need to set up the required environment or install any packages, which is helpful to non-technical users  
- (3) Machine learning model is easily deployed to the cloud so that the end user does not need to worry about forking the files and training the model on their end. They will just either log into the AWS console or open up an URL.
This repository introduces you to build an automated tool on Amazon's AWS to fully automate the HTR process.
## 
## Step 1: Register your free AWS account
First of all, it is necessary for you to have an AWS account in order to set up correctly for this project.  
The free tier will be available for 12 months and you will need to have a credit card on file in order to enjoy all the free tier benefits.
Follow this link to register your free tier account: https://aws.amazon.com/account/sign-up
