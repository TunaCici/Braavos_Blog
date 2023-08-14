---
title: "How to Install ElasticSearch on Ubuntu 22.04"
tags:
- tutorial
- elasticsearch
- ubuntu
date: 2021-12-17T00:00:00Z
header_image_alt: "ElasticSearch and Ubuntu"
---

**Note: Updated for the latest version 8.4.2 and tested on Ubuntu 22.04.**

ElasticSearch is a search engine built on Apache Lucene and developed in Java. It is basically a database but heavily focused on indexing and searching. ElasticSearch, indexes the database regularly to make searching extremely fast and easy. You can search within these indexes using it’s official application called Kibana or the APIs provided. But first you need an ElasticSearch server

Here in this tutorial I will explain how you can download, install and configure ElasticSearch to help you get started with it.

_Note: A lot of big companies and start-up businesses started to implement ElasticSearch in their services one way or another. Learning it will be very helpful for both your career and your personal experience._

**Step 1 — Download Java using the ‘apt’ command.**
===================================================

ElasticSearch is developed in Java. For this reason you need either the OpenJDK or the Oracle’s official JDK. I will be using the OpenJDK.

To install Java Runtime:

```bash
sudo apt upgrade
sudo apt install default-jre
```

After the install, check if Java is installed:

```bash
java -version

Output:  
openjdk version "11.0.16" 2022-07-19
```

You also need the Java Compiler. To install it:

```
sudo apt install default-jdk
```

After the install, check if Java Compiler is installed:

```bash
javac -version

Output:  
javac 11.0.16
```

If everything went well congratulations! Your Java is now ready.

**Step 2 — Download the ElasticSearch**
=======================================

Unlike Java, you cannot install ElasticSearch on Ubuntu with the ‘apt’ command. For this reason we will use the official archive to download and install ElasticSearch. You need to specify which version and architecture you want to use. There are two architectures x86\_64 and aarch64.

_You can get the version and other information you need from the official ElasticSearch:_

```text
https://www.elastic.co/downloads/past-releases/
```

If your CPU is Intel or AMD: **use x86\_64**

If your CPU is Apple M1/M2: **use aarch64**

You can specify the version and architecture like this:

```text
elasticsearch-8.4.2-linux-aarch64.tar.gz
```

Download the official ElasticSearch archive:

```bash
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-8.4.2-linux-aarch64.tar.gz
```

Download the SHA512 checksum for error checking:

```bash
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-8.4.2-linux-aarch64.tar.gz.sha512
```

Check if the download archive is valid and complete:

```bash
shasum -a 512 -c elasticsearch-8.4.2-linux-aarch64.tar.gz.sha512

Output:
elasticsearch-8.4.2-linux-aarch64.tar.gz.sha512: OK
```

**Step 3 — Install and Run the ElasticSearch**
==============================================

We are now ready to install ElasticSearch. Since it uses Java we will not need to use cmake or any other tools like that.

Extract the downloaded archive (this will be your ElasticSearch Home):

```bash
tar -xzf elasticsearch-8.4.2-linux-aarch64.tar.gz
```

Change directory into the extracted folder:

```bash
cd elasticsearch-8.4.2/
```

To start the ElasticSearch server:

```bash
./bin/elasticsearch
```

The first start-up process might take sometime just wait a little bit. You need to see something like this to **make sure the ElasticSearch server is up and running**:

```text
✅ Elasticsearch security features have been automatically configured!
✅ Authentication is enabled and cluster connections are encrypted.
ℹ️ Password for the elastic user (reset with `bin/elasticsearch-reset-password -u elastic`):
wTdmHPQwWxXMGm*riZVF
```

Important thing to note here is that ElasticSearch server automatically creates passwords and such. They are marked as **bold** characters. Make sure to save them somewhere safe as you cannot get them again.

Both the ElasticSearch server user&password and the Kibana token should be displayed on the terminal outputs. **SAVE THEM!**

You can test if the server is up and running by going to this address using any type of web browser.

```text
https://localhost:9200
```

Do not worry if your browser says ‘Unsecure Website’ or something like that. It gives that security warning because the certificate is self-signed and cannot be verified online. You can ignore this warning and proceed.

When asked for username and password, enter the information that you saved previously from the terminal outputs.

If it returns a JSON file saying “You Know, for Search” then congratulations my friend you now have ElasticSearch installed!

Thank you for following my guide. Right now you can not see what is going on inside your ElasticSearch server but we can do that using the Kibana. Soon, I will have another guide about it ❤.

