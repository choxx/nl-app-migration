# hasura-migration
- Install version specific CLI for **hasura** based on source/target hasura instances. [link](https://hasura.io/docs/latest/hasura-cli/install-hasura-cli/)
- Run command `hasura init source-hasura --endpoint https://source-hasura.app --admin-secret source-hasura-secret` to download metadata information for Hasura
- Check & make sure all the environment variables are present in the **target** hasura environment file / docker environment variables registry.
- If **target** hasura is supposed to use different DB connection, make sure the target hasura installation directory / docker environment variable registry have the DB credentials configured properly. Also ensure that this new DB connection has all the databases/schemas created already in it.
- Navigate to **source-hasura** directory and in `config.yaml` file, configure the variables `endpoint` & `admin_secret` to point to the **target** hasura.
- To upload & deploy these migrations to target server, use command `hasura deploy` in the **source-hasura** directory.

# Fusion Auth Data migration
This [tool](https://github.com/choxx/fa-scripts) can be used for Fusion Auth data migration. Please go through the README there.

# Postgres Migration
## Export
- ssh on to the **source server** where Postgresql is installed.
- Ensure you have user credentials for the DB to export the dump.
- Get into the docker CLI for postgres: `docker exec -it psql bash` & create a directory (e.g. `/root/psql-dump`). [here psql is the name of Postgres container]
- Navigate to `/root/psql-dump` & export for each table's INSERT queries (insert queries only because we have schema migrated via Hasura CLI already):
  ```
  pg_dump -U mpcuplakshyauser --column-inserts --data-only --table=assessment_results prerakbalakdb > assessment_results.sql
  pg_dump -U mpcuplakshyauser --column-inserts --data-only --table=assessment_visit_results prerakbalakdb > assessment_visit_results.sql
  pg_dump -U mpcuplakshyauser --column-inserts --data-only --table=competency_mapping prerakbalakdb > competency_mapping.sql
  pg_dump -U mpcuplakshyauser --column-inserts --data-only --table=employees prerakbalakdb > employees.sql
  pg_dump -U mpcuplakshyauser --column-inserts --data-only --table=form_config prerakbalakdb > form_config.sql
  pg_dump -U mpcuplakshyauser --column-inserts --data-only --table=homelearning_event prerakbalakdb > homelearning_event.sql
  pg_dump -U mpcuplakshyauser --column-inserts --data-only --table=mentors prerakbalakdb > mentors.sql
  pg_dump -U mpcuplakshyauser --column-inserts --data-only --table=school prerakbalakdb > school.sql
  pg_dump -U mpcuplakshyauser --column-inserts --data-only --table=school_list prerakbalakdb > school_list.sql
  pg_dump -U mpcuplakshyauser --column-inserts --data-only --table=school_visit prerakbalakdb > school_visit.sql
  pg_dump -U mpcuplakshyauser --column-inserts --data-only --table=start_learning_event prerakbalakdb > start_learning_event.sql
  pg_dump -U mpcuplakshyauser --column-inserts --data-only --table=student_install prerakbalakdb > student_install.sql
  ```
- Now we have all the dump files for each table. Let's export these files out of docker container first, and then we can download them on local machine.
    - `docker cp <container-id>:/root/psql-dump ./psql-dump` #copying the dump files to host first
    - Now export these files present in `./psql-dump` directory to local machine (`scp root@server-ip:~/psql-dump/* ./`)

## Import
- Once we have the sql dump files, we have to upload them on to the target host first. (`scp ./psql-dump/* root@server-ip:~/psql-dump`)
- Upload the dump files to docker container: `docker cp ./psql-dump <container-id>:/root/psql-dump`
- Get into the docker CLI for postgres: `docker exec -it psql bash` & navigate to the psql-dump upload directory (e.g. `/root/psql-dump`)
- Import the records:
  ```
  psql -U mpcuplakshyauser prerakbalakdb < assessment_results.sql
  psql -U mpcuplakshyauser prerakbalakdb < assessment_visit_results.sql
  psql -U mpcuplakshyauser prerakbalakdb < competency_mapping.sql
  psql -U mpcuplakshyauser prerakbalakdb < employees.sql
  psql -U mpcuplakshyauser prerakbalakdb < form_config.sql
  psql -U mpcuplakshyauser prerakbalakdb < homelearning_event.sql
  psql -U mpcuplakshyauser prerakbalakdb < mentors.sql
  psql -U mpcuplakshyauser prerakbalakdb < school.sql
  psql -U mpcuplakshyauser prerakbalakdb < school_list.sql
  psql -U mpcuplakshyauser prerakbalakdb < school_visit.sql
  psql -U mpcuplakshyauser prerakbalakdb < start_learning_event.sql
  psql -U mpcuplakshyauser prerakbalakdb < student_install.sql
  ```