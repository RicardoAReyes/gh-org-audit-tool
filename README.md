# GitHub Org Contributors & Dockerfile Reporter

![Python](https://img.shields.io/badge/Python-3.7%2B-blue)
![License](https://img.shields.io/badge/License-MIT-green)
![Status](https://img.shields.io/badge/status-active-brightgreen)

> Fetch contributor metadata, repo info, and Dockerfile base images from GitHub orgs.

---

## 📚 Table of Contents

- [🔍 What It Does](#-what-it-does)
- [⚙️ Configuration](#️-configuration)
- [💻 How to Run](#-how-to-run)
- [🧠 Notes on Behavior](#-notes-on-behavior)
- [📂 Output Files](#-output-files)
- [🧩 CSV Format Example](#-csv-format-example)
- [✅ Requirements](#-requirements)
- [🛠 Tips](#-tips)
- [📬 Feedback or Questions](#-feedback-or-questions)

---

## 🔍 What It Does

For each GitHub org and its public repositories, this script:

- Retrieves:
  - Repo status (archived/active)
  - Languages used
  - Last commit date
  - Contributor names & emails (commit authors)
- Searches for `Dockerfile` (recursively) and extracts the base image from the `FROM` line
- Writes results to:
  - A `.csv` file per org
  - A combined `.xlsx` workbook with one sheet per org
- Caches already-processed repos for resumability


## ✅ Requirements
* Python 3.7+
* Dependencies:
	* requests
	* openpyxl

Install with: 

```pip install requests openpyxl```

</br>
---

## ⚙️ Configuration

Edit the top of the script to adjust:

`GITHUB_TOKEN = os.getenv("GITHUB_TOKEN", "ghp_your_token_here")`

From Github.com enter the list of organization that you would like to analyze, only enter the org name without github.com on the array list. 

Example: 
`ORG_LIST = ["Chicago", "NASA", "ArlingtonCounty", "CDCGov", "CISAGov"]`

Github has a Global Government Community that they curate which including U.S. Federal agencies, U.S. City, U.S. County, and U.S. Local Law Enforcement, U.S. Military and Intelligence. 

[https://government.github.com/community/](https://government.github.com/community/)


---

🔐 GitHub Token
You must use a Personal Access Token (PAT) with public_repo access to avoid strict API rate limits. You can export it as an environment variable or set it directly in the script:

`export GITHUB_TOKEN=ghp_XXXXXXXXXXXXXXXXXXXXXXXXXXXX`

---
</br>
## 💻 How to Run Script

`python github_org_report.py`

The script will:

* Resume from where it left off using `processed_repos.json`
* Write or update:
	* `ORGNAME_contacts.csv` — per org
	* `all_orgs_contacts.xlsx` — with a sheet per org

</br>
## 🧠 Notes on Behavior 

* ✅ Smart Resuming
	* Uses processed_repos.json to skip previously completed repos

* ✅ Rate Limit Safe
	*  Handles GitHub API rate limits (5,000/hr general, 30/min for search)
	*  Throttles Dockerfile search with sleep(2.1)
* ✅ Safe Output Handling
	* .csv files won't be overwritten unnecessarily
	* Groups contributor data by repo in readable blocks
	* Skipped or rate-limited repos are logged
* 🛑 If a repo has no commits, it's skipped
* ⚠️ Failed or retried requests are logged in failed_requests.log

</br>
---

## 📂 Output Files
			

| File | Description |
| -------- | ------- |
| `ORG_contacts.csv`  | Repo data for ORG1 |
| `ORG2_contacts.csv` | Repo data for ORG2 |
| `processed_repos.json` | Cache of processed org/repo combinations |
| `failed_requests.log` | Logs permanently failed GitHub API calls |
| `all_orgs_contacts.xlsx`	 | **Consolidated Excel report** (one sheet per org)|
</br>
---

## 🧩 CSV Format Example
```Repo URL: https://github.com/CDCgov/some-repo,,,,,,,
Dockerfile: python:3.11-slim,,,,,,,
CDCgov,some-repo,active,Python,04-06-2025,Jane Doe,jane@example.com,04-06-2025
,,,,,,,John Smith,john@example.com,04-02-2025
```


</br>
## 🛠 Tips
* To clear the cache and start over:
	* ``rm processed_repos.json``
* To force-refresh an individual repo:
	* Remove it manually from `processed_repos.json` and rerun the script.
