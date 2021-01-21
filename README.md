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

Indroducing content delivery network 

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

Introducing Lambda Function

Step 16 : Now we have to create a Lambda function that calls the database for weather information when the API is hit 
            Basically we are replacing the API gateway mock with the lambda mock
            
step 17 : create a lambda function using Node JS runtime 
            > seelect author from scrach
            > select node js runtime
            > for execution role choose create a new role and choose Basic Lambda@edge permission(for cloudfront trigger )   # to give permission to write to                       cloudwatch logs
            
step 18 : paste the below code in index.js file 
                                                    function handler(event, context, callback){    
                                                          var 
                                                                city_str = event.city_str,
                                                                response = {
                                                                     city_str: city_str,
                                                                     temp_int: 74
                                                                };
                                                           console.log(response);
                                                           callback(null, response);
                                                     }
                                                     exports.handler = handler;
            This code will simply take city as a string and throw away the temperature as 74 ( lambda mock )
           
step 19 : we can test the lambda function with the following inline code editor 
            {
              "city_str": "LA"
            }
            output of the test will be as follows  {
                                                     "city_str": "LA"
                                                     "temp_int":74
                                                   }
step 20 : now we have to replace the API mock end point with lambda function 
           > go th api gateway we had already created created 
           > under resource choose POST and click integration request
           > for integration type change mock to lambda function # make sure " use lambda proxy integration is de-selected" 
           > choose the desired lambda function and save leaving rest as default
           
 step 21: click method execution at the top and click TEST 
           > paste the following in the request body 
                 {
                  "city_str": "SEATTLE"
                 }
            > click TEST
            > under body response you will see the desired result 
                     {
                      "city_str": "SEATTLE",
                      "temp_int": 74
                     }
            > We can also see the followin in the log section as a confirmation 
                Sending request to https://lambda.us-east-1.amazonaws.com/2015-03-31/functions/arn:aws:lambda:us-east-1:179741345863:function:get_weather/invocations
                Method response body after transformations: {"city_str":"SEATTLE","temp_int":74}
                
  step 22: we now need to reenable CORS similar to how we did in earlier steps 
  step 21: click action and deploy API . set development stage to Test and Deploy 
           WE can now try accessing the cloudfront website and choose any city. It will respond as 74 
            
  Introducing a Database as a backend 
  step 22 : now we need to create a new lambda function so that it can query the database to get the weather information
            > create a dynamoDB table 
            > Once database is created, we will create a lambda function that will take take a CSV file and parse it and throw it into the dynamoDB table
            > once we finish coding and creating a new file CSV file with data in it under the lambda function we try to TEST the function and check if the csv data                 is loaded into the dynamoDB . If the execution result is succeeded that means data is loaded into the database
  step 23 : we have to confirm from the database end as well . hence we login to dynamoDB and open the table and check the table item by going to items tab. 
            > change scan to query and try with any city name and it will give us the result with the temerature 
  step 24 : we can now delete the lambda fucntion which we used to seed the csv file.
  
  step 25 : we now do changes to the main lambda function so that it can query the database and return the temperature. We can now test the lambda function by                     choosing configure test event 
            FOr example : {
                             "city_str": "DENVER"
                          }
                         This should return 
                           {
                            "temp_int": 38,
                             "city_str": "DENVER"
                             }
            We the confirm the same from the cloud front URL by entering a city and it will return the temperature of the city.
  
  FInally we have a serverless data driven text weather app running in a global content delivery network for low latency with DynamoDB as a backend.
                          
            
  
