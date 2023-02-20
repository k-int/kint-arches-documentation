# Installing CouchDB as a docker image

## Steps
1. Clone the official couchDB repo

```
git clone https://github.com/apache/couchdb-docker.git
```

2. Create the following docker-compose.yml file in the root of the repo

```
version: "3.3"

services:
  couchdb:
    image: couchdb-one
    ports:
      - "5984:5984"
    volumes:
      - couchdb-data:/var/lib/couchdb
    networks:
      - internal
    environment:
      COUCHDB_USER: admin
      COUCHDB_PASSWORD: admin
    restart: always

networks:
  internal:

volumes:
  couchdb-data:
```

3. cd into the correct version you are wanting to install. E.g. `cd 3.2.1` and build the image using

```
docker build -t couchdb-one .
```

4. Once built, cd back into the root of the directory and run:

```
docker-compose up -d
```

5. To see if it is running use `docker ps`
