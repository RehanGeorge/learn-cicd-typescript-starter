# Turso

Turso is a cloud provider that specializes in hosting serverless SQLite-like databases (technically it's their fork, libsql). It's a great fit for Notely because it has a very generous free tier, and it's easy to use.

## Assignment

### Create an Account

- Create an account [here](https://turso.tech)
- Complete the onboarding process
- Create a group in any location
- Create your first database with the name: notely-db

### Install and Configure the Turso CLI

Here's the [full quickstart guide in the Turso docs](https://docs.turso.tech/cli/introduction), but in a nutshell:

#### Install the CLI

```bash
# macOS
brew install tursodatabase/tap/turso

# Linux / WSL
curl -sSfL https://get.tur.so/install.sh | bash
```

#### Login

```bash
turso auth login
```

#### Show your databases

```bash
turso db list
```

#### Connect to your database

```bash
turso db shell notely-db
```

#### Run a simple SQL query

```sql
SELECT sqlite_version();
```

#### Create a test table

```sql
CREATE TABLE test (
  id INT,
  name TEXT
);
```

#### Show the table

```sql
SELECT name FROM sqlite_master WHERE type='table' ORDER BY name;
```

#### Delete the table

```sql
DROP TABLE test;
```

Exit the shell with `ctrl+c`.

This SQL shell is a great way to manually interact with your database for debugging purposes, but our server will of course interact with it programmatically.

### Run CLI Tests

Run and submit the CLI tests from the root of your repo.

## Migrations

Notely uses drizzle to manage database migrations, that is, the process of updating the database schema so that the server can function properly. Drizzle has already been added as a dependency and the initial schema files have been created. So we can focus purely on running migrations through CD.

If you're interested in how this was configured you can check out [drizzle's getting started docs](https://orm.drizzle.team/docs/get-started).

### Assignment

1. On the Databases tab in Turso, click on your database and copy the URL. It should look something like this:
   ```
   libsql://notely-db-YOURNAME.turso.io
   ```

2. Paste it into a `.env` file in the root of your repo under the key `DATABASE_URL`.
   The final connection configuration line will look something like this:
   ```
   DATABASE_URL=libsql://notely-db-YOURNAME.turso.io
   ```

3. Don't forget to add your `.env` file to your `.gitignore`

4. Use the Turso CLI to get an authentication token:
   ```bash
   turso db tokens create notely-db
   ```

5. Add the token to your connection string in the `.env` file:
   ```
   DATABASE_URL=libsql://notely-db-YOURNAME.turso.io?authToken=YOURTOKENHERE
   ```

6. Run the migrations, I've added the commands to the package.json for you:
   ```bash
   npm run db:migrate
   ```

7. Once successful, use the Turso CLI to ensure the tables are there:
   ```bash
   turso db shell notely-db
   ```
   
   Then run these queries:
   ```sql
   PRAGMA table_info(users);
   
   PRAGMA table_info(notes);
   ```

If everything looks good, you're ready to move on to the next lesson!

## Cleanup Instructions

- The group was created in AWS Mumbai - name `main-group`
- The db created was named `notely-db`
