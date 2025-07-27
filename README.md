# Træfik

<!-- markdownlint-disable line-length -->
[![minimal-readme compliant](https://img.shields.io/badge/readme%20style-minimal-brightgreen.svg)](https://github.com/RichardLitt/standard-readme/blob/master/example-readmes/minimal-readme.md) [![Project Status: Active – The project has reached a stable, usable state and is being actively developed.](https://www.repostatus.org/badges/latest/active.svg)](https://www.repostatus.org/#active) <a href="https://liberapay.com/benz0li/donate"><img src="https://liberapay.com/assets/widgets/donate.svg" alt="Donate using Liberapay" height="20"></a>
<!-- markdownlint-enable line-length -->

[This project](https://gitlab.com/b-data/docker/deployments/traefik) serves as
a template to run [Træfik](https://hub.docker.com/_/traefik) v3.5 in a docker
container using docker compose.

The goal is to set up a TLS termination proxy for all Docker containers
providing web services on a **single host**.

Features:

* Automatic creation/renewal of Let's Encrypt certificates (HTTP challenge).
* Provision of the following basic Middlewares:
  * RedirectScheme: Redirect from HTTP to HTTPS
  * RateLimit: Limit to a _fair_ amount of requests
    * average: 100 requests per second
    * burst: 50 requests (per second)
  * Headers: HTTP Strict Transport Security with long duration
* Provision of three configurations (modern, intermediate [default], old) for
  TLS.  
  → See [Mozilla SSL Configuration Generator](https://ssl-config.mozilla.org)
  for more information.
* Use of an [.env file](https://docs.docker.com/compose/env-file/) for
  [variable substitution in the Compose file](https://docs.docker.com/compose/compose-file/#variable-substitution).

About træfik:

* Homepage: <https://traefik.io/traefik/>
* Documentation: <https://doc.traefik.io/traefik/>

## Table of Contents

* [Prerequisites](#prerequisites)
* [Install](#install)
* [Usage](#usage)
* [Contributing](#contributing)
* [Support](#support)
* [License](#license)

## Prerequisites

For the HTTP challenge you require:

* A publicly accessible host allowing connections on port 80 & 443.
* A DNS record for the domain you want to expose pointing to this host.

## Install

To install docker and docker compose, follow the instructions for your platform:

* [Install Docker Engine | Docker Documentation > Supported platforms](https://docs.docker.com/engine/install/#supported-platforms)
  * Includes Docker Compose V2
* [Post-installation steps for Linux](https://docs.docker.com/engine/install/linux-postinstall/)

## Usage

1. Create an external docker network named "webproxy":  

       docker network create webproxy

1. Change file mode of `config/acme`:

       chmod go+w config/acme

1. Make a copy of all `sample.` files:  

       for file in sample.*; do cp "$file" "${file#sample.}"; done;

1. Update environment variables `TF_ACME_EMAIL` and `TF_CERTRESOLVER_NAME1` in
   '.env':
    * Replace `postmaster@mydomain.com` with a valid email address of yours.
    * Replace `mydomain-com` with a suitable name to identify this certificate
      resolvers configuration.  
      → Dots (`.`) in the name are not allowed!
1. Start the container in detached mode:  

       docker compose up -d

`docker compose` commands must be run in the root directory of the project, i.e.
where 'docker-compose.yml' is located!

### Test

1. Uncomment lines 43 to 55 in 'docker-compose.yml' to enable service "whoami"
   and configure as follows:
    * Replace `whoami.mydomain.com` with the intended domain from
      [Prerequisites](#prerequisites).
    * Replace `mydomain-com` with the value set for `TF_CERTRESOLVER_NAME1` in
      '.env'.
2. Start the container in detached mode:  

       docker compose up -d

3. Wait a bit and visit <http://whoami.mydomain.com> to confirm everything went
   fine.

### Debugging

Use [docker logs](https://docs.docker.com/engine/reference/commandline/logs/) to
see the output of the container:

    docker logs webproxy-traefik-1

## Contributing

PRs accepted. Please submit to the
[GitLab repository](https://gitlab.com/b-data/docker/deployments/traefik).

This project follows the
[Contributor Covenant](https://www.contributor-covenant.org)
[Code of Conduct](CODE_OF_CONDUCT.md).

## Support

Community support: Open a new discussion
[here](https://github.com/orgs/b-data/discussions).

Commercial support: Contact b-data by [email](mailto:support@b-data.ch).

## License

Copyright © 2019 b-data GmbH

Distributed under the terms of the [MIT License](LICENSE).
