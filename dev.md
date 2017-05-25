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
    
# 集成开发环境

    docker-machine create --engine-registry-mirror=https://2wud1uac.mirror.aliyuncs.com -d virtualbox gerrit
    docker network create dev_default

# LDAP
    docker pull openfrontier/openldap-server
    docker volume create ldap_conf
    docker volume create ldap_data
    docker run --name ldap -d \
        -v ldap_conf:/etc/ldap -v ldap_data:/var/lib/ldap -p 389:389 \
        -e SLAPD_PASSWORD=mcafee123 -e SLAPD_DOMAIN=yihuacomputer.com \
        --network dev_default \
        openfrontier/openldap-server
        
    # export ldif
    ldapsearch -Wx -D "cn=Manager,dc=yihuacomputer,dc=com" -b "dc=yihuacomputer,dc=com" \
        -H ldap://10.2.8.220 -LLL > ldap_dump-20170524-1.ldif
    # import ldif
    ldapadd -Wx -D "cn=admin,dc=yihuacomputer,dc=com" -H ldap://192.168.99.100 -f ldap_dump-20170525-1.ldif
    
用JXplorer导入时移除ldif根节点

# Gerrit

    docker pull openfrontier/gerrit
    docker run --name gerrit -d -p 8080:8080 -p 29418:29418 openfrontier/gerrit
    
    docker volume create gerrit
    docker run --name gerrit -d \
        -p 8080:8080 -p 29418:29418 \
        -v gerrit:/var/gerrit/review_site \
        -e AUTH_TYPE=LDAP \
        -e LDAP_SERVER=ldap://ldap \
        -e LDAP_ACCOUNTBASE=dc=yihuacomputer,dc=com \
        --network dev_default \
        openfrontier/gerrit
