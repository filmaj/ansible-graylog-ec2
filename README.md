# PhoneGap Infrastructure Management

> Provision and configure servers supporting PhoneGap in an automated fashion

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

This repo uses several technologies:
 - AWS as a cloud provider, handling the physical realities of our servers for
   us.
 - [Ansible](http://docs.ansible.com/ansible/) as the main tool to use to
   interact with AWS as well as configure machines to reach a specific and
   consistent state. Ansible uses SSH to communicate with machines, and, when
   used appropriately, guarantees [idempotency](http://docs.ansible.com/ansible/glossary.html#term-idempotency),
   or, put more simply: running a set of ansible commands against a machine
   one or more times always leaves the machine in a single consistent state.

## Requirements

 - [Ansible](http://docs.ansible.com/ansible/intro_installation.html)
 - Private keys for accessing specific AWS instances - talk to Fil

## Getting Started
