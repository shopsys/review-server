# Shopsys Review Server

This is manual and config files for running review server of [Shopsys Platform](https://github.com/shopsys/shopsys) using GitHub Runner on own VPS via GitHub Actions.
GitHub has detailed documentation about [self-hosted runners](https://docs.github.com/en/actions/hosting-your-own-runners/about-self-hosted-runners).

## Installation

### Server requirements
For running GitHub runner you need to meet [these requirements](https://docs.github.com/en/actions/hosting-your-own-runners/managing-self-hosted-runners/about-self-hosted-runners#requirements-for-self-hosted-runner-machines).
Install Docker and Docker Compose.

### Create user for runner
On your server create user e.g. `github-runner` with home directory `/home/github-runner` and [add him](https://docs.docker.com/engine/install/linux-postinstall/#manage-docker-as-a-non-root-user) to `docker` group.

### Add self-hosted runner in `github-runner` user home directory
Follow these [GitHub docs](https://docs.github.com/en/actions/hosting-your-own-runners/managing-self-hosted-runners/adding-self-hosted-runners)

The review workflow picks the runner by the `review-stage` label, so register the runner with `--labels review-stage`.

### Run the runner as service
Follow these [GitHub docs](https://docs.github.com/en/actions/hosting-your-own-runners/managing-self-hosted-runners/configuring-the-self-hosted-runner-application-as-a-service)

### Create directory for review environments
Review environments are deployed to `/home/github-runner/reviews/<branch-name>` regardless of which runner runs the job,
so the directory must be writable by the `github-runner` user:
```bash
    sudo -u github-runner mkdir -p /home/github-runner/reviews
```
Cancelling a review finds its containers by the Docker Compose project name (the escaped branch name),
so it does not depend on the runner that deployed it either.

### Add more runners to deploy reviews in parallel
Every runner needs its own directory, e.g. `/home/github-runner/actions-runner-review-2`, its own name, e.g. `review-stage-2`,
and the `review-stage` label. Repeat the steps above for each of them:
```bash
    cd /home/github-runner/actions-runner-review-2
    ./config.sh --url https://github.com/shopsys/shopsys --token <registration-token> --name review-stage-2 --labels review-stage
    cp /home/github-runner/actions-runner/.env /home/github-runner/actions-runner/.path .
    sudo ./svc.sh install github-runner
    sudo ./svc.sh start
```
Copying `.env` and `.path` from the first runner keeps the same `PATH` (e.g. `yq` in `/usr/local/bin`) for all runners.

### Add Traefik to route requests to correct running application
Copy content of `github-runner` directory in this repository to `/home/github-runner` directory on your server
Run `docker compose up -d` to start Traefik

### Increase Docker address pool
```bash
    sudo mkdir -p /etc/docker/
    sudo mv /home/github-runner/daemon.json /etc/docker/daemon.json
    sudo service docker restart
```

### Run GitHub actions
Your GitHub runner is now ready and accepts connections from GitHub.
Config files for GitHub actions can be found in [Shopsys Platform repository](https://www.github.com/shopsys/shopsys) in `.github` directory.

## Contributing
Thank you for your contributions to Shopsys Review Server repository.
Together we are making Shopsys Platform better.

Please, check our [Contribution Guide](https://github.com/shopsys/shopsys/blob/master/CONTRIBUTING.md) before contributing.

## Support
What to do when you are in troubles or need some help?
The best way is to join our [Slack](https://join.slack.com/t/shopsysframework/shared_invite/zt-11wx9au4g-e5pXei73UJydHRQ7nVApAQ).
