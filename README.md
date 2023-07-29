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
The next step is to apply to changes:
```
terraform apply
```
After this step you can see the S3 bucket being created on your AWS:
<img width="801" alt="image" src="https://github.com/SanikaMK/AWS-Glue/assets/88078801/e82b2bfe-e156-4d5d-8f24-99d127acdef6">

Now Upload the data that you chose from the Open Source. 

To read a CSV file from an S3 bucket using AWS Glue, you can create a Glue job. Glue jobs allow you to perform data transformations and ETL (Extract, Transform, Load) operations on your data. Here's a step-by-step guide on how to read a CSV file from S3 using AWS Glue:

Step 1: Create an IAM Role for Glue Job
First, create an IAM role that grants necessary permissions to your Glue job. This role will allow Glue to access the S3 bucket containing the CSV file. You can do this through the AWS Management Console or by using AWS CLI or SDKs.

Step 2: Write the Glue Job Script
Write the Glue job script in Python or Scala, depending on your preference. For this example, let's use Python. Save the script as glue_job_script.py. Here's a basic example to read a CSV file and print its contents:

python
Copy code
import sys
from awsglue.transforms import *
from awsglue.utils import getResolvedOptions
from pyspark.context import SparkContext
from awsglue.context import GlueContext
from awsglue.job import Job

# Create GlueContext and SparkContext
args = getResolvedOptions(sys.argv, ['JOB_NAME'])
sc = SparkContext()
glueContext = GlueContext(sc)
spark = glueContext.spark_session

# Create the dynamic frame to read the CSV file from S3
source_path = "s3://your-source-bucket/csv-files/your-file.csv"  # Replace with your S3 bucket and CSV file path
dynamic_frame = glueContext.create_dynamic_frame.from_options(
    connection_type="s3",
    connection_options={"paths": [source_path]},
    format="csv",
    format_options={"withHeader": "true"}
)

# Print the contents of the dynamic frame
dynamic_frame.printSchema()
dynamic_frame.show()
Step 3: Create the Glue Job using AWS Console or Terraform
Now, you can create the Glue Job using the AWS Management Console or by using Terraform. If you prefer Terraform, you can modify the previous Terraform script to create the Glue job with the necessary IAM role and permissions:

hcl
Copy code
provider "aws" {
  region = "us-east-1"  # Change to your desired AWS region
}

# IAM Role for Glue Job (Create as described in the previous answer)

# Glue Job
resource "aws_glue_job" "glue_read_csv_job" {
  name        = "glue-read-csv-job"
  role_arn    = aws_iam_role.glue_role.arn
  command {
    name        = "glueetl"
    python_script = filebase64("glue_job_script.py")  # Path to the Glue job script
  }
}
Step 4: Apply the Terraform Configuration
Run the following commands in the same directory as the Terraform script:

bash
Copy code
terraform init
terraform apply
This will create the Glue job that reads the CSV file from the specified S3 bucket and prints its contents. You can modify the Glue job script to perform more complex transformations or write the data to a different destination.

Please ensure that the S3 path in the script matches the actual location of your CSV file in the S3 bucket.




User
source_path = "s3://scene-test1-s3/whitewines.csv"  # Replace with your S3 bucket and CSV file path
dynamic_frame = glueContext.create_dynamic_frame.from_options(
    connection_type="s3",
    connection_options={"paths": [source_path]},
    format="csv",
    format_options={"withHeader": "false"}
)

dynamic_frame.show() is empty
ChatGPT
If your dynamic_frame.show() is empty, it means that Glue was not able to read the CSV file correctly. The most likely reason for this is that the withHeader option is set to false, and Glue is treating the first row of your CSV file as data instead of column headers. As a result, it is unable to properly interpret the data.

To fix this issue, you should set withHeader to true in the format_options when creating the dynamic frame. This way, Glue will read the first row as column headers, and the data will be displayed correctly. Here's the updated Glue job script:
```
import sys
from awsglue.transforms import *
from awsglue.utils import getResolvedOptions
from pyspark.context import SparkContext
from awsglue.context import GlueContext
from awsglue.job import Job
  
sc = SparkContext.getOrCreate()
glueContext = GlueContext(sc)
spark = glueContext.spark_session
job = Job(glueContext)
source_path = "s3://scene-test1-s3/whitewines.csv"  # Replace with your S3 bucket and CSV file path
dynamic_frame = glueContext.create_dynamic_frame.from_options(
    connection_type="s3",
    connection_options={"paths": [source_path]},
    format="csv",
    format_options={"withHeader": "true"}
)
dynamic_frame.show()
job.commit()
```
Make sure that the CSV file you are reading has the first row as the header row containing column names. After making these changes, the Glue job should be able to read the CSV file correctly, and you should see the contents of the dynamic frame in the output.

Create the Glue Job using AWS Console or Terraform
Now, you can create the Glue Job using the AWS Management Console or by using Terraform. If you prefer Terraform, you can modify the previous Terraform script to create the Glue job with the necessary IAM role and permissions:

```
provider "aws" {
  region = "us-east-2"  # Change to your desired AWS region
}

# IAM Role for Glue Job (Create as described in the previous answer)

# Glue Job
resource "aws_glue_job" "glue_read_csv_job" {
  name        = "glue-read-csv-job"
  role_arn    = aws_iam_role.glue_role.arn
  command {
    name        = "glueetl"
    python_script = filebase64("nb2.py")  # Path to the Glue job script
  }
}
```
Apply the Terraform Configuration
Run the following commands in the same directory as the Terraform script:
```
terraform init
terraform apply
```
This will create the Glue job that reads the CSV file from the specified S3 bucket and prints its contents. You can modify the Glue job script to perform more complex transformations or write the data to a different destination.














