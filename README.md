# Writeup for CTFFriday 2020 August Week 2 by Muzkkir

In this article, I’m going to explain solutions of NSCTF August Week 2, 2020 CTF challenge theme on "AWS Cloud" Organized by Net-Square Solutions Pvt. Ltd. and created by Aman Barot. AWS is well known for its cloud computing products and services. Netflix, Twitch, LinkedIn, Facebook, etc. are using AWS services for their platform.

<kbd>![alt text](images/banner.jpg)</kbd>

## Overview

For the First flag, I decoded the uuencoding string which used for file transfer by online service.

After that, I Searched for the bucket on AWS and read the publicly available files. From one of those files, I found my second flag.

Digging into all files I found AWS access credentials from the "_config.yml" file. Configuring the AWS CLI command I had listed all files, functions, and configurations. With the help of lambda functions, I found my third flag.

For the fourth flag, I have one image file and it was in pptx format. So I unzip the file and got one unique GIF file. After decoding the Zxing file I got my Final Flag.




## First Flag

```
challenge 1

Server transfer Unix files from one Unix system to another Unix system and we get this string.
Can you please decode this string for me!

9;G-C=&9[37E?1B%R<W1?8VAA;&Q ;F<S?0
```

That challenge was brainstorming and new for me. I have not performed that in past. So, I google-searched about the file transferring and found that.

<kbd>![minipic](images/6.png)</kbd>

After reading the format and usage of uuencoding from Wikipedia I understand that string was captured during the file transferring by some tool. 

<kbd>![](images/2.png)</kbd>

I visited to the "https://decode.urih.com/" website for decoding this encoded string format into readable text.

<kbd>![](images/5.png)</kbd>

I got my first flag. :bowtie:

<kbd>![](images/4.png)</kbd>

```
nsctf{My_F!rst_chall@ng3}
```




## Second Flag

```
challenge 2

Meena is super tech savy for her age, at just over 52 years old, she is the oldest AWS administrator in 
her county. But old age has its perils. One of them being the inability to grasp new knowledge as quickly
as she did when she was aboard the RMS Titanic.

Configuration goof ups have become a weekly thing now with her. Like the domain configuration she has in 
place for her backup buckets. Or the server she manages. 

I mean, look at the websitesnapshot she has made at Indian region, clearly missing out on utilizing AWS' 
cool features to save the website and instead ending up with a giant blob. Jack can't seem to make head 
nor tail of it either and does not believe this is the actual backup. He believes there has to be a proper 
backup somewhere in that bucket (like the Heart of the Ocean)! 

There is no plank of wood to save Jack this time! Can you help him find the actual backup and use the 
information in there to find the flag for this level please? Before he sinks again! 
```

This challenge was about to explor the AWS services. Now, let me list important words from this passage.

> AWS service used for website

> "websitesnapshot" is bucket or domain name

> Indian region for AWS

At this point, I search over web about the AWS url format and AWS Indian Region.

<kbd>![alt text](images/10.png)</kbd>

<kbd>![alt text](images/11.png)</kbd>

For gathering more information I visited "https://docs.aws.amazon.com/general/latest/gr/rande.html" AWS's Docs.

<kbd>![alt text](images/12.png)</kbd>

```
So I forged bucket URL by that,

Bucket Name + S3 + AWS Domain

https://websitesnapshot.s3.amazonaws.com

OR

Bucket Name + S3 + AWS Asia Pacific (Mumbai) Region + AWS Domain 

http://websitesnapshot.s3.ap-south-1.amazonaws.com/
```

 <kbd>![alt text](images/13.png)</kbd>

I found bunch of URLs and Files. Its hard for me to manually searched all files content. So, I created small python script that work for me.

```
import re, requests

with open('data.xml', 'r') as data:
    data = data.read()

urls = re.findall("<Key>(.*?)</Key>", data)

for i in urls:
    req = requests.get('http://websitesnapshot.s3.ap-south-1.amazonaws.com/'+i)
    if len(req.text) < 70:
        print(req.text)
```

