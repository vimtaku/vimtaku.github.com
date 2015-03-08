---
layout: post
title: "fig and rails env is too heavy, I found workaround(Mac OSX)"
description: "fig and rails env is too heavy, I found workaround(Mac OSX)"
category: "fig"
tags: [fig, docker, ruby, rails]
---

## (PS)
I quit use fig so I decieded to develop localhost. because I have no time to construct devenv.  
Firstly, this blog was quite good and effective for me.  
Sync is working fine, but suddonly, rsync was getting weird.  
Additionally, these rsync strategy is faster than sync folder,  
 but it is definetly slow than localhost.  
Localhost is super fast.  
And I faced problems as follows. 
 - rails console history or mysql history is gone away when fig restart.  
 - rails console mysql connection has gone away because of restarting db.  
For now, I think fig(docker compose) is not good as development environment.  
If you have identical dev env, please message to me.  

## TL;DR
 - virtual box disk io is not good because of file sync. so turn off.  
 -- ![http://gyazo.com/f02f61fea1a6f2d306c37d08b6553776](http://gyazo.com/f02f61fea1a6f2d306c37d08b6553776.png)  
 - use NFS, for sync your directories.  

## Environment
 - fig 1.0.0  
 - boot2docker v1.3.1  
 - docker container(rails)  
 - docker container(mysql)  

## Background & Problem
Recently, it is very easy to create development env because fig and docker.  
But I was starting development, I noticed it is verrrry slow... It's so annoying.  

## Workaround

### Turn off /Users directory sync
By default, /Users directory is synced with boot2docker.  
This sync is very slow so I turned it off, response speed is up.  

### Use NFS
I turn off sync, so I have to another method to sync my host's source code.  
I googled, and found [ this thread ](https://github.com/boot2docker/boot2docker/issues/64).  
This thread is quite good for me.  

I used [this solution](https://gist.github.com/Jupiterrr/5348f7f95df7de2888f0)
because I wanna use fig.  


#### sync.sh
```
!/bin/bash

set -u # prevent unbound variables
set -e # terminate on error

SSH_PORT=$(boot2docker config 2>&1 | awk '/SSHPort/ {print $3}')

# load rsync
boot2docker ssh tce-load -wi rsync

# ensure existance of .rsyncignore
touch .rsyncignore

function sync {
  # sync current directory to ~/share on the vm
  rsync -rlz --exclude-from=.rsyncignore -e "ssh -i $HOME/.ssh/id_boot2docker -p $SSH_PORT" --force --delete ./ docker@localhost:/home/docker/share
  echo "sync: $(date)"
}

sync
#fswatch -o . | xargs -n1 -I{} sync
```

This script is almost good, but in my case, the last command fswatch is don't affect.  
so I used simple command `watch -n 2 sh sync.sh`. it works for me.  
I wrote .rsyncignore something like below.  


#### .rsyncignore

```
tmp/
vendor/
```


#### Dockerfile

gem is installed global.


```
FROM ruby
RUN apt-get update -qq && apt-get install -y build-essential libpq-dev ruby-nokogiri vim
RUN mkdir /myapp
WORKDIR /myapp
ADD Gemfile /myapp/Gemfile
ADD . /myapp
RUN bundle install
```


#### fig.yml

```
db:
  image: mysql:5.6
  ports:
    - "3306:3306"
  environment:
    - MYSQL_ROOT_PASSWORD=hoge
  # volumes:
  #   - ./data:/var/lib/mysql

web:
  build: .
  command: bundle exec rails server --port=3000 --binding=0.0.0.0
  working_dir: /myapp
  dns: localhost
  volumes:
    - /home/docker/share:/myapp
  ports:
    - "3000:3000"
  expose:
    - 3000
  links:
    - db
```

In db section, volumes isn't need.  
In web section, volumes is attached from boot2docker to web container.  

That's it.  
