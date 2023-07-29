**Instruction:**

Here's a step-by-step guide on how to create an Access Key and Secret Key in AWS:

Step 1: Log in to your AWS Account.

Step 2: Navigate to the IAM (Identity and Access Management) section in the Management Console by searching for IAM.

Step 3: On the left side of the panel, click on "User" to manage IAM users.

Step 4: Click on "Add Users" and provide the necessary user details.

Remember to choose "Programmatic access" as the Access type to generate the Access Key ID and Secret Key.

Step 5: Attach the appropriate policy to the user.

Once the user details are entered, attach a policy for S3 as shown below. You can proceed to the next step by clicking the "Next: Tags" button and optionally adding tags.

Step 6: Create the User.

Review the user policy you've set, and then proceed to create the user.

Upon successful IAM user creation, you'll receive a message with your Access Key and Secret Key. It is essential to store this information securely on your local computer since AWS doesn't allow retrieval of secret keys after creation.

Next, let's explore how to create an S3 bucket using Terraform.

Download Terraform, in case it does not exist already. You can use Homebrew for the installation. 

Now we need to access Terraform, .tf, and provide a secure connection to AWS to create an S3 bucket. This connection will be created through AWS Access Key and Secret Key. 

Here's the **main.tf **file, make sure this file is in the same folder where Terraform is been downloaded. 
```
provider "aws" {
  region = "us-east-2"  # Replace with your desired AWS region
}
 
resource "aws_s3_bucket" "my_bucket" {
  bucket = "scene-test2"  # Replace with a globally unique bucket name
  acl    = "private"  # You can set the ACL (Access Control List) as needed
 
  tags = {
    Name = "My S3 Bucket"
  }
}
```
To run this, the following commands need to be executed in the terminal box:

```
terraform init
```
If it gives the below message you can proceed to the next steps:
<img width="897" alt="image" src="https://github.com/SanikaMK/AWS-Glue/assets/88078801/cdfe12bf-87b2-4242-bf27-de26658e88d3">

If it gives out an error, the common error I faced was:
<img width="1023" alt="image" src="https://github.com/SanikaMK/AWS-Glue/assets/88078801/2902e02a-abe5-43af-a3c6-ce2372488912">

For this, you need to:
```
aws configure
brew install awscli
aws configure
```
Provide your AWS access key and secret key. Then run terraform init.

There is a chance you might stumble upon this error:
<img width="883" alt="image" src="https://github.com/SanikaMK/AWS-Glue/assets/88078801/57b70f64-c4ea-413e-947a-2803117ea15d">

To fix that, use this:
```
terraform init -migrate-state
```
Next Step is to apply to changes:
```
terraform apply
```
After this step you can see the S3 bucket being created on your AWS:
<img width="801" alt="image" src="https://github.com/SanikaMK/AWS-Glue/assets/88078801/e82b2bfe-e156-4d5d-8f24-99d127acdef6">

Now Upload the data that you chose from the Open Source. 










