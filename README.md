# Terraform Cloud CLI

A command line utility for interacting with the Terraform Cloud API. Uses [tfe-go][] under the hood.

[![Tests](https://github.com/cbsinteractive/tfc-cli/actions/workflows/tests.yml/badge.svg)](https://github.com/cbsinteractive/tfc-cli/actions/workflows/tests.yml)

## Terraform Cloud API Token

In order to make API requests to Terraform Cloud, the client needs an API token. A token may be created at various scopes, e.g. user, team and organization. Organization tokens are more limited in scope than user or team tokens, but are sufficient for most of the activities enabled by `tfc-cli`.

Go to the API Tokens page for the organization in which you will be setting up workspaces:

`https://app.terraform.io/app/<YOUR ORG NAME>/settings/authentication-tokens`

Do not **regenerate** a new token because this will invalidate the existing token and break any processes that are using it. Only do this if you're sure that's okay. Coordinate with your organization to obtain the token for use in your processes.

Save this API token in a secure manner, such as your password manager. If using the token in an automated process, ensure that it is stored in a secure manner, such as GitHub [encrypted secrets][].

## Supported Environment Variables

Most of the commands require `-token` and `-org` parameters to specify the API token and Terraform Cloud organization, respectively. As a convenience, the tool will fallback on the `TFC_TOKEN` and `TFC_ORG` environment variables. Due to the sensitive nature of the API token, you may wish to keep them out of your shell setup files. A better option would be [direnv][].

To keep the examples below brief, it is assumed that these environment variables are set.

## Installation and Usage

### Docker

This is a good way to run the command locally if you have Docker and/or don't wish to install Go.

```shell
docker run -e TFC_ORG -e TFC_TOKEN --pull always --rm frostedcarbon/tfc-cli:latest workspaces variables create -workspace foo -key bar -value baz -category terraform
```

### On Mac/Linux

1. Make sure you have Go installed using your method of choice. [goenv][] is one way.

2. Install the `tfc-cli` module locally:

   ```shell
   go install github.com/cbsinteractive/tfc-cli@latest
   ```

## Available Commands

Create a workspace:

```shell
tfc-cli workspaces create -workspace foo
```

Update a workspace description:

```shell
tfc-cli workspaces update -workspace foo -description "new description"
```

Set workspace VCS configuration:

```shell
tfc-cli workspaces update -workspace some-workspace -vcs-identifier some-org/some-repo -vcs-branch some-branch -vcs-oauth-token-id some-oauth-token-id
```

Delete a workspace:

```shell
tfc-cli workspaces delete -workspace foo
```

Create a workspace variable:

```shell
tfc-cli workspaces variables create -workspace foo -key bar -value baz -category terraform
```

Update a workspace variable value:

```shell
tfc-cli workspaces variables update value -workspace foo -key bar -value quux
```

Make a workspace variable sensitive:

```shell
tfc-cli workspaces variables update sensitive -workspace foo -key bar -sensitive=true
```

Note the `-sensitive=<bool>` syntax. This is required.

Delete a workspace variable:

```shell
tfc-cli workspaces variables delete -workspace foo -key bar
```

Get current state output variable value:

```shell
tfc-cli stateversions current getoutput -workspace foo -name bar
```

[direnv]: https://direnv.net/
[encrypted secrets]: https://docs.github.com/en/actions/security-guides/encrypted-secrets
[goenv]: https://github.com/syndbg/goenv
[tfe-go]: https://github.com/hashicorp/go-tfe
