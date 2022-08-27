# hasura-migration
- Install version specific CLI for **hasura** based on source/target hasura instances. [link](https://hasura.io/docs/latest/hasura-cli/install-hasura-cli/)
- Run command `hasura init source-hasura --endpoint https://source-hasura.app --admin-secret source-hasura-secret` to download metadata information for Hasura
- Check & make sure all the environment variables are present in the **target** hasura environment file / docker environment variables registry.
- If **target** hasura is supposed to use different DB connection, make sure the target hasura installation directory / docker environment variable registry have the DB credentials configured properly. Also ensure that this new DB connection has all the databases/schemas created already in it.
- To upload & deploy these migrations to target server, use command `hasura deploy --endpoint http://target-hasura.app --admin-secret target-hasura-secret`

# Fusion Auth Data migration
This [tool](https://github.com/choxx/fa-scripts) can be used for Fusion Auth data migration. Please go through the README there.

# Postgres Migration
- ssh on to the **source server** where Postgresql is installed.
- Ensure you have user credentials for the DB to export the dump.
- On command line, hit `pg_dump -U {username} -p {port} -h {host} {DB name} > dbdump.sql`. This will export the dump with name `dbdump.sql` file into the current directory.
- ssh on to the **target server** where new Postgres instance is available.
- Ensure you have user credentials for the DB to import the dump. (Ideally the credentials should be kept same to replicate the exact migration)
- Copy/move this dump file to **target server**.
- On command line, hit `psql -U {username} -p {port} -h {host} {DB name} < dbdump.sql`