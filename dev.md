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
