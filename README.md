# Supa-Backup

Supa-Backup is a GitHub action that creates a backup of your Supabase database and stores it in your repository. With this action, you can easily automate the process of creating backups and ensure that your data is safe and secure. You can also copy our workflow example & deploy it directly to your repo. 

## Usage

To use Supa-Backup, you'll need to add the action to your GitHub workflow. Here's an example:

```
    steps:
      - name: Checkout
        uses: actions/checkout@v4
      - name: Supa-Backup
        uses: mansueli/supa-backup@v1.0.4
        with:
          supabase_url: 'postgresql://postgres:<pass>@db.<ref>.supabase.co:5432/postgres'
          file_prefix: 'test_' 
```
## Usage

### Workflow Example

Here's a workflow example that demonstrates how to use the Supa-Backup action & commit the backup to the repo:

```yaml
name: Supa-Backup
on:
  workflow_dispatch:
  schedule:
    - cron: '0 0 * * *' # Runs every day at midnight
jobs:
  backup:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4
      - name: Supa-Backup
        uses: mansueli/supa-backup@v1.0.4
        with:
          supabase_url: 'postgresql://postgres:<pass>@db.<ref>.supabase.co:5432/postgres'
          file_prefix: 'test_'
      - uses: stefanzweifel/git-auto-commit-action@v4
        with:
          commit_message: Supabase backup
```

In this example, the Supa-Backup action is run every day at midnight. It runs on the latest version of Ubuntu and performs two steps: checking out your repository and running the Supa-Backup action.

# Storage Backup Workflow (generates an [Artifact](https://docs.github.com/en/actions/using-workflows/storing-workflow-data-as-artifacts))

````yaml
name: SupaStorage-backup
on:
  workflow_dispatch:
  schedule:
    - cron: '0 */6 * * *'
jobs:
  backup:
    runs-on: ubuntu-latest
    env:
      SUPABASE_URL: https://project_ref.supabase.co
      SUPABASE_SERVICE_ROLE: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6InByb2plY3RfcmVmIiwicm9sZSI6InNlcnZpY2Vfcm9sZSIsImlhdCI6MTY3MDg4MTY2MSwiZXhwIjoxOTg2NDU3NjYxfQ.fOAQAZMEXOhUh2CDBKKXYrjm_RhB6DlMFVRn8-u_SLA
    permissions:
      contents: write
    steps:
    - name: Checkout code
      uses: actions/checkout@v4
      with:
         ref: ${{ github.head_ref }}
    - name: Set up Python
      uses: actions/setup-python@v4
      with:
        python-version: '3.10'

    - name: Install dependencies
      run: |
        pip install supabase
        [[ -d supabase_storage_backup ]] || mkdir supabase_storage_backup
        cd supabase_storage_backup
        wget https://raw.githubusercontent.com/mansueli/Supa-Migrate/main/storage-backup.py
        chmod +x storage-backup.py
        python storage-backup.py
        rm storage-backup.py
      shell: bash
      
    - name: Set current date as env variable
      run: echo "NOW=$(date +'%Y-%m-%dT%H:%M:%S')" >> $GITHUB_ENV
        
    - uses: stefanzweifel/git-auto-commit-action@v4
      with:
        commit_message: Supabase Storage backup - ${{ env.NOW }}
````
Contributing
If you find a bug or want to suggest a new feature, please open an issue or submit a pull request. We welcome contributions from the community and are happy to help you get started.

License
Supa-Backup is released under the [Apache 2.0 License](https://github.com/mansueli/Supa-Backup/blob/main/LICENSE).
