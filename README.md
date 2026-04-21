# Johnson Matthey Lab Analytics and Cloud Automation

End-to-end data pipeline for Johnson Matthey's lab management system. Exploratory analysis over 310,215 samples, a daily EC2-to-Google-Sheets sync powered by a Python worker, and a PowerBI layer for claims tracking. Four-person delivery. I owned the cloud infrastructure.

## Stack

| Layer | Tools |
|---|---|
| Analysis | Python 3.12, pandas, numpy, matplotlib, seaborn |
| Cloud | AWS EC2 (Amazon Linux 2023, eu-north-1), cron |
| API | Google Sheets + Drive API via `gspread`, `google-auth` |
| BI | PowerBI Desktop |

## Dataset

- 310,215 lab samples
- 269 departments
- 4,396 projects
- 2003 through 2014 time span
- Source: WebALMS lab management system, sanitised at ingest

## Pipeline

1. **EDA notebook** runs over the sanitised WebALMS dataset. Produces turnaround-time trends, department-level performance breakdowns, technique usage patterns, and project throughput metrics. Pre-rendered to HTML so reviewers can open without Jupyter.
2. **Baseline upload** pushes historical samples to a shared Google Sheet via a GCP service account.
3. **Daily sync** on the EC2 worker generates realistic synthetic entries every morning and appends them to the same sheet, simulating live lab operations for stakeholder demos.
4. **BI layer** in PowerBI consumes the sheet plus a claims table for operational dashboards.

Cron entry:

```
0 7 * * * cd /home/ec2-user/pipeline && source venv/bin/activate && python3 sync.py
```

Runs daily at 07:00 Europe/Skopje. Logs rotate under `/var/log/pipeline/`.

## My role

Cloud infrastructure lead. Scope:

- EC2 provisioning, hardening, Python virtualenv setup
- Cron scheduling, log rotation, service restart on reboot
- Git integration on the instance via ED25519 SSH key
- Coordination between the analysis notebook and the API sync script

## Viewing the work

| File | Open with |
|---|---|
| `notebooks/final_notebook.html` | any browser |
| `notebooks/final_notebook.ipynb` | Jupyter plus `requirements.txt` |
| `powerbi/Johnson_Matthey_PowerBI_Project.pbix` | PowerBI Desktop |
| `screenshots/` | any image viewer |

## Repository structure

```
notebooks/
  final_notebook.ipynb      # source
  final_notebook.html       # pre-rendered report
  requirements.txt          # pip deps for local reproduction
powerbi/
  Johnson_Matthey_PowerBI_Project.pbix
cloud/
  README.md                 # EC2 + cron setup notes
screenshots/
```

## Notes

- Source dataset sanitised at ingest. No PII in the notebook or sheets.
- GCP service account keys and SSH private keys are excluded from this repository. Rotate any credentials before any redeploy.
- The EC2 instance was decommissioned after delivery. Cron configuration is documented in `cloud/README.md` for reproducibility.
