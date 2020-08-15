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

Server transfer Unix files from one Unix system to another Unix system and we get this string. Can you please decode this string for me!

9;G-C=&9[37E?1B%R<W1?8VAA;&Q ;F<S?0
```

That challenge was brainstorming and new for me. I have not performed that in past. So, I google-searched about the file transferring and found that.

![](./images/1.png = 250x)

![alt text](images/1.png | width=200 )

<kbd>![alt text](images/1.png)</kbd>



