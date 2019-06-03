title: Hello World!
author: George Diavatis
abbrlink: 22462
tags:
  - AWS
  - Serverless
  - Blog
categories: [Serverless, Blogging]
date: 2019-05-10 08:48:00
---
### Introduction
Hello everyone and welcome.

I had been thinking about creating a blog to share my thoughts, insights, advice and commentary on the world of Cloud for some time but I have only just got round to it.

In my first post, I wanted to share to you how I came about choosing the blogging solution that I did, in the hope that it might be of some use to you too.

There are of course a multitude of different blogging services available, I found a good comparison of them [here](https://themeisle.com/blog/best-free-blogging-sites), however as a technical enthusiast I wanted something that:

- **Provides full control**: Allows me to delve into the inner workings, not just hide behind a WYSIWYG editor
- **Low cost**: Whilst full control is great, I don't want to be incurring lots of unnecessary expense
- **Low overheads**: I would like to avoid having to maintain servers if possible

### Available Solutions

With those requirements in mind, I set out to find the most appropriate solution for my needs. Being AWS certified, my first thoughts were to implementing a solution in the AWS Cloud.

I could run a WordPress server on EC2 however I would want to do that properly, not a half baked attempt.

![© AWS](/images/pasted-1.png)

As you can see from the diagram above, creating a secure, resilient, redundant and scalable solution can get quite involved, much more so that would be justified for the purposes of my personal blog.

Another option would be the use of Docker containers to host the WordPress instances however that would also suffer from the issues of complexity, maintenance and cost.

Having considered the above, my attentions turned to a solution based on **serverless** applications. Within [AWS](https://aws.amazon.com/serverless/) these are services such as S3, Lambda, DynamoDB, Fargate etc.

S3 could provide the storage, DynamoDB would provide the database and Lambda  would provide the compute.

My preference was to make use of Python as the scripting language as I have experience in that and a NoSQL database natively provided by AWS such as DynamoDB as that allows for costs to be kept low. I ruled out using a relational database as that would involve running a server or RDS instance. Aurora serverless was also not an option as it would incur higher costs than I was prepared to accept.

#### AWS Lamda Blog
**[AWS Lamda Blog](https://github.com/sirceljm/aws-lambda-blog)** sounded very promising as it used all the services I had in mind such as

- API Gateway
- Lambda
- DynamoDB
- S3
- Cloudfront
- SES

Unfortunately it is also no longer maintained, seldom a good sign. A replacement project was [mentioned](https://awly.io/) however that is still in the Alpha stages of development so I didn't consider that.

#### Flask Blogging

**[Flask-Blogging](https://github.com/gouthambs/Flask-Blogging)** which was very interesting as it used Python Flask, supports DynamoDB, Lambda and Markdown editing. On the downside it didn't appear to have much in the way of theme support. Nonetheless it became a prime candidate for me.

#### Hexo

**[Hexo](https://hexo.io)** is a static blogging framework based on Node JS. Rather than generate dynamic web pages on the fly, they are pre-rendered and hosted as static pages. Services which require dynamic functionality such as comments are handled by 3rd party plugins such as Disqus.
Hexo has a large community following and a wide range of themes and plugins are available.

My initial thoughts were to discard this option as I wanted a solution based upon Python however I then came across an article on A Cloud Guru which provided some insights into their experiences. They [recommend ditching Python](https://read.acloud.guru/six-months-of-serverless-lessons-learned-f6da86a73526) and embracing NodeJS when it comes to serverless applications.

I have not come across a well maintained serverless blogging solution that utilises NodeJS, Lambda, S3, DynamoDB and Cognito as of yet. I might be inclined to work on one myself given the various frameworks that are available however now is not the time. If you know of a solution that meets those requirements then please get in touch and let me know.

### Chosen Solution
In the mean time, I decided to opt for a Hexo based solution, which is what you are viewing this blog on at the moment.

It utilises S3 and Cloudfront for static web hosting, Route 53 for DNS management and Github/AWS Code build for CI/CD for code storage and build.

I will create a separate post outlining the process I went in putting the blog together.
