# Shopsys Review Server

This is manual and config files for running review server of [Shopsys Platform](https://github.com/shopsys/shopsys) using GitHub Runner on own VPS via GitHub Actions.
GitHub has detailed documentation about [self-hosted runners](https://docs.github.com/en/actions/hosting-your-own-runners/about-self-hosted-runners).

## Installation

### Server requirements

For running GitHub runner you need to meet [these requirements](https://docs.github.com/en/actions/hosting-your-own-runners/managing-self-hosted-runners/about-self-hosted-runners#requirements-for-self-hosted-runner-machines). Install Docker and Docker Compose.

### Create user for runner

On your server create user e.g. `github-runner` with home directory `/home/github-runner` and [add him](https://docs.docker.com/engine/install/linux-postinstall/#manage-docker-as-a-non-root-user) to `docker` group.

### Add self-hosted runner in `github-runner` user home directory

Follow these [GitHub docs](https://docs.github.com/en/actions/hosting-your-own-runners/managing-self-hosted-runners/adding-self-hosted-runners)

### Run the runner as service

Follow these [GitHub docs](https://docs.github.com/en/actions/hosting-your-own-runners/managing-self-hosted-runners/configuring-the-self-hosted-runner-application-as-a-service)

### Add Traefik to route requests to correct running application

Copy content of `github-runner` directory in this repository to `/home/github-runner` directory on your server.
Run `docker compose up -d` to start Traefik.

### Configure Docker daemon

`daemon.json` increases the Docker address pool and limits container logs to 3 files of 50 MB each.
The limit applies only to containers created after the Docker daemon restart.

```bash
    sudo mkdir -p /etc/docker/
    sudo mv /home/github-runner/daemon.json /etc/docker/daemon.json
    sudo service docker restart
```

### System configuration

Files in the `system` directory belong outside the `github-runner` home directory:

| File                               | Destination                                                                             |
|------------------------------------|-----------------------------------------------------------------------------------------|
| `system/sysctl/99-sysctl.conf`     | `/etc/sysctl.d/99-sysctl.conf` (required by Elasticsearch and by many containers)       |
| `system/prometheus/prometheus.yml` | `/etc/prometheus/prometheus.yml` (packages `prometheus` and `prometheus-node-exporter`) |
| `system/fail2ban/sshd.local`       | `/etc/fail2ban/jail.d/sshd.local`                                                       |
| `system/iptables/rules.v4`         | `/etc/iptables/rules.v4` (package `iptables-persistent`)                                |
| `system/iptables/rules.v6`         | `/etc/iptables/rules.v6` (package `iptables-persistent`)                                |

In `sshd.local`, replace `<vpn-ip>` with the VPN IP address that must never be banned.

In `rules.v4`, replace `<monitoring-ip>` with the only IP address allowed to read Prometheus and node exporter.
Both files are loaded on boot before Docker creates its own chains - never load them with `iptables-restore` or `ip6tables-restore` on a running server, it would flush the Docker chains and cut off all containers.
Add a rule to the running server with `iptables -A` instead.

### Runners

The server runs six runners for the `shopsys/shopsys` repository, each in its own directory in `/home/github-runner` and installed as a service via `svc.sh`:

| Directory                 | Runner name         | Labels                                        | Purpose                                                                                                             |
|---------------------------|---------------------|-----------------------------------------------|---------------------------------------------------------------------------------------------------------------------|
| `actions-runner`          | `review-stage`      | `self-hosted`, `Linux`, `X64`, `review-stage` | Deploys review environments (`docker-build` workflow)                                                               |
| `actions-runner-review-2` | `review-stage-2`    | `self-hosted`, `Linux`, `X64`, `review-stage` | Deploys review environments (`docker-build` workflow)                                                               |
| `actions-runner-review-3` | `review-stage-3`    | `self-hosted`, `Linux`, `X64`, `review-stage` | Deploys review environments (`docker-build` workflow)                                                               |
| `actions-runner-test-1`   | `odin-playwright-1` | `self-hosted`, `Linux`, `X64`, `playwright`   | Runs the AI acceptance criteria check with Playwright against a deployed review (`ai-acceptance-criteria` workflow) |
| `actions-runner-test-2`   | `odin-playwright-2` | `self-hosted`, `Linux`, `X64`, `playwright`   | Runs the AI acceptance criteria check with Playwright against a deployed review (`ai-acceptance-criteria` workflow) |
| `actions-runner-test-3`   | `odin-playwright-3` | `self-hosted`, `Linux`, `X64`, `playwright`   | Runs the AI acceptance criteria check with Playwright against a deployed review (`ai-acceptance-criteria` workflow) |

### Run GitHub Actions

Your GitHub runner is now ready and accepts connections from GitHub.
Config files for GitHub Actions can be found in [Shopsys Platform repository](https://www.github.com/shopsys/shopsys) in `.github` directory.

## Contributing

Thank you for your contributions to Shopsys Review Server repository.
Together we are making Shopsys Platform better.

Please, check our [Contribution Guide](https://github.com/shopsys/shopsys/blob/master/CONTRIBUTING.md) before contributing.

## Support

What to do when you are in troubles or need some help?
The best way is to ask in our [GitHub Discussions](https://github.com/shopsys/shopsys/discussions).
