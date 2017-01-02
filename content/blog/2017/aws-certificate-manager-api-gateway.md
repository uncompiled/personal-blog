---
date:  2017-01-01
title: "How to Use AWS Certificate Manager with API Gateway"
description: "How to set up a custom domain name with AWS API Gateway"
tags: [ "aws", "ssl", "https", "certificate manager", "api gateway", "letsencrypt", "acme" ]
---

# TLDR: You can't.

If you're reading this, you're probably trying to figure out [how to set
up a custom domain name with AWS API Gateway](http://docs.aws.amazon.com/apigateway/latest/developerguide/how-to-custom-domains.html).
If you log into the AWS Console, you get some fancy HTML text input
fields asking you for the:

- Domain name
- Certificate name
- Certificate body
- Certificate private key
- Certificate chain

## (╯°□°）╯︵ ┻━┻

Why does this form exist when AWS already has a tool called
[Certificate Manager](https://aws.amazon.com/certificate-manager/)?
AWS Certificate Manager allows you to provision SSL/TLS certificates for free,
but those certificates only work with Elastic Load Balancers (ELBs) and 
CloudFront distributions.

**Note**: [Certificate Manager support for API Gateway is on the AWS Roadmap](https://forums.aws.amazon.com/thread.jspa?threadID=234686),
but until then, keep flipping tables.

## The Problem

Certificate Manager is the simplest way to get SSL/TLS certificates, but it's
so easy to use that I've become reliant on it for all my CA needs.
I started looking for ways to export certificates from Certificate Manager, but 
unfortunately, that's not an option.  The AWS documentation actually lists a bunch of
[third-party certificate authorities](https://www.dmoz.org/Computers/Security/Public_Key_Infrastructure/PKIX/Tools_and_Services/Third_Party_Certificate_Authorities/),
but most of them charge for a certificate.

<amp-img layout="responsive" width="375" height="210"
  src="/img/2017/helpme.jpg">

I could put an ELB or a CloudFront distribution in front of it, but the reason
that I'm using API Gateway in the first place is because I'm using AWS Lambda to
build a "serverless" application. It kind of defeats the purpose of deploying 
stateless microservices on AWS Lambda if you end up running nginx or a load 
balancer in front of it.

## The Solution

Use [Lets Encrypt](https://letsencrypt.org/)!

I've used [Certbot](https://certbot.eff.org/) 
with nginx on Linux, but since AWS Lambda is "serverless", I can't SSH into a server
to install anything.  Fortunately, there are a number of [third-party ACME clients](https://letsencrypt.org/docs/client-options/).
I chose to use the [google/acme client](https://github.com/google/acme).

The google/acme project's [README](https://github.com/google/acme/blob/master/README.md)
was sufficient to get everything set up, but I'll walk through the steps.

### Install the ACME client

If you have [Go](https://golang.org/) installed, you can run `go get -u github.com/google/acme` to install it.

Otherwise, download the [latest release for your operating system](https://github.com/google/acme/releases).

### Create an account with LetsEncrypt

```
mkdir -p ~/.config/acme
openssl genrsa -out ~/.config/acme/account.key 4096
acme reg mailto:your@email.com
```

`mailto:your@email.com` is the ACME contact argument, which is necessary in case the CA needs to notify you.

### Agree to the ACME CA Terms of Service

Check the status of your account with `acme whoami` and run `acme update -accept` to accept.

### Generate your private key using OpenSSL

```
openssl genrsa -out your.custom.domain.key 2048
```

The output will look something like this:
```
Generating RSA private key, 2048 bit long modulus
..............................................+++
...+++
```

This will create a 2048 bit private key inside a file called `your.custom.domain.key`

### Request the certificate

```
acme cert -k your.custom.domain.key -dns=true your.custom.domain
```

**Note**: change `your.custom.domain` to your domain name.

The DNS flag will use a DNS TXT record to prove that you own the domain
(instead of using the .well-known method). You should see output that looks like this:

`Add a TXT record for _acme-challenge.your.custom.domain with the value "PlcpEqnjIzhMlDsUKHFhUZx" and press enter after it has propagated.`

**Don't press enter.**

### Update your DNS records

Login to Route53 or other DNS provider and add the requested DNS record.
Once it has propagated, press enter.

You should see something like `cert url: https://acme-v01.api.letsencrypt.org/`,
but that doesn't matter because there will be a file called `your.custom.domain.crt`
in your current working directory.

### Add the Custom Domain into API Gateway

Let's go back to 1995 and fill in the Custom Domain Names form.

- **Domain name**: enter your `your.custom.domain`
- **Certificate name**: name this something easy to remember, like your custom domain name
- **Certificate body**: copy/paste the first certificate from `your.custom.domain.crt`
- **Certificate private key**: copy/paste from `your.custom.domain.key`
- **Certificate chain**: copy/paste the second certificate from `your.custom.domain.crt`

**Note**: It's fine to copy the parts that say `-----BEGIN CERTIFICATE-----` and `-----END CERTIFICATE-----`.

Click the Save button and you should see AWS create the CloudFront distribution for your custom domain.
