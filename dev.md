# Git

    docker pull jkarlos/git-server-docker:latest
    docker run -d --name gitserver \
        -p 2222:22 \
        -v ~/git-server/keys:/git-server/keys \
        -v ~/git-server/repos:/git-server/repos \
        jkarlos/git-server-docker:latest
