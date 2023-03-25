# Supa-Backup

Supa-Backup is a GitHub action that creates a backup of your Supabase database and stores it in your repository. With this action, you can easily automate the process of creating backups and ensure that your data is safe and secure.

## Usage

To use Supa-Backup, you'll need to add the action to your GitHub workflow. Here's an example:

```yaml
name: Supa-Backup
on:
  # Allows you to run this workflow manually from the Actions tab
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]
  workflow_dispatch:
  schedule:
    - cron: '0 0 * * *' # Runs every day at midnight
jobs:
  backup:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v3
      - name: Supa-Backup
        uses: mansueli/supa-backup@v0.0.6
        with:
          supabase_url: SUPABASE_URL
          supabase_password: SUPABASE_PASSWORD
      - uses: stefanzweifel/git-auto-commit-action@v4
        with:
          commit_message: Supabase backup
         
```
In this example, the Supa-Backup action is run every day at midnight. It runs on the latest version of Ubuntu and performs two steps: checking out your repository and running the Supa-Backup action.

You'll also need to set the following secrets in your repository settings to your action secrets in GitHub:

 - `SUPABASE_PASSWORD`
 - `SUPABASE_URL`

#Alternatively, you can set-up this workflow on your project directly & use GitHub Secrets.

````yaml
name: Supa-backup
# Controls when the workflow will run
on:
  # Allows you to run this workflow manually from the Actions tab
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]
  workflow_dispatch:
  schedule:
    - cron: '0 0 * * *' # Runs every day at midnight
    
# A workflow run is made up of one or more jobs that can run sequentially or in parallel
jobs:   
 run_db_backup:
  runs-on: ubuntu-latest
  permissions:
      # Give the default GITHUB_TOKEN write permission to commit and push the changed files back to the repository.
      contents: write
  steps:
     - uses: actions/checkout@v3
       with:
         ref: ${{ github.head_ref }}
     - name: Postgres15 
       run: |
         sudo apt-get remove postgresql-client-common
         sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list'
         wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo tee /etc/apt/trusted.gpg.d/pgdg.asc &>/dev/null
         sudo apt update
         sudo apt install postgresql-15 postgresql-client-15 -y
         /usr/lib/postgresql/15/bin/pg_dump --clean --if-exists --quote-all-identifiers --schema '*' --exclude-schema 'extensions|graphql|graphql_public|net|pgbouncer|pgsodium|pgsodium_masks|realtime|supabase_functions|storage|pg_*|information_schema' -d postgres://postgres:${{ secrets.SUPABASE_PASSWORD }}@${{ secrets.SUPABASE_URL }}:6543/postgres > dump.sql
         
     - name: Tweaking the dump file    
       run: |
         sed -i -e 's/^DROP SCHEMA IF EXISTS "auth";$/-- DROP SCHEMA IF EXISTS "auth";/' dump.sql
         sed -i -e 's/^DROP SCHEMA IF EXISTS "storage";$/-- DROP SCHEMA IF EXISTS "storage";/' dump.sql
         sed -i -e 's/^CREATE SCHEMA "auth";$/-- CREATE SCHEMA "auth";/' dump.sql
         sed -i -e 's/^CREATE SCHEMA "storage";$/-- CREATE SCHEMA "storage";/' dump.sql
         sed -i -e 's/^ALTER DEFAULT PRIVILEGES FOR ROLE "supabase_admin"/-- ALTER DEFAULT PRIVILEGES FOR ROLE "supabase_admin"/' dump.sql
       shell: bash   

     - uses: stefanzweifel/git-auto-commit-action@v4
       with:
         commit_message: Supabase backup
````
Contributing
If you find a bug or want to suggest a new feature, please open an issue or submit a pull request. We welcome contributions from the community and are happy to help you get started.

License
Supa-Backup is released under the [Apache 2.0 License](https://github.com/mansueli/Supa-Backup/blob/main/LICENSE).
