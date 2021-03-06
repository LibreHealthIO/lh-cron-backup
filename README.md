# A docker image to perform cron backups of data in Docker containers

[![pipeline status](https://gitlab.com/librehealth/lsc/lh-docker-cron-backup/badges/master/pipeline.svg)](https://gitlab.com/librehealth/lsc/lh-docker-cron-backup/commits/master)

This is based off of [OpenMRS Docker Cron Backup Image](https://github.com/openmrs/openmrs-contrib-docker-cron-backup).

It retains the history, but the fork relationship is removed. The decision to push our own image is so that we have control over the image.

It tars directories DIR1, DIR2, etc. defined in the DIRS environment variable in the format DIR1:NAME1,DIR2:NAME2,... under /backup/NAME1-Y-m-d_H-M-S.tar.gz by defined SCHEDULE.

Example of use with docker-compose:
```
  backup:
    image: registry.gitlab.com/librehealth/lsc/lh-docker-cron-backup:latest
    depends_on:
      - api
    volumes:
      - mongo_data:/mongo_data
      - solr_data:/solr_data
      - ${BACKUP_DIR-./backups}:/backup
    environment:
      - DIRS=/mongo_data:mongo,/solr_data:solr
      - SCHEDULE=0 0 * * *
    restart: always
    healthcheck:
      test: "exit 0"
```

In order to backup on-demand run:
```
docker-compose run --rm backup bash backup.sh backup
```

In order to restore backups from the BACKUP_DIR run:
```
docker-compose run --rm backup bash backup.sh restore 2017-09-27_00-00-01
```


It's also possible to call scripts:
```
  backup:
    image: registry.gitlab.com/librehealth/lsc/lh-docker-cron-backup:latest
    volumes:
      - scripts:/scripts
    environment:
      - SCRIPT=/scripts/my_backup_script.sh
      - SCHEDULE=0 0 * * *
    restart: always
    healthcheck:
      test: "exit 0"
```
