# Container Image Build Workflows

This repo serves as a centralized build hub for the container images that Supermileage uses for compilation.

## Setup

The workflow requires two secrets, `CR_USER` and `CR_PAT` to authenticate against GitHub's container registry. Make sure those are set before adding. Refer to [this page](https://docs.github.com/en/packages/guides/migrating-to-github-container-registry-for-docker-images#authenticating-with-the-container-registry) for more details.

## Adding new images

To add a new image, simply add a new directory (named what you want the package to be) and add your contents within. For example, if we want a new package `hello-world`, add a new top-level directory `hello-world/` and add your Dockerfile and other required contents within.

Refer to [hello-world](hello-world/) to see how it works.

# Caveats

## Directories

Since the workflow scans the repo for non-hidden directories to build images in parallel, non-image directories should be avoided. If a directory needs to be added to the repo, prepend it with `.` to mark it as hidden.
