---
title: "How to Install Kibana on Ubuntu 22.04"
tags:
- tutorial
- kibana
- ubuntu
date: 2022-09-24T00:00:00Z
header_image_alt: "Kibana & Ubuntu"
---

Kibana is the front-end application for the ElasticSearch server. It provides an interface to your server and all it’s data. You can do many things like: adding data, removing data, searching through your database and creating graphs and visualizations.

If you use ElasticSearch, it is highly recommended that you also use Kibana too. They work excellent together. Writing applications that uses ElasticSearch becomes way easier if you use Kibana. It has a debug section where you can create queries which then can be used in your applications.

Some popular companies that use ElasticSearch and Kibana are: Airbnb, Nettlix, Stack Overflow, Blizzard, Medium and LinkedIn.

Here in this tutorial I will explain how you can download, install and configure Kibana to help you get started with it.

Note: I also have a guide on ElasticSearch installation. I recommend that you read it before this guide. But you can follow this tutorial without it.

**Throughout all of the steps, ElasticSearch server should be up and running**

_PS: A lot of big companies and start-up businesses started to implement Kibana & ElasticSearch in their services one way or another. Learning it will be very helpful for both your career and your personal experience._

Step 1— Download the Kibana
===========================

You can install Kibana using the ‘apt’ command but in this tutorial I will show you the ‘archive’ method. You need to specify which version and architecture you want to use. There are two architectures: **x86\_64** and **aarch64**.

_You can get the version and other information you need from the official ElasticSearch:_

```
_https://www.elastic.co/downloads/past-releases/_
```

If your CPU is Intel or AMD: **use x86\_64**

If your CPU is Apple M1/M2: **use aarch64**

You can specify the version and architecture like this:

```
kibana-8.4.2-linux-aarch64.tar.gz
```

Download the official Kibana archive:

```
wget https://artifacts.elastic.co/downloads/kibana/kibana-8.4.2-linux-aarch64.tar.gz
```

Download the SHA512 checksum for error checking:

```
wget https://artifacts.elastic.co/downloads/kibana/kibana-8.4.2-linux-aarch64.tar.gz.sha512
```

Check if the download archive is valid and complete:

```
shasum -a 512 -c kibana-8.4.2-linux-aarch64.tar.gz.sha512_Output:  
_kibana-8.4.2-linux-aarch64.tar.gz.sha512_: OK_
```

Step 3 — Install and Run the Kibana
===================================

We are now ready to install Kibana.

Extract the downloaded archive (this will be your Kibana Home):

```
tar -xzf kibana-8.4.2-linux-aarch64.tar.gz
```

Change directory into the extracted folder:

```
cd kibana-8.4.2/
```

To start the Kibana:

```
./bin/kibana
```

In the first start-up process Kibana will ask you to configure some settings. You need to see something like this in the terminal:

```
**i Kibana has not been configured.**Go to http://localhost:5601/?code=438065 to get started.
```

Go to the address using your browser. It will ask you to enter a token. You should have this token from your ElasticSearch server. _(See my ElasticSearch installation guide)._ Enter your token and click ‘**Configure Elastic**’

_If you do not have the token you can generate a new one. Go to your ElasticServer home and open up the terminal. Execute the following command to generate a new token._

```
_./bin/elasticsearch-create-enrollment-token -s kibana_
```

You can test if the server is up and running by going to this address using any type of web browser. When asked to login, use your ‘elastic’ account. You should have your ‘elastic’ account from your ElasticSearch server. _(See my ElasticSearch installation guide)_

```
https://localhost:5601
```

If you can successfully login then congratulations my friend you now have Kibana installed!

Thank you for following my guide. If you have any further questions feel free to ask❤.
