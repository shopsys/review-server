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

### Add more runners when you also run tests (optional)
One runner processes one job at a time, there is no setting to make it run several jobs in parallel.
Jobs are assigned to runners by labels and a runner is only eligible when it has *all* labels listed in `runs-on`.

To prevent tests from delaying deployments of review applications, install separate runners for tests
and give them a label that the deployment job does not use.
Install as many of them as you want tests to run in parallel.

```bash
    TOKEN=<registration token from GitHub>
    RUNNER_VERSION=<latest version from https://github.com/actions/runner/releases>
    curl -sL -o /tmp/runner.tar.gz https://github.com/actions/runner/releases/download/v${RUNNER_VERSION}/actions-runner-linux-x64-${RUNNER_VERSION}.tar.gz

    for i in 1 2 3; do
        mkdir -p ~/actions-runner-test-$i && cd ~/actions-runner-test-$i
        tar xzf /tmp/runner.tar.gz
        ./config.sh --url https://github.com/<organization>/<repository> --token "$TOKEN" \
                    --name <server>-tests-$i --labels tests \
                    --work _work --unattended
    done
```

Install every runner as a service the same way as the first one and target them from the workflow
by `runs-on: [self-hosted, linux, tests]`.

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
