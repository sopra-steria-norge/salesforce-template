# Setup

## On GitHub.com:

1. Click `Use this template`
    - Check `Include all branches`
1. Go to Settings → Secrets and add:
    - `SFDX_URL_DEVHUB`
        - Production SFDX url
        - Login to production in SFDX
        - Use `sfdx force:org:display -u [prod_org] --verbose` to get SFDX auth URL
    - `PACKAGE_KEY`
        - Password to protect installation of packages outside of org
    - `SLACK_WEBHOOK_URL`
        - Slack app to post updates to a Slack channel
    - `DEPLOYMENT_PAT`
        - [Documentation](https://docs.github.com/en/github/authenticating-to-github/creating-a-personal-access-token), give repo access
        - Needed to be able to force merge package incrementation (because the master branch is protected) 
1. Go to Settings → Environments and add:
    - `Production`
        - Set `Wait timer` to 15 minutes
    - `UAT`
    - `Dev`
    - `SIT`
    - For all Environments, do the following:
        - Set `Deployment branches` to `Protected branches`
        - Add `SFDX_URL_ENV` (SFDX url for each org to deploy packages)
1. Go to Settings → Branches → New
    - **Branch name:** master
    - Check the following:
        - ☑️ Require pull request reviews before merging
        - ☑️ Dismiss stale pull request approvals when new commits are pushed
        - ☑️ Require status checks to pass before merging
        - ☑️ Require branches to be up to date before merging
        - ☑️ Require conversation resolution before merging
    - Add the following required status checks (might need to create a dummy PR for these to appear):
        - Run Apex Tests
        - Compile Metadata
        - Validate 85% Code Coverage
1. Do the same as the previous step for `uat` and `dev`
    - Except that `uat` and `dev` should **not** have the following
        - 🔘 Require branches to be up to date before merging
    - Except that `dev` should **not** have the following
        - 🔘 Require pull request reviews before merging
        - 🔘 Dismiss stale pull request approvals when new commits are pushed
1. Create an init release in GitHub (not pre-release)
    - Important! Release creation will fail if an init release has not been made!

## In VS Code / terminal

1. Create file `.sfdx/sfdx-config.json` in the repo, locally on your machine
    - Add `{"defaultdevhubusername": "[your_devhub_user]","defaultusername": "" }` to it and change the DevHub username
1. Create a package in SFDX
    - `sfdx force:package:create -t Unlocked -r force-app -n [PACKAGE_NAME]`
1. Create an test metadata file in `force-app` folder to initiate init package creation (can be just a CustomLabel file)
1. Push changes made to `force-app` and `sfdx-project.json`

You should now be done configuring your new repo.