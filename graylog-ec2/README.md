# Graylog Metrics Server

> Powers https://metrics.phonegap.com

This repository currently contains everything necessary to control a single
server: [the PhoneGap metrics server](https://metrics.phonegap.com). This server
runs [Graylog](http://www.graylog.org), a log management and analytics storage
system, and provides a [Kibana](https://www.elastic.co/products/kibana) web
interface for visualization.

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
