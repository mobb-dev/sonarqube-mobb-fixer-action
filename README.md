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
coming soon
```
