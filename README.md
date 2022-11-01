# Github Actions CI/CD workflows

This repo contains our CI/CD Github Actions workflows defined in folder [workflows/](workflows/).
It also syncs the workflows via pull requests to all repositories defined in [.github/syncs.yml][2] (using action [GitHub File Sync][1]).

## Usage

Remember that master branch is protected. So you need to either use *dev* branch or create a new branch for PR's. Don't forget to delete any new branches after you merge them. If you use *dev* it will be automatically reset every time something is merged to master.

### Create workflows

Create or modify a yaml file in [workflows/](workflows/). Also update README.md with details about your workflow and remember that this is a public repository.

Push your changes to dev or a separate branch. Once you are done with all your changes. Merge into master.

## Workflows

Descriptions of our current workflows.
