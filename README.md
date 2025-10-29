# 📬 Daily Progress Auto-Sync using AWS Lambda & Gmail API

![AWS Lambda](https://img.shields.io/badge/AWS%20Lambda-Automation-orange?logo=awslambda)
![Python](https://img.shields.io/badge/Python-3.13-blue?logo=python)
![GitHub Actions](https://img.shields.io/badge/GitHub-AutoCommit-black?logo=github)
![EventBridge](https://img.shields.io/badge/EventBridge-Scheduled%20Trigger-red?logo=amazonaws)
![Status](https://img.shields.io/badge/Status-Live%20and%20Working-success)

---

## 🧠 Project Overview

This project automates the collection of **daily progress reports** from Gmail and commits them automatically to a GitHub repository using **AWS Lambda**.  
It integrates **Gmail API + GitHub API + AWS Lambda + EventBridge**, requiring **zero manual intervention** once deployed.

---

## ⚙️ Core Workflow

1. **Lambda Trigger (EventBridge):**  
   - Runs automatically every night at **11:30 PM IST**.
   - Invokes the Lambda handler `lambda_function.main`.

2. **Gmail Fetching:**  
   - Reads the most recent reply to the subject **“Daily Progress Reminder 📬”**.
   - Extracts only the *clean text* portion (skipping quoted replies).

3. **Progress Storage:**  
   - Saves the cleaned text to a timestamped file:  
     `/tmp/progress_reports/YYYY-MM-DD.txt`

4. **GitHub Sync:**  
   - Commits and pushes this file automatically to the configured GitHub repo.
   - Uses environment variables stored in AWS Lambda:
     - `GITHUB_TOKEN`
     - `GITHUB_REPO`
     - `GITHUB_BRANCH`

5. **Error Handling & Logging:**  
   - Structured error messages are returned via Lambda test output.
   - Handles missing or expired Gmail tokens gracefully.

---

## 🔑 Authentication & Security

- **Gmail API** credentials are stored in `credentials.json` and a renewable `token.json`.  
- The `token.json` is refreshed automatically if expired.  
- **GitHub Token** is stored securely in Lambda environment variables.  
- Lambda uses the **/tmp directory** for temporary write operations due to its read-only environment.

---

## 🚀 Deployment Summary

**Build Environment:**
```bash
docker run --rm -v "${PWD}:/var/task" -w /var/task amazonlinux:2023 /bin/bash -c "
dnf install -y python3.13 python3.13-pip zip && \
pip3.13 install --no-cache-dir -r /var/task/requirements.txt -t /var/task/package && \
cd /var/task/package && zip -r9 /var/task/deployment_package.zip . && \
cd /var/task && zip -g deployment_package.zip lambda_function.py credentials.json token.json"