This script will find urls from the "data.xml" file in which I saved the page content. From that, It collect all strings between "<Key>" and "</Key>". As python send request for all urls and print response which lenght is less than 70 otherwise it will print the entire pages from the website and we can not find any flags.

<kbd>![alt text](images/14.png)</kbd>

I received the string ```Sｏｍｅoｎe foｕｎd a ｗriteup оf ｔhіs ｃhaｌlｅｎge ａnd ｔwｅｅｔeｄ abouｔ ｉt```. So, I did not get how to decode that message. 

A few minutes later surfing on the net I found "twitter secret message" encoding is used. I navigated to "https://holloway.nz/steg/" and decode this obfuscated message.

That was my 2nd flag. 

```nsctf w3rd_nm3_fr```

<kbd>![alt text](images/15.png)</kbd>



## Third Flag

```
Challenge 3

Rehana used serverless technology to publish her website. She set some values for multiple project.
Find some interesting values.
```

I have retrived server files content with the help of python script. Now, again I used that with some changes to find the AWS access credentials. I send request to all URls and print them on terminal. Also use grep command to find the "Access Key" string from terminal output.

<kbd>![alt text](images/23.png)</kbd>

Bingoo !!!

```
root@muzzy:~# python3 ctf.py | grep "Access Key" -A 1
    Access Key : ***************** 
    Secret Key : ***************************
root@muzzy:~#
```

I found the AWS Access Key and Secret Key. Now I need to configure the AWS CLI with this credentials for access the bucket. I finally has reached so far for exploitaing the AWS bucket.

```
root@muzzy:~# aws configure
AWS Access Key ID [None]: **************
AWS Secret Access Key [None]: ************************
Default region name [None]: ap-south-1
Default output format [None]: 
```

<kbd>![alt text](images/24.png)</kbd>

As we know, User has been using Serverless bucket and one of the most popular bucket is AWS Lambda.

> AWS Lambda is an event-driven, serverless computing platform provided by Amazon as a part of Amazon Web Services. It is a computing service that runs code in response to events and automatically manages the computing resources required by that code. You can use AWS Lambda to extend other AWS services with custom logic, or create your own back-end services that operate at AWS scale, performance, and security.

> AWS Lambda is a compute service that lets you run code without provisioning or managing servers. AWS Lambda executes your code only when needed and scales automatically, from a few requests per day to thousands per second.

Regarding this I read entire documentation from "https://docs.aws.amazon.com/cli/latest/reference/lambda/". I run some commands and get below output.

<kbd>![alt text](images/18.png)</kbd>

```
"FunctionName": "websitebackup"
```

Bucket has one function named "websitebackup" which I found interesting.

There are many AWS CTF challanges in which we find flag within functions. So I did learn commands from "https://docs.aws.amazon.com/cli/latest/reference/lambda/get-function-configuration.html" and get the configuration details from aws.

<kbd>![alt text](images/19.png)</kbd>

