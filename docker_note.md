## Note for docker

## Create gitlab
```
sudo docker run --detach --hostname domain.com -p 0.0.0.0:443:443 -p 0.0.0.0:8880:80 -p 0.0.0.0:2020:22   --name gitlab gitlab/gitlab-ce:latest
```
### How to git with custom gitlab
```
git clone ssh://git@domain:8880/hahaproject.git
```

## Create Mongo server
```
sudo docker run --name mongoserver -e MONGO_INITDB_ROOT_PASSWORD_FILE=/run/secrets/mongo-root -d mongo
```
