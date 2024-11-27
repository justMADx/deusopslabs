# Docker Role

An Ansible role for installing and configuring Docker on Ubuntu-based systems.

## Requirements

- Ansible 2.9+ is required.
- Target system must be Ubuntu.

## Role Variables

The following variables can be configured in the role to customize behavior:

| Variable                | Default Value                                                                                          |
|-------------------------|:-------------------------------------------------------------------------------------------------------|
| `docker_apt_packages:`  | - apt-transport-https                                                                                  |
|                         | - ca-certificates                                                                                      |
|                         | - curl                                                                                                 |
|                         | - software-properties-common                                                                           |
| `docker_repository`     | `"deb [arch=amd64] https://download.docker.com/linux/ubuntu {{ ansible_distribution_release }} stable` |
| `docker_gpg_key`        | `https://download.docker.com/linux/ubuntu/gpg`                                                         |
| `docker_user`           | `"{{ ansible_user }}"`                                                                                 |
| `  docker_package_name` | `docker-ce`                                                                                            |


## Dependencies

This role has no dependencies on other roles.