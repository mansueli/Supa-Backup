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
        uses: mansueli/supa-backup-action@v0.0.3
        with:
          supabase_url: ${{ secrets.SUPABASE_URL }}
          supabase_password: ${{ secrets.SUPABASE_PASSWORD }
      - uses: stefanzweifel/git-auto-commit-action@v4
        with:
          commit_message: Supabase backup
         
```
In this example, the Supa-Backup action is run every day at midnight. It runs on the latest version of Ubuntu and performs two steps: checking out your repository and running the Supa-Backup action.

You'll also need to set the following secrets in your repository settings to your action secrets in GitHub:

 - `SUPABASE_PASSWORD`
 - `SUPABASE_URL`

Contributing
If you find a bug or want to suggest a new feature, please open an issue or submit a pull request. We welcome contributions from the community and are happy to help you get started.

License
Supa-Backup is released under the [Apache 2.0 License](https://github.com/mansueli/Supa-Backup/blob/main/LICENSE).
