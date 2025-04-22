# Xygeni Octokit Demo

[![SpritaIT](https://img.shields.io/badge/Developed%20by-SpritaIT%20Brasil-blue?link=https://spritait.com.br)](https://spritait.com.br)
[![License: ISC](https://img.shields.io/badge/License-ISC-blue.svg)](https://opensource.org/licenses/ISC)

## Description

This project, created by [SpritaIT Brasil](https://spritait.com.br), serves as a demonstration for using the **Xygeni.io** security platform. Specifically, it showcases the implementation of Xygeni's **GuardRail** feature within a GitHub Actions CI/CD pipeline to perform security analysis on software dependencies, using the `octokit` library as a primary example.

## Purpose

The main goal of this repository is to illustrate how the Xygeni Scanner, integrated into GitHub Actions, can function as an automated security gate. It demonstrates how pipeline execution can be controlled (allowed or blocked) based on predefined security policies concerning dependencies, such as:

-   Identifying known vulnerabilities (CVEs).
-   Detecting potentially suspicious or malicious packages.
-   Enforcing license compliance (if configured).

## How it Works

The core of this demonstration lies in the GitHub Actions workflow defined in `.github/workflows/build-safe.yml` (or a similar name within the `.github/workflows` directory).

1.  **Trigger:** The workflow is typically triggered by Pull Requests targeting the `main` branch or can be run manually via the Actions tab (`workflow_dispatch`).
2.  **Checkout:** The workflow checks out the repository code.
3.  **Xygeni Scanner:** It downloads and executes the Xygeni Scanner, using credentials stored in GitHub Secrets.
4.  **Scan Execution:** The scanner analyzes the project, focusing on `inventory`, `misconf` (misconfigurations), `deps` (dependency vulnerabilities - SCA), and `suspectdeps` (suspicious dependencies).
5.  **GuardRail Enforcement:** The critical part is the `--fail-on` parameter within the `xygeni scan` command. This parameter points to a policy file (e.g., `.github/GuardRails/deps-critical.xyflow`). This `.xyflow` file defines the specific conditions (like finding critical vulnerabilities) that will cause the scanner step to fail.
6.  **Pipeline Outcome:** If the scan detects issues violating the policies defined in the `.xyflow` file, the step fails, causing the workflow run to fail. This failure prevents subsequent steps (like a build or deployment, although the build step is commented out in the provided example) and signals a security issue in checks associated with a Pull Request. If no policy violations are found, the workflow succeeds.

## Technology Stack

-   GitHub Actions (for CI/CD automation)
-   [Xygeni Scanner](https://xygeni.io) (for security analysis)
-   Node.js / npm (as the project environment)
-   [Octokit](https://github.com/octokit/octokit.js) (as the primary dependency example)

## Configuration

-   **Secrets:** Xygeni API credentials (`XY_USERNAME_DEMO`, `XY_PASSWORD_DEMO`, `XY_URL_DEMO`) and a GitHub Personal Access Token (`GH_PAT`) must be configured as GitHub Actions Secrets in the repository settings.
-   **GuardRail Policy:** The specific rules that determine if the pipeline passes or fails the security check are defined in the `.github/GuardRails/deps-critical.xyflow` file within this repository. This file needs to be configured according to Xygeni's policy language.

## Observing the Demo

-   **Pull Requests:** Open a Pull Request targeting the `main` branch to see the workflow run automatically as a status check.
-   **Manual Runs:** Navigate to the "Actions" tab of this repository, select the "Build (safe)" workflow, and click "Run workflow" (providing dummy inputs if required by the manual trigger setup, like `octokit` and `latest`).
-   **Results:** Examine the workflow run logs in the "Actions" tab to see the output of the Xygeni scan and whether the job succeeded or failed based on the GuardRail policy. Error annotations (`::error`) may appear if the GuardRail conditions are met.

## License

This project is licensed under the **ISC License**.
