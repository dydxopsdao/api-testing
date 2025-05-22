# dYdX API Testing

This repository contains Postman collections used to verify the public APIs for the `dydxprotocol/v4-chain` project. The tests are executed automatically with GitHub Actions and can also be run locally with [Newman](https://github.com/postmanlabs/newman).

## Requirements

* [Node.js](https://nodejs.org/) and `npm`
* [Postman](https://www.postman.com/) – used to create and export the collection
* [`newman`](https://github.com/postmanlabs/newman) CLI (`npm install -g newman`)
* `newman-reporter-htmlextra` (installed via `npm install` in this repo)

## Repository structure

```
├─ .github/workflows/             # GitHub Actions workflows
├─ postman/
│  ├─ collection.json             # Exported Postman collection
│  ├─ environment.mainnet.json    # Environment for mainnet
│  └─ environment.testnet.json    # Environment for testnet
├─ package.json                   # Dev dependency for HTML reports
└─ README.md
```

The Postman collection is maintained in Postman and exported into the `postman/` directory. Two example environments are provided – `environment.mainnet.json` and `environment.testnet.json` – which can be selected when running tests.

## Installing dependencies

Run `npm install` to install the reporter dependency used by the GitHub workflows and local runs. Newman itself should be installed globally:

```bash
npm install -g newman
```

## Running tests locally

Execute the collection with the environment of your choice. Below is an example using the testnet environment and generating both CLI and JUnit/HTML reports:

```bash
newman run postman/collection.json \
  -e postman/environment.testnet.json \
  --reporters cli,junit,htmlextra \
  --reporter-junit-export results.xml \
  --reporter-htmlextra-export newman-report.html
```

The results are saved to `results.xml` and `newman-report.html` in the project root.

## Automated runs via GitHub Actions

A workflow located in `.github/workflows/` checks for updates in the upstream repository and triggers Newman tests. Results are uploaded as artifacts for each run. You can also trigger the workflow manually from the **Actions** tab.

