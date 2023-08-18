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

### Run the runner as service
Follow these [GitHub docs](https://docs.github.com/en/actions/hosting-your-own-runners/managing-self-hosted-runners/configuring-the-self-hosted-runner-application-as-a-service)

### Add Traefik to route requests to correct running application
Copy content of `github-runner` directory in this repository to `/home/github-runner` directory on your server
Run `docker compose up -d` to start Traefik

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
