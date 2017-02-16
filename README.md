# Graylog Server on EC2

> Provision and configure a graylog metrics server in an automated fashion

## Why?

Sometimes, we need servers to power our services and tools. Setting up servers
boils down to two parts: set up a machine to act as the server, and configure
it so it functions properly. Cloud providers give us an easy way to handle
managing the physical realities of running a server, and
[configuration management](https://continuousdelivery.com/foundations/configuration-management/)
gives us an automated way to reproduce the server's software, libraries and
configuration. Combining these two together gives us a fast and powerful way of
managing our servers, and gives us the ability to _reproduce_ and _trace_ our
infrastructure.

## Overview

### Technology Used

 - AWS as a cloud provider, handling the physical realities of our servers for
   us.
 - [Ansible](http://docs.ansible.com/ansible/) as the main tool to use to
   interact with AWS as well as configure machines to reach a specific and
   consistent state. Ansible uses SSH to communicate with machines, and, when
   used appropriately, guarantees [idempotency](http://docs.ansible.com/ansible/glossary.html#term-idempotency),
   or, put more simply: running a set of ansible commands against a machine
   one or more times always leaves the machine in a single consistent state.
   No more `sudo apt-get install mysql` and zero-fucks-given sysadmining.

### Repository Structure

This repository currently contains everything necessary to control a single
server: the PhoneGap metrics server. The [Ansible Playbooks](http://docs.ansible.com/ansible/playbooks.html)
defining this server are:

 - **TODO** `provision.yml`: provisions a new server in Amazon EC2 for running
   the metrics server.
 - `configure.yml`: once a server is provisioned, this playbook configures the
   server with the appropriate libraries, dependencies, software and
   configuration necessary for the metrics server to run.

## Requirements

 - [Ansible](http://docs.ansible.com/ansible/intro_installation.html) tool installed
 - Private keys for accessing specific AWS instances
 - Highly recommended to read through the [Ansible Introduction](http://docs.ansible.com/ansible/intro.html),
   but Ansible's concept of [Playbooks](http://docs.ansible.com/ansible/playbooks.html)
   (a collection of tasks defining the desired final state of a machine), is
   worth a read.

## Getting Started

There is currently one playbook in this repository: the `configure.yml`
playbook. The active metrics server instance is described in the `hosts` file
in this repository. To ensure the metrics server is configured properly, run:

    $ ansible-playbook configure.yml -i hosts --private-key path/to/metrics-server.pem

Check out the [`vars` inside `configure.yml`](https://github.com/filmaj/ansible-graylog-ec2/blob/master/configure.yml#L5)
to see what variables you can override at the command line; you can do so with
the `--extra-vars` flag, e.g.:

    $ ansible-playbook configure.yml -i hosts --private-key ~/.ssh/metrics-server.pem --extra-vars "metrics_disk=/dev/xvdf"

## References

Shamlessly cobbled together from the following sources:

- http://www.touchstonesoftworks.com/blog/aws_graylog_ansible_provision