```
root@muzzy:~# aws lambda get-function --function-name websitebackup
{
    "Code": {
        "RepositoryType": "S3", 
        "Location": "https://awslambda-ap-s-1-tasks.s3.ap-south-1.amazonaws.com/snapshots/018871369877/websitebackup-f6af606a-e5ce-4456-aa12-467963b722e6?versionId=dxbbWzGFGoGxrGkaDtzQ79mKtCx_50G4&X-Amz-Security-Token=IQoJb3JpZ2luX2VjEOL%2F%2F%2F%2F%2F%2F%2F%2F%2F%2FwEaCmFwLXNvdXRoLTEiRjBEAiAg9K78qVoiaxbzTtBO48v%2BHwfYdhyOiYmNxz%2FXOs%2F26AIgUUWj0V4vdjqV%2BxKWUwIbKQilI%2F%2BoFMKjDvS0i1ST%2FkwqvwMIuv%2F%2F%2F%2F%2F%2F%2F%2F%2F%2FARABGgw1NDUyNTUyMDEzMDciDMV%2BtcppfXM6ZShFSCqTA0gFfX9Sp5dfwVzCSJ7Z%2BG2gdKK5hSQLDLMEFHcZahsCetleGJPD5Gj62oJWIftJoMoD0x04jeokuD2aKv8RtjuaS%2BsD4cQMIMhvR8o7vEhLCvQFK7X14I6QQCFhgnBBZHa3Mmm0fh79Lc%2BiHYAlu0moV92rCn0J%2BeknlqGEowCeNw%2F7LfStv58QQiBrizd%2F%2Br%2BnpxHM%2FwYX8cSyFGVjNgq%2BCGOXE2QBCqzkRRvvDQmwie90oeJP58%2F3h4vkKTxlsBJEbzoDoVScP6LIO52IuWjHKWuddQKbSca%2Fk%2FlOUi2EXkH%2F1abQQWF1wx95CaJ%2F7JIpHBLgJRa7WmQHJQYNO2V6hpKUYwzgroVXLmINWx%2FFt1knSe4OpRc3T%2FMYC80Y0cOA1q1Ivdb%2FiK%2BPwrcYTV%2F2HvvG5iGC%2F6%2Fvv3y7AXT7xM%2Bhddt1he%2Fjcf4osi9WM1j0aL4A6lvElQ3r%2BsKXsQtzEB50nxOYepwu3XlF9MQaYVFPcNU5LyYplwxMZoLOh%2BqwucRLvZx8L3XHhFm5cAAvMmgwod7e%2BQU67AF3KEbaUcmKOSXNrrx%2Fr5QZIm10ic8Z1vEJHx%2FURwa%2BVss4V9aiPvstpItPnJABvLZ5n6piTdCuDwGlKVe5IUDNJ1%2FdWdihM%2FjIbb83C82AwIq6e1A%2B99jM8p%2Fr0V%2FCyWr5D5TQtgAcbRm5xuRvaiLZ4CH3bpJilb8LyYEAoxcEIm7WpnJGTzxFsPx%2Bw%2F5lAWeo9seLBHuHg6I8gUEn9VokB%2FNsqnnfYunutBu9BVuih5qRqosBntdHb6OzgHaNR2iQ514qJ3c2Fi6mPeOG7NFmtSthwJegYilR0PKzRNmQy0RrPZUntYllXVZCGw%3D%3D&X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Date=20200815T103058Z&X-Amz-SignedHeaders=host&X-Amz-Expires=600&X-Amz-Credential=ASIAX5456DIN6D6DCCGT%2F20200815%2Fap-south-1%2Fs3%2Faws4_request&X-Amz-Signature=92c7e1ac2db30b4cd3c2c8c4cebb0ecaabd9824821815104b4ba02890c043718"
    }, 
    "Configuration": {
        "TracingConfig": {
            "Mode": "PassThrough"
        }, 
        "Version": "$LATEST", 
        "CodeSha256": "vLje7sZloPikmGIHGO22ePKqHjnPXyDI5mj3wmL+Afg=", 
        "FunctionName": "websitebackup", 
        "LastUpdateStatus": "Successful", 
        "MemorySize": 128, 
        "RevisionId": "4480402d-5a23-4df6-b6c2-dfa11fca4cfd", 
        "CodeSize": 14041922, 
        "FunctionArn": "arn:aws:lambda:ap-south-1:018871369877:function:websitebackup", 
        "State": "Active", 
        "Handler": "lambda_function.lambda_handler", 
        "Role": "arn:aws:iam::018871369877:role/service-role/websitebackup-role-mxbomdo3", 
        "Timeout": 3, 
        "LastModified": "2020-08-13T07:04:16.231+0000", 
        "Runtime": "python3.6", 
        "Description": ""
    }, 
    "Tags": {
        "flag": "nsctf S3rverless_T3chnology"
    }
}
```

Gochaa!!!!

<kbd>![alt text](images/20.png)</kbd>







