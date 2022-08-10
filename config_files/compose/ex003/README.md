docker compose up                       # start docker compose
docker compose up -d                    # start docker compose dettached
docker compose up -d --build            # start docker compose creating a new image
docker compose ps                       # show docker composes running
docker compose exec app bash            # access container

docker compose exec -u root app bash    # access container with root user

whoami                                  # check current user on container
cat /etc/passwd                         # check users on container
command1 & command2                     # run commands together
command1 && command2                    # run command1 first and then command2

git config --global -l                  # list git config