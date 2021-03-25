AWS Bill Summary Report for each account
2020-02-27 v1.0

========================================
A. Package Contents
========================================
a. bill-summary-report.yml
b. bill-summary-report.zip
c. readme.md (this file)

===============================================================
B. Verification of Email Addresses in AWS Simple Email Service
===============================================================
1. Log into the master account, open Simple Email Service (SES) and go to Email Addresses.

2. Click on "Verify a New Email Address".

3. Enter the Email Adress and click on "Verify This Email Address".

4. You will get an email from AWS to confirm the email address.

5. Once it is confirmed, we can use this email address below as a parameter value (Step C. 4)

Note: This step has to be repeated for all the email addresses entered as a parameter value in the below step while creating a parameter (Step C. 4)

6. Check the email of organization master account is verfied or not in SES. If not, verify the master account id as well 
	e.g. skybase-master@enquizit.com

=========================================
C. Create Parameters in Parameter Store
=========================================
1. Log into the master account and go to Parameter store (Systems Manager -> Parameter Store).

2. Select "Create Parameter".

3. Enter Name as for e.g. /acme/billing/contact/{account number}. Note: The account number must be for the account you want the bill report for that Organization.
	e.g. /acme/billing/contact/123456789012

4. For the Value, enter the email address you want the report to be delivered through SES. If you have multiple recipients, enter the email separated by comma.
   Note: Please make sure the email address is verified in SES. If not, it will result in "Email address not verified" error.
   
5. Click "Create parameter".

Output: A parameter is created with the Name and Value mentioned above. This step has to be repeated for each account in an Organization. 
		For e.g. if you have 4 accounts, you have to create 4 different parameters.
		
===============================================================
D. Creation of S3 Bucket and deployment of Bill Summary Report
===============================================================
1. Create a bucket in the region where this package will be deployed - e.g. bill-summary-bucket (referred to as <bucket> below).

2. Upload the file bill-summary-report.zip to <bucket>.
	
3. Make sure that the bucket can be accessed by the master account.

4. Log into the master account and go to the CloudFormation service.

5. Select "Create stack" and choose the option "With new resources (standard)".

6. Choose "Template is Ready" and "Upload Template".

7. Click "Choose file" and upload the file "bill-summary-report.yml" included in the package.  Click "Next".

8. Enter a name for the stack.

9. Enter the parameters requested.  Each has an explanation. (For testing purpose, you can change the cron job to your local time. By Default the Cron
   is set to 9:00 AM  UTC every 3rd of the month)

10. Click "Next".

11. Click "Next".

12. Acknowledge that CFN will create IAM Resources and click "Create Stack".

Output: This template creates a lambda function, a CloudWatch event rule and a SNS topic.
		You can find the names in the output section of the CloudFormation stack.
		
===========================
E. Testing
===========================
1. Go to the CloudWatch. Select the event rule which got created above.

2. Change the cron job timing to your local time so that the lambda gets triggered and generates the bill summary report for each account for the previous month.

3. At the scheduled time, a report is generated and sent to the email address mentioned as a parameter value. 

4. The report consists of Total Bill Due and Bill Amount by each service for the previous month for each account.
