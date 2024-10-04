# Mobb Fixer for SonarCloud's Webhook based GitHub PR Integration

This action is used alongside the Sonarcloud's native Webhook integration with GitHub where SonarCloud publishes a scan report in the Pull Request's comment section. 

This action will monitor the presence of such a comment and trigger a job to download the SAST report. The SAST report is submitted to the Mobb vulnerability analysis engine, and a fix is presented to the Pull Request's comment section. 

If you are using this on a private repo, the Mobb user to which the API key belongs must have access to the repo and must approve GitHub access for the user on the Mobb platform beforehand.

## Inputs

## `sonarqube-token`

**Required** Your SonarCloud API token. 


## `sonarqube-host-url`

**Required** The SonarCloud Host URL. Usually it's `https://sonarcloud.io`

## `sonarqube-project`

**Required** The SonarCloud Project (Project Key)

## `sonarqube-org`

**Required** The SonarCloud Org (Organization Key)

## `mobb-api-token`

**Required** The Mobb API token to use with the action. [Find out how to get it here](https://docs.mobb.ai/mobb-user-docs/administration/access-tokens). 


## `github-token`

**Required** The GitHub api token to use with the action. Usually available as `${{ secrets.GITHUB_TOKEN }}`.



## Example usage

Create a file under the path `.github/workflow/mobb.yml`. 

A sample content of the workflow file: 
```yaml
# Mobb/SonarQube Fixer on pull requests.
#
# This workflow defines the needed steps to run SonarQube on every pull request
# and pass the results to Mobb Fixer.
#
# Secrets in use (add your missing ones):
#
# SONARQUBE_HOST_URL - your SonarQube server host. https://sonarcloud.io for cloud version.
# SONARQUBE_PROJECT - your SonarQube project name.
# SONARQUBE_ORG - your SonarQube organization name.
# SONARQUBE_TOKEN - your SonarQube access token.
# MOBB_API_TOKEN - your Mobb user API token.
# GITHUB_TOKEN - automatically set by GitHub.
#
name: "Mobb/Sonar/PRWebhook"
on:
  issue_comment:
    types: [created]
jobs:
  report-and-fix:
    name: Get Sonar Report and Fix
    if: ${{ github.event.issue.pull_request && contains(github.event.comment.body,'[![Quality Gate Failed]') }} # This makes sure that the comment originates from a PR and not an issue comment
    runs-on: 'ubuntu-latest'
    timeout-minutes: 360
    permissions:
      pull-requests: write
      statuses: write
      contents: read
      actions: read
    env:
      BRANCH: ${{ github.head_ref || github.ref_name }}
    steps:
      # - name: Checkout repo to get code
      #   uses: actions/checkout@v3
      - name: Run Mobb on the findings and get fixes
        if: always()
        uses: mobb-dev/sonarqube-mobb-fixer-action@main
        with:
          mobb-api-token: ${{ secrets.MOBB_API_TOKEN }}
          sonarqube-host-url: ${{ vars.SONARQUBE_HOST_URL }}
          sonarqube-project: ${{ vars.SONARQUBE_PROJECT }}
          sonarqube-token: ${{ secrets.SONARQUBE_TOKEN }}
          sonarqube-org: ${{ vars.SONARQUBE_ORG }}
          github-token: ${{ secrets.GITHUB_TOKEN }}
```
