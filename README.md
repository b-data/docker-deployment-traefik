# Træfik

[This project](https://gitlab.b-data.ch/docker/deployments/traefik) serves as
a template to run [træfik](https://hub.docker.com/_/traefik) v2.0 in a docker
container using docker-compose.

The goal is to set up a TLS termination proxy for all Docker containers
providing web services on a **single host**.

**Features**

*  Automatic creation/renewal of Let's Encrypt certificates (HTTP challenge).
*  Provision of the following basic Middlewares:
    *  RedirectScheme: Redirect from HTTP to HTTPS
    *  Headers: HTTP Strict Transport Security with long duration
*  Provision of three configurations (modern, intermediate [default], old) for
   TLS.  
   → See [Mozilla SSL Configuration Generator](https://ssl-config.mozilla.org)
   for more information.
*  Use of an [.env file](https://docs.docker.com/compose/env-file/) for
   [variable substitution in the Compose file](https://docs.docker.com/compose/compose-file/#variable-substitution).
   

**About træfik**

*  Homepage: https://containo.us/traefik/
*  Documentation: https://docs.traefik.io/v2.0/

## Prerequisites

For the HTTP challenge you will need:

*  A publicly accessible host allowing connections on port 80 & 443.
*  A DNS record for the domain you want to expose pointing to this host.

### Docker

To install docker, follow the instructions for your platform:

*  [About Docker Engine - Community > Supported platforms](https://docs.docker.com/install/#supported-platforms)
*  [Post-installation steps for Linux](https://docs.docker.com/install/linux/linux-postinstall/)

### Docker Compose

*  [Install Docker Compose](https://docs.docker.com/compose/install/)

## Setup

1.  Create an external docker network named "webproxy":  
    ```bash
    docker network create webproxy
    ```
2.  Make a copy of [`.env.sample`](.env.sample) and rename it to `.env`.
3.  Update the environment variables `NAME1` and `ACME_EMAIL1` in .env:
    *  Replace `mydomain-com` with a suitable name to identify this certificate
       resolvers configuration.  
       → Dots (`.`) in the name are not allowed!
    *  Replace `postmaster@mydomain.com` with a valid email address of yours.
4.  Make a copy of [`docker-compose.yml.sample`](docker-compose.yml.sample) and
    rename it to `docker-compose.yml`.
5.  Start the container in detached mode:  
    ```bash
    docker-compose up -d
    ```

`docker-compose` commands must be run in the root directory of the project, i.e.
where `docker-compose.yml` is located!

### Test

1.  Uncomment lines 42 to 53 in `docker-compose.yml` to enable service "whoami"
    and configure as follows:
    *  Replace `whoami.mydomain.com` with the intended domain from
    [Prerequisites](#prerequisites).
    *  Replace `mydomain-com` with the value set for `NAME1` in `.env`.
2.  Start the container in detached mode:  
    ```bash
    docker-compose up -d
    ```
3.  Wait a bit and visit http://your_domain to confirm everything went fine.

## Debugging

Use [docker logs](https://docs.docker.com/engine/reference/commandline/logs/)
to see the output of the container:

```bash
docker logs traefik
```
