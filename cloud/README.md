# Cloud infrastructure

## EC2 instance

| Setting | Value |
|---|---|
| AMI | Amazon Linux 2023 (64-bit) |
| Type | t3.micro (free tier) |
| Region | eu-north-1 (Stockholm) |
| Access | SSH via PuTTY, ED25519 key |

Instance was decommissioned after project delivery. Public DNS and IP intentionally not published.

## Python environment

```bash
sudo yum install -y python3.12 python3.12-venv
python3.12 -m venv /home/ec2-user/pipeline/venv
source /home/ec2-user/pipeline/venv/bin/activate
pip install gspread google-auth pandas
```

## Cron job

```
0 7 * * * cd /home/ec2-user/pipeline && source venv/bin/activate && python3 sync.py >> /var/log/pipeline/sync.log 2>&1
```

Daily at 07:00 Europe/Skopje. stdout and stderr redirected to a rotating log.

## Log rotation

`/etc/logrotate.d/pipeline`:

```
/var/log/pipeline/*.log {
    daily
    rotate 14
    compress
    missingok
    notifempty
    create 0640 ec2-user ec2-user
}
```

## Git integration on the instance

- ED25519 SSH key generated on the instance, public half added as a deploy key on the repo
- `git pull` in the cron script guarantees the instance runs the latest version of `sync.py` before each run

## Secrets

- GCP service account JSON lived at `/home/ec2-user/pipeline/credentials.json`, permissions `600`
- Never committed to the repo
- Rotate via GCP Console -> IAM -> Service Accounts if the instance is ever redeployed
