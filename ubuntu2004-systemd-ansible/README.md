# ubuntu2004-systemd-ansible

a Dockerfile for images that are used for testing ansible playbooks.

taken from: https://github.com/j8r/dockerfiles/blob/e1c34be01fbdd37b8a8513c40c7e912c26eae700/systemd/ubuntu/20.04.Dockerfile

one modification was adding `sudo` to the installed packages

only hard feature for the image:

- allow playbook run to match what it would run like an canonical standard ubuntu
  virtual machine on AWS

## Supported tags
 - `20.04`, `latest`

## Usage

The image built is supposed to run on Github Actions. While one wouldn't
necessarily be running this themselves, the configuration for Github Actions may
be relevant to place here. Here's an example minimal configuration I created
when I followed Jeff Geerling's fast demo for "Continuous testing with molecule,
ansible and Github Actions."

```
conrado@clappy  ~/ggans  ggans main 🍺
$ tree
.
├── main.yml
└── molecule
    ├── default
    │   ├── INSTALL.rst
    │   ├── converge.yml
    │   ├── molecule.yml
    │   └── verify.yml
    └── gh-actions
        ├── INSTALL.rst -> ../default/INSTALL.rst
        ├── converge.yml -> ../default/converge.yml
        ├── molecule.yml
        └── verify.yml -> ../default/verify.yml

3 directories, 9 files
conrado@clappy  ~/ggans  ggans main 🍺
$ cat molecule/gh-actions/molecule.yml
---
dependency:
  name: galaxy
driver:
  name: docker
lint:
  set -e
  yamllint .
  ansible-lint
platforms:
  - name: ggans
    image: conrado/ubuntu2004-systemd-ansible:latest
    command: ""
    volumes:
      - /sys/fs/cgroup:/sys/fs/cgroup:ro
    privileged: true
    pre_build_image: true
provisioner:
  name: ansible
verifier:
  name: ansible
```

## To test locally (you can only do this on a Linux host, mac doesn't have the right virtual filesystems):

1. Run the container as a daemon

`docker run -d --name ubuntu2004-systemd-ansible --tmpfs /tmp --tmpfs /run --tmpfs /run/lock -v /sys/fs/cgroup:/sys/fs/cgroup:ro conrado/ubuntu2004-systemd-ansible`

or if it doesn't work

`docker run -d --name ubuntu2004-systemd-ansible --privileged -v /sys/fs/cgroup:/sys/fs/cgroup:ro conrado/ubuntu2004-systemd-ansible`

2. Enter to the container

`docker exec -it ubuntu2004-systemd-ansible sh`

3. Remove the container

`docker rm -f ubuntu2004-systemd-ansible`
