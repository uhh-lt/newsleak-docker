# newsleak-docker

Docker configuration to run new/s/leak

## Installation

Install docker.

Checkout this repository.

```
git clone https://github.com/uhh-lt/newsleak-docker.git
cd newsleak-docker
``` 

You may want to edit the `postgres.env` file to setup your own db password.

```
nano postgres.env
```

Set up docker network. Newsleak needs to access the Hoover docker containers in the same virtual network. Thus change the network prefix of newsleak `hoover_default` to whatever your hoover network name is join the hoover network. The network name is usually derived from the directory your Hoover docker-setup resides in.

```
nano docker-compose.yml
```

Start containers.

```
docker-compose up -d
```

## Data import

Newsleak is closely integrated with [Hoover](https://hoover.github.io), a software suite to extract texts from large collections of documents. We assume that you already have an instance of Hoover running on your machine which has imported the collection `testcollection`. To setup Hoover and extract texts from your collection, please follow the instructions on this page: [Hoover Docker Setup](https://hoover.readthedocs.io/en/latest/installation/).

1. Once Hoover is running, edit `volumes/ui/newsleak.properties` to configure the newsleak pre-processing pipeline. Open the file with your favorite text editor, e.g. nano.

```
nano volumes/ui/conf/newsleak.properties
```

You may use the example data or copy your own data files into the `volumes/ui` folder and point to them in the properties file. If you changed the db password in the previous step, change it in the properties file, too.

To select which languages newsleak should process, set `processlanguages` to a list of ISO 639-3 codes in the properties file. Also, set a default language to the lanaguage you expect to dominate the collection.

For very long documents, newsleak can split texts into paragraphs of a certain minimum length. To enable splitting of document texts set `paragraphsasdocuments=true`.

You also can use additional dictionaries to annotate your texts. Place dictionary text files (format: one term per line) into `volumes/ui/conf/dictionaries`. The dictionary category label will be inferred from the dictionary textfile name, e.g. `fck.eng` containing a list of English swear words, one per line, can be used to annotate occurrence of those swear words with the label FCK.

3. Run preprocessing for information extraction.

```
docker exec -it newsleak sh -c "cd /opt/newsleak && java -Xmx10g -jar preprocessing.jar -c /etc/settings/conf/newsleak.properties"
```

Open the UI application in your browser

```
http://localhost:9000
```

Login into the browser application with the credentials `user` and `password`. To set your own credentials, edit the file `volumes/ui/conf/application.production.conf`.

## Security

### Minimum setup

Basic security settings for the newsleak UI can be setup in the file `volumes/ui/conf/application.production.conf`:

1. Set custom user credentials for basic user authentication
2. Set a custom secret key for the Play application framework
3. Set custom passwords for the newsleak postgres database (`postgres.env`, `volumes/ui/conf/newsleak.properties`, `volumes/ui/conf/application.production.conf`)
4. Put everything behind a SSL proxy which only forwards to http://localhost:9000, if you want to enable access via the internet 

### More secure setup

For confidential data, newsleak is supposed to run on a local system or network without any internet connection. We strongly advise for the following procedding:

With internet connection:

1. Install the Hoover docker setup  and import the Hoover test collection.
2. Install the Newsleak docker setup and import the testcollection extracted by Hoover.
3. If everything works fine, disconnect form the internet.

Without internet connection:

4. Copy your data to the Hoover collection directory.
5. Import your as a new Hoover collection.
6. Import the new Hoover collection into Newsleak.
7. Set credentials for the newsleak app.
8. Now you are fine analyzing your content.


## Troubleshooting

1. Not enough RAM: Preprocessing will be slow or even abort, if your Docker setup has not enough memory. Allow to use at least 8 GB.
2. Different docker container names: docker-compose will use the directory name containing `docker-compose.yml` as a prefix for orchestrated containers (some special characters such as dashes are removed from the directory name beforehand). If you have placed it in a directory other than `newsleak-docker`, `mynewsleak` for instance, you need to change the commands above accordingly. Replace `newsleakdocker_newsleak-ui_1` with `mynewsleak_newsleak-ui_1` in steps 1 and 3.
3. Different docker network name: The newsleak docker containers need to share a virtual network with the Hoover containers. This is configured in the `docker-compose.yml` of newsleak. If your Hoover resides in a directory other tahn `hoover`, e.g. `hoover2`, then change the network name from `hoover_default` to `hoover2_default` and restart the containers `docker-compose restart`
