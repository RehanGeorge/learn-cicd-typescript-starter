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

## Cleanup Instructions

- The group was created in AWS Mumbai - name `main-group`
- The db created was named `notely-db`
