# newsleak-docker

Docker configuration to run new/s/leak

# Usage

Install docker.

Checkout this repository.

```
git clone https://github.com/tudarmstadt-lt/newsleak-docker.git
cd newsleak-docker
``` 

Run docker-compose. You may want to edit the `postgres.env` file to setup your own db password.

```
docker-compose up -d
```

Setup configuration. Edit `volumes/ui/newsleak.properties`. First, copy it to the volume location `./volumes/ui` and set write permissions.

```
docker exec -it newsleakdocker_newsleak-ui_1 cp -r /opt/newsleak/preprocessing/conf /etc/settings
docker exec -it newsleakdocker_newsleak-ui_1 chmod -R 777 /etc/settings/conf
```

Then, edit the file with your favorite text editor.

```
nano volumes/ui/conf/newsleak.properties
```

You may use the example data or copy your own data files into the `volumes/ui` folder and point to them in the properties file. If you changed the db password in the previous step, change it in the properties file, too.

Finally, run preprocessing for information extraction.

```
docker exec -it newsleakdocker_newsleak-ui_1 sh -c "cd /opt/newsleak/preprocessing && java -Xmx10g -jar target/preprocessing-0.5-jar-with-dependencies.jar -c /etc/settings/conf/newsleak.properties"
```

Open the UI application in your browser

```
http://localhost:9000
```
