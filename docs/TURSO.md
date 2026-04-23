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

## Migrate Automatically

There are many ways that teams handle database migrations in continuous deployment environments.

Some teams prefer to run migrations as part of the deployment process, others run them manually, and some have more complete systems featuring downward migrations. Downward migrations provide one more tool for recovery in case of a problem with a recent database schema change. For simplicity, this course runs migrations via our GitHub Actions deployment script.

We'll simply migrate the DB to the latest version every time we deploy. That way, if the code we're deploying requires a new schema, we'll always have it. The only time this can be a problem is if a migration makes backward-incompatible changes to the schema (like dropping a table). If the currently running application needs a table that we drop, it will stop working until the new code is deployed.

That's bad, downtime is bad.

To avoid those scenarios, we could simply roll out the code that stops relying on the hypothetical table first, then in the next deployment remove the table in a migration.

### Assignment

1. Add a `DATABASE_URL` secret to your GitHub repo's Settings → Secrets and variables → Actions. Double-check the URL itself as the migration will fail if quotes are used around the URL!

2. Add a new section to the `cd.yml` deploy job, that grabs the secret and provides it to the rest of the job environment.

   ```yaml
   jobs:
     deploy:
       name: Deploy
       runs-on: ubuntu-latest
   
       # This part
       env:
         DATABASE_URL: ${{ secrets.DATABASE_URL }}
   ```

3. Update the cd workflow in your GitHub Actions `cd.yml` file to run migrations before deploying the application.
   I'd recommend running it after the Docker image is built, but before the deployment of the image. That accomplishes two things:

   - We won't run the migration if there is a problem building the image.
   - The migration will be live before the new code is deployed.

4. Use `git diff` / `git diff HEAD` to check for any sensitive credentials like database connection strings, that may have slipped into your source code. We've taken the liberty of `.gitignore`'ing the `.env` file already, but `git diff` can point out credentials mistakes even before you commit code changes.

5. Commit the code, push it to GitHub, then make sure the workflow runs as expected.

6. Paste the URL of your GitHub repo into the box and run the GitHub checks.

## Using the DB

Migrations are done! Now let's configure our Cloud Run app to use the database.

### Assignment

1. Go to the secrets manager in GCP (enable the API if you have to) and create a new secret:
   - Name: `notely_db_password`
   - Paste your database URL into the value field

2. Go back to the Cloud Run page and select your app. Click "Edit & Deploy New Revision" and then under "Container(s)" → "Variables & Secrets" → "Reference A Secret". Supply the name `DATABASE_URL`. Select the `notely_db_password` secret. Select latest version. Select Done.

3. Select Deploy. (We expect this deployment to fail with a permission error.)

4. Back in the Google Cloud IAM & Admin dashboard, edit the Cloud Run Deployer service account and assign it a new role, Secret Manager Secret Accessor.

5. Return to the Cloud Run notely service and edit the service once more. Select the Security tab. We want to use the Cloud Run Deployer service account.

6. Select Deploy again. (This deployment should work better.)

7. Let's test the application deployment.

You'll know the app is fully operational when:

- You can create new users
- You can create new notes
- Refreshing the page indicates that you are "logged in"
- You can view notes that you create

Run and submit the CLI tests and use the URL of your service (e.g. `bootdev config base_url https://test-vo4kpyh36a-uc.a.run.app/`).

## Cleanup Instructions

- The group was created in AWS Mumbai - name `main-group`
- The db created was named `notely-db`
- Remember to shut down your notely project in GCP when you're done playing with it, to reduce extra bank charges.
- Remember to delete your Turso database as well.
