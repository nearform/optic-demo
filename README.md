# Optic Demo

This is a demo of how to automatically publish Npm packages to the Npm registry using GitHub Actions and Optic.

# What is Optic?
Optic is an ecosystem of a mobile application, GitHub action, and backend application. The mobile app stores Npm secrets, generates OTPs, and sends push notifications. The GitHub action triggers the deployment workflow. Backend server stores app subscriptions and generates Optic tokens.

# How Optic works
In a nutshell, Optic works by automating the build and versioning process, as well as publishing Npm packages. It does all that without compromising the security. Publishing an Npm package requires you to authenticate using a publish token issued by Npm. These tokens are used when publishing packages using the Npm CLI. An OTP is required for the publishing process if you also have (2FA) enabled.

# Steps to configure the workflow
Install the optic-release-automation GitHub app to your organization (or selected repositories)
Create a new workflow file at .github/workflows/release.yml (from example below) with one step that uses this action and supply the inputs.

# What does it do?
- When run, it opens a new PR for the release.
- When/if the PR gets merged, it publishes a new Npm release and a new GitHub release with change logs

# Secrets
The workflow requires you to define `NPM_TOKEN` and `OPTIC_TOKEN` secrets.
- NPM_TOKEN - This is your Npm Publish token. Read how to create acccess tokens. Required only if you want to release to Npm.
- OPTIC_TOKEN - This is your Optic token. You can add your Npm secret to the Optic app, generate a token and pass it to this input.

This example shows how to configure this action to release a new Npm package version:

```yml
name: release

on:
  workflow_dispatch:
    inputs:
      semver:
        description: "The semver to use"
        required: true
        default: "patch"
        type: choice
        options:
          - patch
          - minor
          - major
      tag:
        description: "The Npm tag"
        required: false
        default: "latest"
      commit-message:
        description: "The commit message template"
        required: false
        default: "Release {version}"
  pull_request:
    types: [closed]

jobs:
  release:
    runs-on: ubuntu-latest
    steps:
      - uses: nearform/optic-release-automation-action@v2
        with:
          github-token: ${{ secrets.github_token }}
          Npm-token: ${{ secrets.NPM_TOKEN }}
          optic-token: ${{ secrets.OPTIC_TOKEN }}
          semver: ${{ github.event.inputs.semver }}
```
