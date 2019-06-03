---
title: Serverless Email Forwarding
author: George Diavatis
tags:
  - AWS
  - Serverless
  - Email
  - Lambda
categories:
  - Serverless
  - Email
abbrlink: 58309
date: 2019-05-25 14:12:00
---
### Serverless Email Forwarding ###

If you followed the steps in my previous post then you will have your own domain **example.com** registered and administered through Route 53.

Wouldn't it be nice if you could leverage that domain to also receive emails sent to @example.com addresses?

The answer is yes, you can! And without having to utilise paid solutions such as G Suite.

A Node.js script written by Arithmetric on Github makes use of AWS Lambda and Simple Email Service (SES) to run a ["serverless" email forwarding service](https://github.com/arithmetric/aws-lambda-ses-forwarder).

Emails sent to designated aliases on @example.com are stored in an S3 bucket and then forwarded to the configured email address via a Lambda script. No need to run dedicated email servers or EC2 instances!

Daniel López Azaña has written [an excellent guide](http://www.daniloaz.com/en/use-gmail-with-your-own-domain-for-free-thanks-to-amazon-ses-lambda/) on how you can implement this.

In the first step where you need to create a TXT record in Route 53, this is the value you need to enter:

~~~
"v=spf1 a mx ~all"
~~~
