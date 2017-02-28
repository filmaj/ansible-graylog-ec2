# Graylog Metrics Server

> Powers https://metrics.phonegap.com

This repository currently contains everything necessary to control a single
server: [the PhoneGap metrics server](https://metrics.phonegap.com). This server
runs [Graylog](http://www.graylog.org), a log management and analytics storage
system, and provides a [Kibana](https://www.elastic.co/products/kibana) web
interface for visualization. It leverages [Ansible](http://ansible.com), a
configuration management software for automating machine setup.

## Technical Overview

If you follow the instructions in this README, you will end up with a server
running the following components / design:

 - [nginx](https://nginx.org): acts as the initial request handler. Check the
   [nginx config in the templates](templates/kibana-nginx-conf.j2) for full
   details. Main things to know about it:
   - listens on ports 80 and 443, and redirects requests coming in on port 80
     (from http) to port 443 (to https)
   - is configured with SSL (see `ssl.yml`), and SSL is terminated at nginx.
   - requests hitting the root of https://metrics.phonegap.com get sent to
     Graylog's web interface (more info on that below)
   - requests hitting `/viz` get sent to Kibana (more below)
   - requests hitting `/gelf` get sent to the GELF HTTP input (more below)
 - [Graylog](https://graylog.org): a full-featured log management solution,
   leveraging [ElasticSearch](https://www.elastic.co/products/elasticsearch)
   as a datastore. Provides abilities to process events on the fly, tag them,
   and even create alerts based on incoming logs.
   - it will be configured with [one HTTP input accepting GELF messages](http://docs.graylog.org/en/2.2/pages/sending_data.html#gelf-sending-from-applications).
     This input listens on the server's loopback interface on port 12201, but
     nginx redirects to it (see above).
   - https://metrics.phonegap.com leads to the main Graylog web interface.
 - [Kibana](https://www.elastic.co/products/kibana), a more sophisticated
   front-end for visualizing incoming data.
   - it listens on the loopback interface on port 5601, but nginx redirects to
     it (see above).
 

## Repository Structure

The [Ansible Playbooks](http://docs.ansible.com/ansible/playbooks.html)
defining this server are:

 - **TODO** `provision.yml`: provisions a new server in Amazon EC2 for running
   the metrics server.
 - `ssl.yml`: interacts with [Let's Encrypt](http://letsencrypt.org) to
   retrieve an SSL certificate and leverages them to secure the metrics server.
   You may also use this playbook to renew the SSL certificate that is already
   in place.
 - `graylog.yml`: once a server is provisioned, this playbook configures
   Graylog for the metrics server to run properly.
 - `kibana.yml`: sets up a kibana instance on the server. Note that this
   changes the nginx configuration on the server. Please check the playbook
   details first!

The active metrics server instance is described in the `hosts` file in this
repository - this should be its fully-qualified domain name, as the inventory
hostname is used in various configuration locations on the server.

**NOTE**: The playbooks contain sensible defaults for PhoneGap-specific
domains and property. Please take a look at the playbooks themselves - they
contain comments near the top fully describing
[Ansible variables](http://docs.ansible.com/ansible/playbooks_variables.html)
you can pass in to customize the playbook for your own purposes.

## Getting Started

First, ensure you have pulled down the submodules:

    $ git submodule update --init

### First Run / Initial Setup

#### Provision a Machine

When setting up the instance for the first time, spin up an EC2 instance and
make sure you have access to the private key to access the instance. PhoneGap
uses an M4-XLarge EC2 instance.

#### Setting up Graylog

You should most definitely set a new admin password for the Graylog instance.
Do so by passing the `set_graylog_admin_password` variable to the
`graylog.yml` playbook like so:

    $ ansible-playbook graylog.yml -i hosts --private-key path/to/metrics-server.pem --extra-vars "set_graylog_admin_password=mysecretpassword"

You can also run the above if you need to reset the Graylog admin password.

#### Securing with SSL

Graylog defaults to a self-signed SSL certificate. If you would like to secure
your instance with a legitimate certificate instead, we have provided an
`ssl.yml` playbook to do that easily for you. To retrieve an SSL certificate
from [Let's Encrypt](http://letsencrypt.org) and put them in the appropriate
place, run:

    $ ansible-playbook ssl.yml -i hosts --private-key path/to/metrics-server.pem

**NOTE**: [Let's Encrypt](http://letsencrypt.org) has
[production rate limits in place](https://letsencrypt.org/docs/rate-limits/) -
please respect these!

#### Installing Kibana

You can, optionally, drop Kibana in to help with visualizing your metrics:

    $ ansible-playbook kibana.yml -i hosts --private-key path/to/metrics-server.pem

**NOTE**: This playbook currently assumes you have leveraged `ssl.yml` to
secure your instance with legit SSL certificates.

### Subsequent Runs

To ensure the metrics server is configured properly, run:

    $ ansible-playbook graylog.yml -i hosts --private-key path/to/metrics-server.pem --extra-vars "graylog_pass=mysecretpassword"

Note the use of the `graylog_pass` variable - this specifies the existing, in-
place Graylog admin password.

To renew the SSL certificate, simply re-run the `ssl.yml` playbook:

    $ ansible-playbook ssl.yml -i hosts --private-key path/to/metrics-server.pem


## References

Shamelessly cobbled together from the following sources:

- http://www.touchstonesoftworks.com/blog/aws_graylog_ansible_provision
