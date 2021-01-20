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
step 5 : under metadata section For header choose cache-control and for value enter max-age=0 and save
step 6 : create a cloud front distribution to overcome the latency problem for people across the globe.
step 7 : Once cloud front is create go to the bucket policy under permission and you will see something like this
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AddPerm",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::2019-03-01-er-website/*"
        },
        {
            "Sid": "2",
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::cloudfront:user/CloudFront Origin Access Identity E1KO2GAPIWFF7X"
            },
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::2019-03-01-er-website/*"
        }
    ]
}

step 8 : remove the public s3 access section from the above policy . This will allow only cloud front to access s3 bucket.
step 9 : Try accessing the text.html using the cloud front domain name 

