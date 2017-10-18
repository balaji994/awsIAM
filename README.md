# awsIAM

AWS Proof of Concepts Assignment 

1. IAM settings are region specific or NOT?

No, IAM settings are not region specific as you could see in the console below 
![image](https://user-images.githubusercontent.com/23042611/31738973-e66f69c8-b411-11e7-9ca8-5e6221cac675.png)

![image](https://user-images.githubusercontent.com/23042611/31739000-070fc9f2-b412-11e7-973c-79d7c6559100.png)







2. By default users have no permissions when first added



 

Creating a user 

Reference: http://docs.aws.amazon.com/IAM/latest/UserGuide/access_permissions.html
 

By default there are no permissions for an user created but we can create permission through a assigning user to a group or by copying permissions from other users. You could see the options below 








 
 


Note*: If any user is created with required password reset then the user gets a permission called IAMUserChangePassword the proof is below.
 
 

3. Active Directory Federation (AD) Can authenticate to AWS with AD using SAML

 

1.	The flow is initiated when a user (let’s call him Bob) browses to the ADFS sample site (https://Fully.Qualified.Domain.Name.Here/adfs/ls/IdpInitiatedSignOn.aspx) inside his domain. When you install ADFS, you get a new virtual directory named adfs for your default website, which includes this page
2.	The sign-on page authenticates Bob against AD. Depending on the browser Bob is using, he might be prompted for his AD username and password.
3.	Bob’s browser receives a SAML assertion in the form of an authentication response from ADFS.
4.	Bob’s browser posts the SAML assertion to the AWS sign-in endpoint for SAML (https://signin.aws.amazon.com/saml). Behind the scenes, sign-in uses the AssumeRoleWithSAML API to request temporary security credentials and then constructs a sign-in URL for the AWS Management Console.
5.	Bob’s browser receives the sign-in URL and is redirected to the console.
Reference:  https://aws.amazon.com/blogs/security/enabling-federation-to-aws-using-windows-active-directory-adfs-and-saml-2-0/
4th and 5th 
Web Identity Federation to AWS via Facebook or any other social sites.AssumeRoleWithWebIdentity API call used to obtain temporary security credentials when authenticating using Web Identity Federation?
Go to the web identity federation playground 

 

then sign in with google  
 



allow the permission 
 

proceed to step 2 
 

call the AssumeRoleWithWebIdentity

 
you will get the request and response GoTo step 3

 
Similar steps for Facebook too here they are 

  

  




 
 




Here are the code patterns for the above things

Trust Policy:

{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "",
      "Effect": "Allow",
      "Principal": {
        "Federated": "accounts.google.com"
      },
      "Action": "sts:AssumeRoleWithWebIdentity",
      "Condition": {
        "StringEquals": {
          "accounts.google.com:aud": "GoogleAppIdHere.apps.googleusercontent.com"
        }
      }
    }
  ]
}


Request
GET / HTTP/1.1
Host: sts.amazonaws.com
Content-Type: application/json; charset=utf-8
URL: https://sts.amazonaws.com/?DurationSeconds=900&Action=AssumeRoleWithWebIdentity&Version=2011-06-15&RoleSessionName=web-identity-federation&RoleArn=arn:aws:iam::877950674958:role/WebIdFed_Google&WebIdentityToken=eyJhbGciOiJSUzI1NiIsImtpZCI6IjRlZWM5NjBhNmQ1MGE3NGY3OTEzNDZjMmJjN2M3NDZkMjdiZTk2YWYifQ.eyJhenAiOiIzMjg3NTkyNTU5MzcuYXBwcy5nb29nbGV1c2VyY29udGVudC5jb20iLCJhdWQiOiIzMjg3NTkyNTU5MzcuYXBwcy5nb29nbGV1c2VyY29udGVudC5jb20iLCJzdWIiOiIxMDg0NzIyODcxOTg0NDMyNDM3MDAiLCJhdF9oYXNoIjoiQ0NRb2I1LWZ4RHN0TThuT0ZGaXhNZyIsImNfaGFzaCI6ImVBTER3VUloZHU4X1I1YVNXajVYZVEiLCJpc3MiOiJhY2NvdW50cy5nb29nbGUuY29tIiwianRpIjoiM2VlNTk0ZmFmMTAyMmZkZTFiZjEyOTM2NGVhYTlkYjFiM2M2ZTAyYSIsImlhdCI6MTUwODMzOTE5MCwiZXhwIjoxNTA4MzQyNzkwfQ.rZs86MEWi-XSOWIoGhr7z6e2mnZtEX5GjM3Lbh6bnIOc1Ktvuu01BLnD61gTbKLi_YjnbEB4y_7E2MfbhPzLKjdH0EiM3hATsdf2P70yKTAQU_RquZ-qYXu21uE4dOUhmxuveXs9OqhLsAblBFFo1atsnLBH-6pjgpiijUCRuqyed8AVgGkCpEPi9X4jlDINWqjtmFsO9IQY5GuL4poSojqtzxDhRBvh_Zhl6ab65dpzs2HdWJltmJCx0m6SB1Ir8BjENWFZjrUF8hSdwrAx_xbxCPITr0UV7kDV0MhY1KfU1jvldmG1kn_GedROGocT8H0DvYQtkb38EJ_fhHl7Dw


Response

<AssumeRoleWithWebIdentityResponse
  xmlns="https://sts.amazonaws.com/doc/2011-06-15/">
  <AssumeRoleWithWebIdentityResult>
    <Audience>328759255937.apps.googleusercontent.com</Audience>
    <AssumedRoleUser>
      <Arn>arn:aws:sts::877950674958:assumed-role/WebIdFed_Google/web-identity-federation</Arn>
      <AssumedRoleId>AROAJSA6CYQ3QWA6EM2QQ:web-identity-federation</AssumedRoleId>
    </AssumedRoleUser>
    <Provider>accounts.google.com</Provider>
    <Credentials>
      <AccessKeyId>ASIAJPKCQHGONR2F6DDQ</AccessKeyId>
      <SecretAccessKey>bJ5O2HDjME4d2Vfqk6U2tMVcpd7wmL+fyTJ6Yyzw</SecretAccessKey>
      <SessionToken>FQoDYXdzEJD//////////wEaDAS2/YoGO7odcGr7KSKiA6esChq6LTtqwf9kNA5MjY5VswemNv4ApzMe7QDbwVCDc1bhKgW2DCduDP466cUvWZDdnkWIYaw5IhjC3XvEvpG051IuhsksxVsgaIbSnrtlTBfRtqKeJQ67xI/T71ICPZCKXUijZ4Y7QYrPagNSbBnYa3rxiiugpkcsXIYC57IRqG8SX5iouMCi+Dux/T/i/ywN/oeOgdoo8J2bPzi9EAV2VWTaTEDpSN6H/YFzuOoIRJzaLH5ms3uuN1mkcqI49VwNhLJQvCwXNe3clpM1B5/us64XLJSPn58svn1tAu/Aj4GGy70MIJZH0W35TUixUoDTspLf9n7OpR4Vdbc9MZgYykLYUq7tNMPiIW8O00TZ3pkVgoN3E+2qXAeTN0jVAehW08+y/ULjRvY37pNpWWlLNWf3Mr2cTCfcQQ8YXiK0GQGyHFy9zWCZT/XrOkMo717F2+WeY/qVAub96eCGBzbwoUbbq7iMdwOHNY+SlIP4td3ZwVZoWcpTsrriY6akbQCFqZDrQSFBoNUmMSAMtoTLzxpiWkjnwCxr1yrd5iXYubMor96dzwU=</SessionToken>
      <Expiration>2017-10-18T15:26:43Z</Expiration>
    </Credentials>
    <SubjectFromWebIdentityToken>108472287198443243700</SubjectFromWebIdentityToken>
  </AssumeRoleWithWebIdentityResult>
  <ResponseMetadata>
    <RequestId>a6725427-b416-11e7-beeb-c1689e858149</RequestId>
  </ResponseMetadata>
</AssumeRoleWithWebIdentityResponse>








Access Policy

{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Action": [
        "s3:GetObject"
      ],
      "Sid": "Stmt1374684569000",
      "Resource": [
        "arn:aws:s3:::web-identity-federation-playground/*"
      ],
      "Effect": "Allow"
    },
    {
      "Action": [
        "s3:ListBucket"
      ],
      "Sid": "Stmt1374684915000",
      "Resource": [
        "arn:aws:s3:::web-identity-federation-playground"
      ],
      "Effect": "Allow"
    }
  ]
}

Near access policy you will get the access key id and secret access keys 

Reference: http://docs.aws.amazon.com/STS/latest/APIReference/API_AssumeRoleWithWebIdentity.html



Thank You
