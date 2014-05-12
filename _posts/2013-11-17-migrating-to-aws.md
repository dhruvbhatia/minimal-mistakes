---
layout: post
title: "Migrating to AWS"
description: "Migrating from traditional web hosting to the cloud."
category: thoughts
tags: [aws, amazon web services, blog hosting, hosting migration, ec2, aws australia, aws kinesis]
image:
  feature: header-2.jpg
  credit: Jared Marshall
  creditlink: http://500px.com/JaredMarshall
---

Like most people, I'm one of those individuals that has a multiple internet side projects on the go at once. Effectively managing the infrastructure behind all my projects has been something that I have typically disregarded.

### Previous setup / Reasons for migrating

I have traditionally been hosting my projects on a mixture of VPS's from <a href="http://www.linode.com/" target="_blank">Linode</a> & standard managed hosting from <a href="http://www.ventraip.com.au/" target="_blank">VentraIP</a>. Both have been great and provide outstanding service, however this weekend I wanted to amalgamate the majority of my sites over to <a href="http://aws.amazon.com/" target="_blank">Amazon Web Services</a> for the following reasons:

* I wanted full `root` access to a box that was local and had low latency[^1].
* Amazon has been unrolling some *really exciting* technologies lately, like <a href="https://aws.amazon.com/kinesis/" target="_blank">Kinesis</a>, a high-scale, real-time data processing engine. Gimme gimme gimme!
* The ability to spin up instances on-demand and do some serious crunching is appealing.

Back in 2011, I had dabbled with AWS briefly, but at the time they didn't have an Australian offering. In addition, the AWS Console and documentation was lacking.

### AWS Services

I logged into the same AWS account that I had used in 2011, and was happy to see a much more polished experience:

<figure>
  <a href="/images/aws-console.png"><img src="/images/aws-console.png"></a>
  <figcaption>The AWS Console nicely presents all offerings and provides a description of each service.</figcaption>
</figure>

<br>
While I appreciate all their services on one centralised screen, the breadth of it may be overwhelming to some, so I've mapped their services to more common terminology:

| AWS Service         |      | Standard Definition |
|:--------------------|------|-------------------------:|
| EC2                 |      | VPS |
|----
| ElasticIP                 |      | Gives an EC2 instance a dedicated IP (so your DNS can point to it) |
|----
| Route 53            |      | DNS Manager |
|----
| S3                  |      | File storage (also static site hosting) |
|----
| Cloudfront                  |      | Edge caches your files so that they are globally distributed - needs an `origin` to read from |
|----
| RDS                  |      | Managed DB (PostgreSQL, MySQL *(bleh!)* or Oracle) |
{: rules="groups"}
<figcaption>The table above explains the core AWS offerings.</figcaption>

<br>

> Hot Tip: A typical use case involves simply pointing an existing domain name over to a VPS that runs on AWS. In that case, you'd just want to sign up to an `EC2` instance, give it an `ElasticIP`, set it up to serve your files using the webserver of your choice (I like nginx), and create the relevant A Record(s) in your existing DNS Manager to point to the `ElasticIP`.

I'd encourage you to try the various AWS services and see what fits your use case.

### What am I using on AWS?

This site itself using quite a few of the different offerings listed above. My domain points to `Route 53's` DNS servers, and I'm serving this page through a `Cloudfront` distribution that reads from an origin that is stored on `S3`. This means that this page is **cached on all of Amazon's regions and will load fast, irrespective if you are from Tennessee or Timbuktu**. See <a href="http://docs.aws.amazon.com/gettingstarted/latest/swh/website-hosting-intro.html" target="_blank">this guide</a> for instructions on how I achieved this setup.

I obviously don't want *just* want to serve static content, though!

One of the sites I migrated also has a use case in which I need to run a <a href="http://www.playframework.com/" target="_blank">Scala-based Play Framework JVM social app</a> that connects to a PostgreSQL backend and handles a ton of concurrent connections while performing lots of IO. For this setup, I'm using one of <a href="http://techcrunch.com/2013/11/14/amazon-launches-its-fastest-ec2-instance-type-yet/" target="_blank">the new</a> `EC2 Compute instances with SSD (c3.large)` and `a RDS PostgreSQL` instance. In conjunction, I am using `S3` and `Cloudfront` to store and edge cache user uploads. So far I am **very happy** with the performance of this setup[^2].

So far, AWS is ticking all the boxes. :)

### Final words

I still have a few low traffic sites hosted on Linode and VentraIP, however after my weekend dealings with AWS, I am confident I will continue to use AWS for the majority of my future projects.

To conclude, AWS is a) local; b) a lot easier to utilise today than what it was when I first tried in 2011 and c) coming up with compelling new services regularly.

<hr />

[^1]: I'm in Melbourne, Australia. Amazon offer a data centre in Sydney (< 40 ms), while the closest Linode data centre is in Tokyo (~200 ms).
[^2]: I originally configured this setup with an `EC2 m1.small` instance, however the CPU constraints hindered my app's performance.