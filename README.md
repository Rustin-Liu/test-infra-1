# GitHub Workflow & Testing Infrastructure

## DBG

DBG stands for Drivers Build Grid.

It's a tool that we created to prebuilt a set of Falco drivers (both kernel module and eBPF probe) for various target distro and kernel releases, by using [driverkit](https://github.com/falcosecurity/driverkit).

You can find more about it [here](/driverkit).

## Prow

[Prow](https://github.com/kubernetes/test-infra/tree/master/prow) is a CI/CD system running on Kubernetes.

This directory contains the resources composing the Falco's workflow & testing infrastructure.

Are you looking for Deck to check the merge queue and prow jobs?

- http://147.75.81.50:30754/

### Components

Listing the deployments we are currently using.

#### Deck

> UI for prow jobs.

#### Sinker

> Clean up stale prow jobs.

#### Hook

> Handle GitHub events dispatching them to plugins.

#### Horologium

> Start periodic jobs.

#### Plank

> Start prow jobs.

### Setup

This assumes that you are in the `prow` directory and that you can reach your working Kubernetes cluster.

1. Create a bot account (we have our own [poiana](https://github.com/poiana)).

    1.1. Grant it **owner** level access to the GitHub organisations on which prow will operate on.

    1.2. Generate a [personal access token](https://github.com/settings/tokens) for the bot with full `repo` scope and `admin:org`, `admin:repo_hook`, and `admin:org_hook` too (in case you want prow to operate at organisation level).

    1.3 Save such OAuth token to `prow/oauth_secret` file.

2. Create a token for GitHub webhooks.

    ```bash
    openssl rand -hex 20 > prow/hmac_secret
    ```

3. Deploy prow

    ```bash
    make prow
    ```

4. Setup the hook

    4.1. Install the `update-hook` tool

    ```bash
	go get -u k8s.io/test-infra/experiment/update-hook
    ```

    4.2. Attach it to the organisation using `--repo` flag (or to a precise repo using `MY_ORG/MY_REPO` convention)

    ```bash
	update-hook --hmac-path=path/to/hmac/secret --github-token-path=path/to/oauth/secret --hook-url http://an.ip.addr.ess/hook --repo MY_ORG --confirm=true
    ```

5. Setup or update your plugins and configs with

   ```bash
   make plugins
   make configs
   ```

---
