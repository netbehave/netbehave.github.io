---
layout: post
title:  "Introducing NetBehave!"
date:   2018-11-12 19:30:00 -0500
categories: netbehave
---
I'm happy to announce the official release of the NetBehave that I presented at the BSides Ottawa 2018 conference on November 8th, 2018 in, obviously, Ottawa, Canada.

The code for the system is now live. You can get a copy of the presentation of the [here][[netbehave-bsidesottawa2018-slides] .


**Getting started**

Step 1. Get a copy of the code (download zip/unzip or git clone)

{% highlight shell %}
git clone https://github.com/netbehave/netbehave.git
{% endhighlight %}

Step 2. Edit docker-compose.yml file to edit the ENV variable values for netbehave-alerting and 	netbehave-core.
{% highlight docker %}
...
    netbehave-alerting:
        image: netbehave-alerting
        build: ./netbehave-alerting
        environment:
        - ENV SMTP_USER=account@example.com
        - ENV SMTP_PASSWORD=PASSWORD 
        - ENV SMTP_RECIPIENT=account@example.com
        - ENV SMTP_SERVER=smtp.example.com
        - ENV SMTP_PORT=2525 # Is 25 blocked by default?
        volumes:
        - logs:/var/log
        - data:/opt/netbehave

        
    netbehave-collector-nfipfix:
        image: netbehave-collector-nfipfix
        build: ./netbehave-collector-nfipfix
        expose:
        - "2055/udp"
        ports:
        - "2055:2055/udp"
        environment:
        - ENV LOCAL_NETWORKS="home=192.168.0.;docker=172.17.;NAT=1.1.1.1"
...
{% endhighlight %}

Step 3. Go to the folder and run: docker-compose build [sudo may be required]

{% highlight shell %}
docker-compose build
{% endhighlight %}

Step 4. Go to the folder and run: docker-compose up -d [sudo may be required]

{% highlight shell %}
docker-compose up -d
{% endhighlight %}



[netbehave-bsidesottawa2018-slides]: /files/netbehave-bsidesottawa2018-slides.pdf