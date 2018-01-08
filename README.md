# newsleak-docker

Docker configuration to run new/s/leak

# Usage

Install docker

Checkout this repository

```
git clone https://github.com/tudarmstadt-lt/newsleak-docker.git
cd newsleak-docker
``` 

Run docker-compose. You may want to edit the postgres.env file to setup your own db password.

```
docker-compose up
```

Setup configuration

```
docker exec -it newsleakdocker_newsleak-ui_1 cp /opt/newsleak/preprocessing/conf/newsleak.properties /etc/settings
```

Run information extraction

```
docker exec -it newsleakdocker_newsleak-ui_1 sh -c "cd /opt/newsleak/preprocessing && java -jar target/preprocessing-0.0.1-SNAPSHOT-jar-with-dependencies.jar -c /etc/settings/newsleak.properties"
```

