# Docker plugin for Jenkins

Note: This plugin is officially ["up for adoption"](https://www.jenkins.io/doc/developer/plugin-governance/adopt-a-plugin/#faq).
It would benefit from having a new maintainer who uses it "for real work"
and is thus able to test things "for real" before release
instead of relying purely on the unit tests.

## Overview

This plugin allows containers to be dynamically provisioned as Jenkins agents using Docker.
It is a Jenkins cloud plugin for Docker.

The aim of this Docker plugin is to be able to use a
[Docker](https://docs.docker.com/)
host to dynamically provision a Docker container as a Jenkins agent,
run a single build on it,
and then tear down that agent,
without the build process (or Jenkins job definition)
requiring any awareness of Docker.

The Jenkins administrator configures Jenkins with
knowledge of one or more Docker hosts
and knowledge of one or more templates
(which describe
the labels/tags that the template provides,
the Docker image,
how to start it,
etc),
and Jenkins can then run Docker containers to provide Jenkins agents on which Jenkins can run builds.

## See also
* [Software licence](LICENSE)
* Support and [contribution guide](CONTRIBUTING.md)
* [Changelog](https://github.com/jenkinsci/docker-plugin/releases)

----

**Note:** There is _more than one Docker plugin_ for Jenkins.
While this can be confusing for end users, it is even more confusing when end users report bugs in the wrong place.
For example, if you are using Jenkins
[Pipeline / Workflow / `Jenkinsfile`](https://jenkins.io/doc/book/pipeline/docker/)
builds with code including terms like
`docker.withDockerRegistry`
or
`docker.image`
etc., then you are using the
[`docker-workflow`](https://plugins.jenkins.io/docker-workflow)
plugin and should go to its repository instead of this one.

----

**Note:** This plugin does not use the OS's native Docker client;
rather, it uses [docker-java](https://github.com/docker-java/docker-java).
You do not need to install a Docker client on Jenkins or on your agents to use this plugin.

----

**Note:** This plugin does not _provide_ a Docker daemon; it allows Jenkins to _use_ a Docker daemon.
That is, once you have installed Docker somewhere, this plugin will allow Jenkins to make use of it.

----

## Setup

A quick setup includes:

1. Get a Docker environment running
1. Follow the instructions for creating a Docker image that can be used
as a Jenkins agent
_or_ use one of the pre-built images
e.g. [jenkins/inbound-agent](https://hub.docker.com/r/jenkins/inbound-agent/)

### Docker environment

Follow the installation steps on [the Docker website](https://docs.docker.com/).

If your Jenkins controller is not on the same host as the Docker daemon,
you will need to open the Docker TCP port so that Jenkins can communicate with the Docker daemon.
This can be achieved by editing the Docker configuration file and setting e.g.

```sh
DOCKER_OPTS="-H tcp://0.0.0.0:2376 -H unix:///var/run/docker.sock"
```

The Docker configuration file location will depend your system, but it
is likely to be
`/etc/init/docker.conf`
,
`/etc/default/docker`
or
`/etc/default/docker.io`.

### Multiple Docker hosts

If you want to use more than just one host to run containers,
you can use
[Classic Swarm](https://github.com/docker/swarm)
or you can define more than one Jenkins cloud.
The Swarm mode built into the Docker Engine API is not supported
(at present; enhancement contributions would be welcomed).

To use Classic Swarm,
follow its setup instructions and configure Jenkins with the swarm's API endpoint.

### Jenkins configuration

Docker plugin is a Jenkins cloud implementation.
You will need to edit Jenkins system configuration
(**Jenkins, Manage, Nodes, Clouds**)
and add a new Jenkins cloud of type Docker.

![](docs/images/add-new-docker-cloud.png)

Configure the Docker API URL with the required credentials.
The test button lets you check the connection.

Then configure agent templates,
assigning them labels that you can use so your jobs select the appropriate template,
and set the Docker container to be run with whatever container settings you require.

### Creating a Docker image

You need a Docker image that can be used to run Jenkins agent runtime.
Depending on the launch method you select, there are some prerequisites
for the Docker image to be used:

## Launch via SSH

-   [sshd(8)](https://linux.die.net/man/8/sshd) server and a JDK installed.
    You can use
    [jenkins/ssh-agent](https://hub.docker.com/r/jenkins/ssh-agent/)
    as a basis for a custom image.
-   a SSH key (based on the unique Jenkins instance identity) can be
    injected in container on startup, you do not need any credential set
    as long as you use standard openssl sshd.
    ![](docs/images/connect-with-ssh.png)
    For backward compatibility *or* non-standard sshd packaged in your
    Docker image, you also have option to provide manually configured
    ssh credentials
-   **Note:** If the Docker container's host SSH key is not trusted by
    Jenkins (usually the case), then you will need to set the SSH host key
    verification method to "non-verifying".

## Launch via JNLP

-   a JDK installed.
    You can use
    [jenkins/inbound-agent](https://hub.docker.com/r/jenkins/inbound-agent/)
    as a basis for a custom image.
-   Jenkins controller URL has to be reachable from container.
-   container will be configured automatically with agent's name and
    secret, so you do not need any special configuration of the container.

## Launch attached

-   a JDK installed.
    You can use
    [jenkins/agent](https://hub.docker.com/r/jenkins/agent/)
    as a basis for a custom image.

To create a custom image and bundle your favorite tools,
create a `Dockerfile` with the `FROM` to point to one of the
`jenkins/*-agent`
reference images,
and install everything needed for your own usage, e.g.

```
FROM jenkins/inbound-agent
RUN apt-get update && apt-get install XXX
COPY your-favorite-tool-here
```

## Note on `ENTRYPOINT`

Avoid overriding the Docker command, as the SSH launcher relies on it.

You _can_ use an entrypoint to run some side service inside your build agent container before the agent runtime starts and establish a connection
... but you MUST ensure your entrypoint eventually runs the command that was passed in:

    exec "$@"

## Further information

More information can be obtained from the online help built into the Jenkins web UI.
Most configurable fields have explanatory text.
This,
combined with knowledge of [Docker itself](https://docs.docker.com/),
should answer most questions.

## Configuration as code

Jenkins and `docker-plugin` can be configured using a Groovy initialization script
and/or using the [JCasC plugin](https://plugins.jenkins.io/configuration-as-code/).

If you are unsure which method to use, use JCasC.

### Groovy script

For example, this
[configuration script](docs/attachments/docker-plugin-configuration-script.groovy)
could be run automatically upon
[Jenkins post-initialization](https://www.jenkins.io/doc/book/managing/groovy-hook-scripts/)
or through the
[Jenkins script console](https://www.jenkins.io/doc/book/managing/script-console/).
If run,
this script will configure `docker-plugin` to look for a Docker daemon running within the same host as the Jenkins controller
(connecting to the Docker daemon through `unix:///var/run/docker.sock`)
and with the containers connecting to Jenkins using the "attach" method.

### JCasC plugin

Install the [Configuration as Code plugin](https://plugins.jenkins.io/configuration-as-code/) and follow [its example](https://github.com/jenkinsci/configuration-as-code-plugin/tree/master/demos/docker).
