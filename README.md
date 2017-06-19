# mist.ee

Mist.io helps you operate, monitor and govern your computing infrastructure,
across clouds and platforms.

The enterprise version of mist.io also includes Role Based Access, VPN tunnels,
and Insights for cost optimization.

The enterprise version is also available as a service at https://mist.io.


## Hardware requirements

Recommended hardware resources are:
    4 CPU cores
    8 GB RAM
    10 GB disk (accessible to /var/lib/docker/)


## Installation

Mist.io is a large application split into microservices which are packaged in
docker containers. The easiest way to run it is by using `docker-compose`. So,
in order to run it, one needs to install a recent version of `docker` and
`docker-compose`.

In order to install the latest stable release, head over to mist.ee's github
[releases](https://github.com/mistio/mist.ee/releases/) and follow the
instructions there.


### Prerequisites

In order to be able to run the EE version of mist.io, first of all, you will
need to have a dockerhub account, so that we can grant you access to our
private docker images.


## Running mist.io

Switch to the directory containing the `docker-compose.yml` file and run

    docker-compose up -d

This will start all the mist.io docker containers in the background.

To create a user for the first time, first run

    docker-compose exec api sh

This should drop you in a shell into one of the mist.io containers. In there,
run

    ./bin/adduser --admin --docker-cloud admin@example.com

Replace the email address with yours. Try running `./bin/adduser -h` for more
options. The `--docker-cloud` flag will add the docker daemon hosting the
mist.io installation as a docker cloud in the created account.

You may also subscribe to a larger Plan in order to extend the limit of your
monitored machines. In the same shell, run:

    ./bin/addplan my-org

Specify the name of your Organization, as created above. For more options, run
`./bin/addplan -h`.

Visit http://localhost and login with the email and password specified above.

Welcome to mist.io! Enjoy!

You can now visit http://docs.mist.io for the full mist.io Documentation.


## Configuring mist.io

After the initial `docker-compose up -d`, you'll see that a configuration file
is created in `./config/settings.py`. Edit this file to modify configuration.

For instance, you may choose to alter the monitor server's hostname passed to
collectd in order to be able to also ship monitoring metrics to mist.io from
outside the docker's network, since the default hostname is resolvable only
inside docker-compose's internal network.

Additionally, you may provide your own mailer settings. The default mailing
service is provided by the `mailmock` container. Have a look by tailing its
logs.

Finally, restart the services (with `docker-compose restart`, for instance).


### Configuring your system

Elasticsearch may need some tweaks in case you are on a Linux host. If you
experience Out-Of-Memory (OOM) exceptions, try to increase the size of your
virtual address space:

To apply the change on a live system type, run as root:

        sysctl -w vm.max_map_count=262144

To make it permanent add it to /etc/sysctl.conf:

        vm.max_map_count = 262144


## Managing mist.io

Mist.io is managed using `docker-compose`. Look that up for details. Some
useful commands:

    # See status of all applications
    docker-compose ps

    # Almost all containers should be in the UP state. An exception to this
    # is shortlived containers. Currently the only such container is
    # elasticsearch-manage. This should run for a few seconds and exit 0 if
    # everything went fine.

    # Restart nginx container
    docker-compose restart nginx

    # See the logs of the api and celery containers, starting with the last
    # 50 lines.
    docker-compose logs --tail=50 -f api celery

    # Stop mist.io
    docker-compose stop

    # Start mist.io
    docker-compose start
    # or even better
    docker-compose up -d

    # Stop and remove all containers
    docker-compose down

    # Completely remove all containers and data volumes.
    docker-compose down -v
