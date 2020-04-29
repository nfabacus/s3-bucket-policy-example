# AWS S3 - Bucket Policy and CORS configuration Example
### Purpose: Restrict access to a S3 bucket by specific domain only.
Below setting worked for me.

1. Go to s3 in aws and create a bucket.
2. Place some image and video files in the bucket.
3. Permissions
	1) Go to Permissions tab in the bucket.
	2) In "Block public access" under Permissions, set Block public access Off for all items.  This will allow public access under the below conditions.
4. Access Control List
		
	Make sure there is nothing is ticked in 'Public access' in Access Control List.  We will set public access in "Bucket Policy" instead as below.
5. Bucket Policy
	
	1) Go to "Bucket Policy" in Permissions for the Bucket and set Bucket Policy in "Bucket policy editor" in your s3 bucket
    Read this:
	```https://docs.aws.amazon.com/AmazonS3/latest/dev/example-bucket-policies.html#example-bucket-policies-use-case-4```
	
	Example for Production
	```
	{
        "Id": "policy id from policy generator here",
        "Version": "2012-10-17",
        "Statement": [
            {
                "Sid": "some id from policy generator here",
                "Action": [
                    "s3:GetObject"    <--- actions
                ],
                "Effect": "Allow",    <--- allow actions
                "Resource": "arn:aws:s3:::test-bucket-123-hello/*",  <--- your bucket arn
                "Condition": {
                    "StringLike": {
                        "aws:Referer": [
                            "https://nfabacus.github.io/s3-bucket-policy-example/*"  <--- your website url(s) to allow access to the s3 bucket
                        ]
                    }
                },
                "Principal": "*"  <--- * means anybody to access it.
            }
        ]
    }
	```
	
	Example for Development - just remove condition to accept any
		```
    	{
            "Id": "policy id from policy generator here",
            "Version": "2012-10-17",
            "Statement": [
                {
                    "Sid": "some id from policy generator here",
                    "Action": [
                        "s3:GetObject"    <--- actions
                    ],
                    "Effect": "Allow",    <--- allow actions
                    "Resource": "arn:aws:s3:::test-bucket-123-hello/*",  <--- your bucket arn
                    "Principal": "*"  <--- * means anybody to access it.
                }
            ]
        }
    	```
	
6. CORS configuration

	To avoid cors issue when your app is using s3 bucket operations, place below in the CORS configuration editor in 'CORS configuration'.
	```
	<?xml version="1.0" encoding="UTF-8"?>
	<CORSConfiguration xmlns="http://s3.amazonaws.com/doc/2006-03-01/">    
	<CORSRule>
	    <AllowedOrigin>https://nfabacus.github.io/s3-bucket-policy-example/</AllowedOrigin>  <--- replace this with your website origin url
	    <AllowedMethod>PUT</AllowedMethod>
	    <AllowedMethod>POST</AllowedMethod>
	    <AllowedMethod>DELETE</AllowedMethod>
	    <AllowedHeader>*</AllowedHeader>
	</CORSRule>
	</CORSConfiguration>
	```

Now you should be able to access the s3 bucket images/videos only via your domain.

Example: https://nfabacus.github.io/s3-bucket-policy-example/
