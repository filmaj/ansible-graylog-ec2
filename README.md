# PhoneGap Infrastructure

> Provision and configure different kinds of infrastructure in an automated fashion

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

## Requirements

 - [Ansible](http://docs.ansible.com/ansible/intro_installation.html) tool installed
 - Private keys for accessing specific AWS instances
 - Highly recommended to read through the [Ansible Introduction](http://docs.ansible.com/ansible/intro.html),
   but Ansible's concept of [Playbooks](http://docs.ansible.com/ansible/playbooks.html)
   (a collection of tasks defining the desired final state of a machine), is
   worth a read.

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

The subdirectories in this repository contain sets of
[Ansible Playbooks](http://docs.ansible.com/ansible/playbooks.html) and
documentation to manage specific kinds of infrastructure. So far:

 - `graylog-ec2`: A [Graylog](http://www.graylog.org)-powered data storage and
   analytics server running on EC2, powering https://metrics.phonegap.com.
