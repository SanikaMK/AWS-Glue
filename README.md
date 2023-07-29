# AWS-Glue
Terraform script to initiate S3 bucket:
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

Glue Job script:
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

Data checking through Glue PySpark Notebooks:
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
