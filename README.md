# Postman Test Runner
This repository contains a workflow to automatically run Postman API tests against the dydxprotocol/v4-chain project whenever there are new commits on the main branch. It also supports manual test runs via GitHub Actions.

## Overview
Scheduled Checks: Every 15 minutes, a GitHub Action checks if there are new commits in dydxprotocol/v4-chain:main. If there is a new commit, the workflow automatically installs Newman and runs the Postman collection defined in this repository.
Manual Runs: You can also manually trigger the workflow in the “Actions” tab, optionally overriding the commit check to force a test run even if no new commits exist in the other repo.
Artifacts: The test results are uploaded as a build artifact in JUnit XML format (and optionally in other formats if you wish), so you can view the results directly in the GitHub Actions console.

## Getting Started
### 1. Repository Structure.
```
├─ .github/
│  └─ workflows/
│     └─ postman-tests.yml        # The GitHub Actions workflow
├─ postman/
│  ├─ collection.json             # Your Postman Collection file
│  └─ environment.json            # Your Postman Environment file
├─ .last_sha                      # Stores the last tested commit SHA (auto-managed by workflow)
└─ README.md                      
```

- postman/collection.json – Defines the API endpoints and tests for Newman.
- postman/environment.json – Provides environment variables and configuration for the tests.
- .last_sha – Automatically updated by GitHub Actions to store the last known commit from v4-chain.


### 2. Scheduled Workflow
The workflow file (.github/workflows/postman-tests.yml) uses GitHub Actions’ schedule event to run every 15 minutes. It checks for changes in v4-chain by hitting the GitHub API, comparing the latest commit to whatever is stored in .last_sha.

If a new commit is found:

Installs Newman (npm install -g newman)
Runs the collection (collection.json) with the specified environment (environment.json)
Uploads the results artifact (results.xml)
If there’s no new commit, the run is skipped (no tests run).

### 3. Manual Trigger
You can also manually trigger the workflow in the Actions tab by selecting “Scheduled or Manual Postman Tests” (or whatever name you gave the workflow) and clicking “Run workflow.” You’ll see an optional input called “override-commit-check.”

If set to "true", the tests will run regardless of whether a new commit exists.
If set to "false", it will behave like the scheduled run and skip tests if there’s no new commit.

### 4. Running Tests Locally
If you want to run the tests locally (outside of GitHub Actions), make sure you have Node.js and Newman installed:

install newman globally

```
npm install -g newman
```

Run your collection with environment

```
newman run postman/collection.json \
  -e postman/environment.json \
  --reporters cli,junit \
  --reporter-junit-export results.xml
You’ll see test output in your terminal, and a results.xml file for JUnit-format logs.