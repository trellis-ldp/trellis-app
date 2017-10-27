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
follow the steps below. `systemd` is used by linux distributions such as CentOS/RHEL 7+ and Ubuntu 15+.

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

## Configuration

The web application wrapper (Dropwizard.io) makes many [configuration options](http://www.dropwizard.io/1.2.0/docs/manual/configuration.html)
available. Any of the configuration options defined by Dropwizard can be part of your application's configuration file.

Trellis defines its own configuration options, including:

        partitions:
            - id: partition-name
              binaries:
                path: /path/to/binaries
              resources:
                path: /path/to/resources
              baseUrl: http://localhost:8080/

| Name | Default | Description |
| ---- | ------- | ----------- |
| id | (none) | The unique identifier for a partition |
| binaries / path | (none) | The path for storing binaries |
| resources / path | (none) | The path for storing resources |
| baseUrl | (none) | A defined baseUrl for resources in this partition. If not defined, the `Host` request header will be used |

        namespaces:
            file: /path/to/namespaces.json

| Name | Default | Description |
| ---- | ------- | ----------- |
| file | (none) | The path to a JSON file defining namespace prefixes |

        zookeeper:
            ensembleServers: localhost:2181

| Name | Default | Description |
| ---- | ------- | ----------- |
| ensembleServers | (none) | The location of the zookeeper ensemble servers (comma separated) |

        kafka:
            bootstrapServers: localhost:9092

| Name | Default | Description |
| ---- | ------- | ----------- |
| bootstrapServers | (none) | The location of the kafka servers (comma separated) |

        auth:
            webac:
                enabled: true
            anon:
                enabled: true
            jwt:
                enabled: true
                base64Encoded: false
                key: secret
            basic:
                enabled: true
                usersFile: /path/to/users.auth

| Name | Default | Description |
| ---- | ------- | ----------- |
| webac / enabled | true | Whether WebAC authorization is enabled |
| anon / enabled | false | Whether anonymous authentication is enabled |
| jwt / enabled | true | Whether jwt authentication is enabled |
| jwt / base64Encoded | false | Whether the key is base64 encoded |
| jwt / key | (none) | The signing key for JWT tokens |
| basic / enabled | true | Whether basic authentication is enabled |
| basic / usersFile | (none) | The path to a file where user credentials are stored |

        cors:
            enabled: true
            allowOrigin:
                - "*"
            allowMethods:
                - "GET"
                - "POST"
                - "PATCH"
            allowHeaders:
                - "Content-Type"
                - "Link"
            exposeHeaders:
                - "Link"
                - "Location"
            maxAge: 180
            allowCredentials: true

| Name | Default | Description |
| ---- | ------- | ----------- |
| enabled | false | Whether CORS is enabled |
| allowOrigin | * | A list of allowed origins |
| allowMethods | "PUT", "DELETE", "PATCH", "GET", "HEAD", "OPTIONS", "POST" | A list of allowed methods |
| allowHeaders | "Content-Type", "Link", "Accept", "Accept-Datetime", "Prefer", "Want-Digest", "Slug", "Digest" | A list of allowed request headers |
| exposeHeaders | "Content-Type", "Link", "Memento-Datetime", "Preference-Applied", "Location", "Accept-Patch", "Accept-Post", "Digest", "Accept-Ranges", "ETag", "Vary" | A list of allowed response headers |
| maxAge | 180 | The maximum age of pre-flight messages |
| allowCredentials | true | Whether the actual request can be made with credentials |

        async: false

| Name | Default | Description |
| ---- | ------- | ----------- |
| async | false | Whether resource caches should be handled by an async processor |

        cacheMaxAge: 86400

| Name | Default | Description |
| ---- | ------- | ----------- |
| cacheMaxAge | 86400 | The value of the cache-related response headers |

