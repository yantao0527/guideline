# Git Server

    docker pull jkarlos/git-server-docker:latest
    docker volume create git_keys
    docker volume create git_repos
    docker run -d --name gitserver \
        -p 2222:22 \
        -v git_keys:/git-server/keys \
        -v git_repos:/git-server/repos \
        jkarlos/git-server-docker:latest

# Git repo

    docker cp id_rsa.pub gitserver:/git-server/keys/<username>.pub
    docker exec -it sh
        cd /git-server/repos
        git init --bare <repo>.git
    docker restart gitserver
    
    git remote add <name> ssh://git@192.168.99.100:2222/git-server/repos/<repos>.git
    git push <name> master

# Jenkins master

    docker pull jenkins:alpine
    docker volume create jenkins_home
    docker run -d --name jenkins -p 8080:8080 -p 5000:5000 jenkins:alpine

# Jenkins slave

    sudo adduser jenkins
    sudo usermod -aG docker jenkins
    sudo yum -y install java-1.8.0-openjdk
    
# Gerrit

    docker-machine create --engine-registry-mirror=https://2wud1uac.mirror.aliyuncs.com -d virtualbox gerrit
    docker pull openfrontier/gerrit
    docker run --name gerrit -d -p 8080:8080 -p 29418:29418 openfrontier/gerrit
    
    docker run --name gerrit -d \
        -p 8080:8080 -p 29418:29418 \
        -e AUTH_TYPE=LDAP \
        -e LDAP_SERVER=ldap://10.2.8.220 \
        -e LDAP_ACCOUNTBASE=dc=yihuacomputer,dc=com \
        openfrontier/gerrit
