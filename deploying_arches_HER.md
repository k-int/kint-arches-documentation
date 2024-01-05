# need to write properly

- different version of python for arches v6 - had to install 3.8.18
- write own docker compose (used some of arches dependency containers) for psql, es, rabbitmq, couchdb, memcache, tomcat, celery?
- clone arches and arches_her (renamed from arches-her to arches_her for simplicity and continuity)
- apache installed normally non containerised
- mentioned above but arches 6.2.6 + required memcache for django cache hence the addition to docker compose. need to `pip install pylibmc`
- remove mapbox api key as one of Farallon's - Arches customer should generate their own

Would be nice to write ansible script for all of that 
