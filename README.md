# Mobb Fixer for Checkmarx One GitHub Integration

This action is used alongside the Sonarcloud's native Webhook integration with GitHub where SonarCloud publishes a scan report in the Pull Request's comment section. 

This action will monitor the presence of such a comment and trigger a job to download the SAST report. The SAST report is submitted to the Mobb vulnerability analysis engine, and a fix is presented to the Pull Request's comment section. 

If you are using this on a private repo, the Mobb user to which the API key belongs must have access to the repo and must approve GitHub access for the user on the Mobb platform beforehand.

## Inputs

## `sonarqube-token`

**Required** your SonarCloud API token. 


## `sonarqube-host-url`

**Required** The SonarCloud Host URL. Usually it's `https://sonarcloud.io`

## `sonarqube-project`

**Required** The SonarCloud Project

## `sonarqube-org`

**Required** The SonarCloud Org

## `mobb-api-token`

**Required** The Mobb API token to use with the action. [Find out how to get it here](https://docs.mobb.ai/mobb-user-docs/administration/access-tokens). 


## `github-token`

**Required** The GitHub api token to use with the action. Usually available as `${{ secrets.GITHUB_TOKEN }}`.



## Example usage

Create a file under the path `.github/workflow/mobb.yml`. 

A sample content of the workflow file: 
```
# Mobb/Checkamrx Fixer on pull requests
# This workflow defines the needed steps to run Checkmarx on every pull request and pass the results to Mobb Fixer.
#
# Secrets in use (add your missing ones):
# CX_API_TOKEN - Your Checkmarx credentials (find how to get it here: https://checkmarx.com/resource/documents/en/34965-68775-generating-a-refresh-token--api-key-.html)
# MOBB_API_TOKEN - Your mobb API Token (find out how to get it here: https://docs.mobb.ai/mobb-user-docs/administration/access-tokens)
# GITHUB_TOKEN - Automatically set by GitHub

name: "Mobb/Checkmarx"

on:
  issue_comment:
    types: [created]

jobs:
  report-and-fix:
    name: Get Report and Fix
    if: ${{ github.event.issue.pull_request && contains(github.event.comment.body,'Checkmarx One – Scan Summary & Details') }} # This makes sure that the comment originates from a PR and not an issue comment
    runs-on: 'ubuntu-latest'
    timeout-minutes: 360
    permissions:
      pull-requests: write
      statuses: write
      contents: read
    steps:
      - name: Run Mobb GH Fixer monitor for CxOne Comments
        if: always()
        uses: mobb-dev/cx-mobb-fixer-action@v1.5
        with:
          cx-api-token: ${{ secrets.CX_API_TOKEN  }}
          mobb-api-token: ${{ secrets.MOBB_API_TOKEN }}
          github-token: ${{ secrets.GITHUB_TOKEN }}
          cx-timeout: 20000
```
