**NOTE:** Until the `staff-blog` rename to `libi` is complete, the documentation may refer to `/apps/git/staff-blg` in the direcotry paths, if you have the code checked out `/apps/git/libi`, use that as the path instead. 

# Local devevlopment using Docker

## Starting the docker environment

### Prerequisites

Clone the staff-blog-configuration to `/apps/git/staff-blog-configuration`:

    cd /apps/git/
    git clone git@bitbucket.org:umd-lib/staff-blog-configuration.git

```
# From the staff-blog directory
docker-compose up
```

**Note:** If you have not built the staff-blog image on you workstation before, `docker-compose up` will build the image on first run and that may add-up the container start time on the first. Subsequent runs will be faster.

The staff-blog application should be available at http://localhost:8000

You can terminiate the container with `ctrl+c`

## Running in background mode
Start the containers:
```
# From the staff-blog directory
docker-compose up -d
```

Stop the containers:
```
# From the staff-blog directory
docker-compose down
```


## Running commands on the drupal container

To run commands on the docker, you need to first know the docker container id.

```
STAFF_BLOG_CONTAINER=$(docker container ls -q --filter name=staff-blog_drupal)
```

See logs:
```
docker logs -f $STAFF_BLOG_CONTAINER
```

Run composer:
```
docker exec -w /app/web/blog $STAFF_BLOG_CONTAINER composer install
```

Run drush command
```
docker exec -w /app/web/blog $STAFF_BLOG_CONTAINER drush cr
```

Connect a shell to the container
```
docker exec -it $STAFF_BLOG_CONTAINER bash
```

See confluence for more useful docker commands:



## Restoring database dump to postgres container

```
DB_CONTAINER=$(docker container ls -q --filter name=staff-blog_db)
docker cp /path/to/db.dump $DB_CONTAINER:/tmp/
docker exec $DB_CONTAINER psql -U drupaluser -d drupaldb -f /tmp/db.dump
```

# Building the staff-blog image for deployment


## Build

From the `/apps/git/staff-blog` (or `/apps/git/libi` depending where the code is cloned to) directory:

    docker build .

To build and tag:

    docker build -t staff-blog:<TAG> .

**Note**: If the image needs to include new changes in the staff-blog-configuration repository, ensure that latest changes are pull'ed to the sync directory before building the image.

    cd sync
    git checkout <necessary_branch>
    git pull

## Deploying the image to the UMD Nexus Docker Repository

See [Storing images to the UMD Private Docker Registry](https://confluence.umd.edu/display/LIB/Storing+images+to+the+UMD+Private+Docker+Registry)