---
title: Your own Serverless Blog
author: George Diavatis
tags:
  - AWS
  - Serverless
  - Blog
  - Hexo
  - S3
  - Cloudfront
categories:
  - Serverless
  - Blogging
abbrlink: 54945
date: 2019-05-18 18:18:00
---

## Your own serverless blog!

### Overview

- Create static website offline using Hexo
- Register domain name using Route 53
- Create S3 bucket to host the site and make it publicly accessible
- Create SSL Certificate
- Create Cloudfront distribution and point it to S3 bucket
- Create DNS records
- Upload your site to S3/Cloudfront

### Introduction

In this post I will demonstrate how you can host your own blog site in a few steps without the need for expensive hosting services or running and maintaining your own server.

This is made possible through the use of a static website generator, of which there are several different types freely available online. I will be focusing on [Hexo](http://www.hexo.io) which was used to generate the blog you are reading now.

Being a static site does not mean you have to miss out on dynamic functionality such as comments, which are available via third party plugins. I have made use of [Disqus](https://disqus.com/) for commenting functionality.

But dont even static pages need to be hosted from somewhere you might ask? That is correct yes. Services such as Github Pages, Fastly, Amazon S3 and many more offer static web hosting for little to no cost.

In my example, I will be using services from AWS to host the blog. AWS offers a generous [free tier](https://aws.amazon.com/free/) which includes allowan"v=spf1 a mx ~all"ces for most of the services we will be making use of.

I have assumed you have an AWS account with sufficient privileges to create S3 buckets, Cloudfront distributions and administer DNS in Route 53.

An S3 bucket will be used to provide the static web hosting which in turn will be associated with a CloudFront distribution which is a Content Delivery Network service (CDN) used to speed up delivery of the site across the globe and also provide HTTPS access. Route 53 will also be used to manage the DNS for the domain name we will register.

### Hexo

![awesome](https://raw.githubusercontent.com/hexojs/awesome-hexo/master/hexo-logo.png)

Our first step is to download the Hexo application. Hexo is a static website generator based on NodeJS. You create your offline posts in [Markdown](http://daringfireball.net/projects/markdown/) and then Hexo generates the static web pages which you upload to your web host.

There is a way to automate this process which we will touch upon later.

I am assuming you have access to terminal on Linux/OSX to perform the tasks described in this post. If not then I recommend you look at installing [WSL on Windows 10](https://docs.microsoft.com/en-us/windows/wsl/install-win10) or run a Linux distribution in a virtual machine.

Make sure you have NodeJS installed. You can do this by running the following command

```bash
npm -v
```

Which will output the installed version. If not then following the [instructions](https://nodejs.org/en/) as required for your particular operating system.

Now it is time to install Hexo globally.

```bash
npm install -g hexo
```

And lets initialise Hexo with the name of our blog directory which I will call **myblog** but you are free to call it something else.  And install dependencies.

```bash
hexo init myblog
cd myblog
npm install
```

You should now have a folder called **myblog** which contains
the makings of your website.

Using a text editor of your choice, I recommend [Atom](https://atom.io/), edit the **_config.yml** file which should be in the **myblog** folder you just created.

The following options should be of interest

```yaml
title: [Blog Title]
subtitle: [Sub Title]
description:
author: [Name]
email:
language: [IETF format]
```

Now its time to see what our blog looks like. We run the command

```bash
hexo server
```

and navigate to the address shown.

Great! We can preview our site but its running on a web server on our machine and we want to host the site on the internet...

Time to configure our static host! But first lets register a domain name to help our readers more easily find our site.

### Route 53

In this step we will configure a custom domain name for our blog.

Whilst this step can be skipped if you do not wish to pay for the registration of a domain name, you would then have to present an unfriendly S3 or Cloudfront URL to your users.

Have a look for a domain name of your choosing. For sake of this exercise, we will assume you chose **example.com**

### Amazon S3

Now its time to create your S3 bucket.

Make sure you are in the AWS region which you want the bucket to be based in.

I recommend you name your bucket the same name as URL of your site,  something along the lines of  **blog.example.com**

By default the bucket will be locked down, with public access restricted.

Enable static hosting on the bucket which under properties section of the S3 bucket. Set the index document to **index.html**

Under the the permissions section of S3 the bucket will you find the bucket policy option in which you will need to amend, to allow public access.

```json
{
    "Version": "2012-10-17",
    "Id": "Policy1554146268776",
    "Statement": [
        {
            "Sid": "Stmt1554145991530",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::blog.example.com/*"
        }
    ]
}
```

### Certificate Manager

In order for pages to be delivered securely over HTTPS using own custom domain on Cloudfront in the next step, we must first request an SSL certificate.

Make sure you are in the US East (N. Virginia) region for this step. You can go back to your chosen region afterwards.

Request a certificate with a wildcard ***.example.com** to give you some flexibility should you wish to make use of additional subdomains in the future.

### Cloudfront

Now that you have your S3 bucket configured and your SSL certificate issued, its down to set up the Cloudfront distribution.

Click on **Create Distribution** and create a new **Web** distribution. In the Origin Domain Name field, make sure that you add your S3 bucket address in the format of **YOURBUCKETNAME**.s3.amazonaws.com. You might find yourself encountering inexplicable errors later on if you do not, as I found out.

If you decided to implement a subdomain e.g. blog.example.com then make sure you add that into the Alternate Domain Names (CNAMEs) box.

In the SSL Certificate section, choose **Custom SSL Certificate**.

When you click on the text box, you should see an option for the certificate you just created, so make sure you select that.

Then click **Create Distribution**.

### Route 53

Now we head back to Route 53 to finalise the DNS settings.

Go to Hosted Zones and select your domain. You should see a number of records that were created when you registered your domain.  Click on **Create Record Set** and make sure A - IPv4 address is selected.

If you aren't planning on using a subdomain then leave the name field blank however you might still want to create an additional record with the name **www**.

In our example I am using blog as the subdomain. Click on the alias target box and paste in the address of the Cloudfront distribution.

### Getting your site live!

Okay, so you now have a static site on your computer and you have a static hosting service with your own domain on AWS. How do you get your site live onto the internet?

There are a number of ways you can do this.

A popular method is to configure a CI/CD ( Continuous Integration and Continuous Delivery) pipeline using a source code source control service such as GitHub or CodeCommit and a code build service such as CodeBuild.

You commit your code to GitHub/CodeCommit and then that triggers an automated build of your code in the Cloud using CodeBuild.

For the sake of simplicity though I will assume you can build the hexo site locally on your machine. In which case all you need to do is get the content from your machine into the S3 bucket.

The easiest way to do this is via the AWS CLI. You can find instructions on how to install that [here](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html)

Navigate to your blog folder.

If you want to [create any posts](https://hexo.io/docs/writing), make sure you do so before the next step.

Type the command

```bash
hexo generate
```

This will generate the static web pages that will be hosted on S3/Cloudfront.

Now its time to upload the site. Navigate to the public sub folder and use the AWS sync with the below commands

```bash
cd myblog
cd public
aws s3 sync . s3://blog.example.com
```

And now your site should be live at blog.example.com.

### Additional Notes

#### Hexo Themes

You might want to experiment with different appearances for your site, which is where themes come in. Lots of different options are available on the [Hexo website](https://hexo.io/themes/).

Once you have chosen a theme, for instance [Cactus](https://github.com/probberechts/hexo-theme-cactus), you have to install it. Follow the instructions as described on the github page to achieve this.

#### Creating posts for Hexo

So now that you have successful created your blog, its time to write your first post!

To create a new post, use the following command:

```bash
hexo new post "My first post!"
```

This created file will be located at  `source/_posts`. You can find more information about   writing posts [here](https://hexo.io/docs/writing.html).

You might want to consider the use of a Markdown editor such as [Stack Edit](https://stackedit.io) for generating your content.
