# Trellis Application

[![Build Status](https://travis-ci.org/trellis-ldp/trellis-app.png?branch=master)](https://travis-ci.org/trellis-ldp/trellis-app)
[![Build status](https://ci.appveyor.com/api/projects/status/xu5qujp9ky2xq0uf?svg=true)](https://ci.appveyor.com/project/acoburn/trellis-app)
[![Coverage Status](https://coveralls.io/repos/github/trellis-ldp/trellis-app/badge.svg?branch=master)](https://coveralls.io/github/trellis-ldp/trellis-app?branch=master)

## Requirements

  * A [Zookeeper](http://zookeeper.apache.org) ensemble (3.5.x or later)
  * A [Kafka](http://kafka.apache.org) cluster (0.11.x or later)
  * Java 8 or 9
  * An [asynchronous processing application](https://github.com/trellis-ldp/trellis-rosid-file-streaming)

The location of Kafka and Zookeeper will be defined in the `./etc/config.yml` file.

## Running Trellis

Unpack a zip or tar distribution. In that directory, modify `./etc/config.yml` to match the
desired values for your system.

To run trellis directly from within a console, issue this command:

        $ ./bin/trellis-app server ./etc/config.yml

**Note**: When running trellis, please be sure to also have an active
[asynchronous processor](https://github.com/trellis-ldp/trellis-rosid-file-streaming).

## Installation

To install Trellis as a [`systemd`](https://en.wikipedia.org/wiki/Systemd) service on linux,
follow the steps below. `systemd` is used by linux distributions such as CentOS/RH 7+ and Ubuntu 15+.

1. Move the unpacked Trellis directory to a location such as `/opt/trellis`.
   If you choose a different location, please update the `./etc/trellis.service` script.

2. Edit the `./etc/environment` file as desired (optional).

3. Edit the `./etc/config.yml` file as desired (optional).

4. Create a trellis user:

        $ sudo useradd -r trellis -s /sbin/nologin

5. Create data directories. A different location can be used, but then please update
   the `./etc/config.yml` file.

        $ sudo mkdir /var/lib/trellis
        $ sudo chown trellis.trellis /var/lib/trellis

6. Install the systemd file:

        $ sudo ln -s /opt/trellis/etc/trellis.service /etc/systemd/system/trellis.service

7. Reload systemd to see the changes

        $ sudo systemctl daemon-reload

8. Start the trellis service

        $ sudo systemctl start trellis

To check that trellis is running, check the URL: `http://localhost:8080`

Application health checks are available at `http://localhost:8081/healthcheck`

## Building Trellis

1. Run `./gradlew clean install` to build the application or download one of the releases.
2. Unpack the appropriate distribution in `./build/distributions`
3. Start the application according to the steps above

