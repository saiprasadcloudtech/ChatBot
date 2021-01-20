Project Description: In this project I will be creating a web based application that can figure out the temperature when we tell it a particular city. To accomplish a project of this sort we may need a Front end application , a back end serverless database, s3 bucket and a lambda function as it is serverless project.

step 1 : Create a S3 bucket and configure it as a static website.
step 2 : after creating the bucket, choose static website hosting and select " use this bucket to host a website". under index document type "text.html" and under error document type "error.html"
step 3 : select permission tab and select bucket policy. paste the below code with your bucket arn 

{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AddPerm",
            "Effect": "Allow",
            "Principal": "*",
            "Action": [
                "s3:GetObject"
            ],
            "Resource": [
                "arn:aws:s3:::2019-03-01-er-website/*"
            ]
        }
    ]
}

step 4 : upload the objects that make the front end design into the bucket 
