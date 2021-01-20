Project Description: In this project I will be creating a web based application that can figure out the temperature when we tell it a particular city. To accomplish a project of this sort we may need a back end serverless database(DynamoDB) , s3 bucket( static website hosting) and lambda fuction, Cloud front, API gateway, Amazon Lex to create a Chat Bot.
Below are the steps to accumplish this project

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

step 9 : Try accessing the text.html using the cloud front domain name and it should work

step 10 : Now we need to create a backend API using API gateway so that we can mock our API. Once our mock is wired up and we have tested our website
            we can then think of creating a API that can hit the backend database or run a function.
step 11 : Create a REST endpoint with API gatway to Mock the API and check if it is working 

step 12 : We need to enable Cross origin resource sharing (CORS) as the cloudfront domain name is the API gateway Domain name are different and the browser will act                cranky when you try to go across domains

step 13 : click action and choose deploy API . You will get a invole URL and need to copy and save that info

step 14 : Now we need to wire up mock API to our website
          go the config.js object file which is inside your bucket and replace null with the invoke url as shown below
          var API_GATEWAY_URL_STR = "https://Your-Invoke-URL.execute-api.us-east-1.amazonaws.com/test";
          before saving remember to set cache-control to max age=0
          
step 15: browse the cloud front URL and choose the city. It will give an output as set in our mock API . Now we have a Mock API intercating with our website


