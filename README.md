<a href="https://benz0li.b-data.io/donate?project=2"><img src="https://benz0li.b-data.io/donate/static/donate-with-fosspay.png" alt="Donate with fosspay"></a> <a href="https://liberapay.com/benz0li/donate"><img src="https://liberapay.com/assets/widgets/donate.svg" alt="Donate using Liberapay" height="20"></a>

# Træfik

[This project](https://gitlab.com/b-data/docker/deployments/traefik) serves as
a template to run [Træfik](https://hub.docker.com/_/traefik) v2.5 in a docker
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

*  Homepage: https://traefik.io/traefik/
*  Documentation: https://doc.traefik.io/traefik/

## Prerequisites

For the HTTP challenge you will need:

*  A publicly accessible host allowing connections on port 80 & 443.
*  A DNS record for the domain you want to expose pointing to this host.

### Docker

To install docker, follow the instructions for your platform:

*  [Install Docker Engine | Docker Documentation > Supported platforms](https://docs.docker.com/engine/install/#supported-platforms)
*  [Post-installation steps for Linux](https://docs.docker.com/engine/install/linux-postinstall/)

### Docker Compose

*  [Install Docker Compose](https://docs.docker.com/compose/install/)

## Setup

1.  Create an external docker network named "webproxy":  
    ```bash
    docker network create webproxy
    ```
1.  Make a copy of all `sample.` files:  
    ```bash
    for file in sample.*; do cp "$file" "${file#sample.}"; done;
    ```
3.  Update environment variables `TF_ACME_EMAIL` and `TF_CERTRESOLVER_NAME1` in
    '.env':
    *  Replace `postmaster@mydomain.com` with a valid email address of yours.
    *  Replace `mydomain-com` with a suitable name to identify this certificate
       resolvers configuration.  
       → Dots (`.`) in the name are not allowed!
5.  Start the container in detached mode:  
    ```bash
    docker-compose up -d
    ```

`docker-compose` commands must be run in the root directory of the project, i.e.
where 'docker-compose.yml' is located!

### Test

1.  Uncomment lines 43 to 55 in 'docker-compose.yml' to enable service "whoami"
    and configure as follows:
    *  Replace `whoami.mydomain.com` with the intended domain from
    [Prerequisites](#prerequisites).
    *  Replace `mydomain-com` with the value set for `TF_CERTRESOLVER_NAME1` in
    '.env'.
2.  Start the container in detached mode:  
    ```bash
    docker-compose up -d
    ```
3.  Wait a bit and visit http://whoami.mydomain.com to confirm everything went
fine.

## Debugging

Use [docker logs](https://docs.docker.com/engine/reference/commandline/logs/)
to see the output of the container:

```bash
docker logs webproxy_traefik_1
```
