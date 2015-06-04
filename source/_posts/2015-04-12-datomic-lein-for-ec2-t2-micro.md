---
layout: post
title: "Create staging server using jvm with t2.micro"
description: "Create staging server using jvm with t2.micro"
category: "leiningen"
tags: [leiningen, datomic]
---

# Create staging server using jvm with t2.micro

## jvm is required many memories
I ran command `lein ring server`, not-enough-memory error has raised.  
I googled and found solution.  
[https://github.com/omcljs/om/issues/101](https://github.com/omcljs/om/issues/101)  

## memo for create staging on ec2 instance(amazon linux)

something like this(picked up from history)

```
sudo yum install leiningen
wget https://raw.githubusercontent.com/technomancy/leiningen/stable/bin/lein
mkdir bin
chmod a+x lein
./lein
ssh-keygen
export MY_DATOMIC_USERNAME="moge@gmail.com"
export MY_DATOMIC_PASSWORD="datomic_password"
sudo yum install java-1.8.0-openjdk.x86_64
ll /etc/alternatives/java
cd /etc/alternatives/
sudo mv /etc/alternatives/java{,.bak}
ln -s /usr/lib/jvm/jre-1.8.0/bin/java /etc/alternatives/java
sudo ln -s /usr/lib/jvm/jre-1.8.0/bin/java /etc/alternatives/java
lein ring server
```


