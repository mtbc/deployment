# OME Infrastructure

Tools, scripts and other systems infrastructure used to support the work of the OME http://www.openmicroscopy.org/

Eventually this will be used for setting up production servers including OMERO instances.
If you are interested in this work please be aware it is a relatively new initiative and still under development, so please [contact us](http://www.openmicroscopy.org/site/community) if you require more information.


[![Build Status](https://travis-ci.org/openmicroscopy/infrastructure.png)](http://travis-ci.org/openmicroscopy/infrastructure)

## OME Ansible repository


This playbook uses Ansible 2.0 features. See
[the Ansible documentation](http://docs.ansible.com/ansible/intro_installation.html)
for full installation instructions.

### Installation

- Create a virtual environment and install Ansible:

        virtualenv ~/venvs/ansible
        ~/venvs/ansible/bin/pip install ansible

- If using Ansible with OpenStack, you might want to install `shade`:

        ~/venvs/ansible/bin/pip install shade

- Clone this repository:

        git clone https://github.com/openmicroscopy/infrastructure.git

-   Clone the repository containing the inventory, host and group vars files.
    Ansible will automatically look for `host_vars` and `group_vars`
    directories in the parent directory of the inventory file.


### Running the development ansible

-   Activate the virtualenv:

        source venvs/ansible/bin/activate

All Ansible tools should now be in your path. Alternatively adjust the paths in this script as necessary:

    shell
    #!/bin/sh
    source ~/venvs/ansible/bin/activate
    exec "$(basename $0)" "$@"


And copy/symlink it into a directory on your `PATH` as `ansible`, `ansible-playbook`, etc (the script should automatically run the ansible command corresponding to it's file name).

#### Examples

In the following examples replace example-hosts with the private host inventory file

Dry-run `ci-provision.yml` for all hosts listed in `ci-provision.yml`:
- `-u` Login as this user
- `--ask-become-pass` prompt for sudo password
- `-C` Dry-run mode
- `-v` Verbose (repeat to increase verbosity)

Note this may fail since some tasks are dependent on others being completed:

    ansible-playbook -i example-hosts -u $USERNAME --ask-become-pass -C -v ci-provision.yml

Run `provision.yml`:

    ansible-playbook -i example-hosts -u $USERNAME --ask-become-pass ci-provision.yml

Run `provision.yml` for all subset of the hosts or groups listed in `provision.yml`:

    ansible-playbook -i example-hosts -u $USERNAME --ask-become-pass ci-provision.yml --limit $HOST_OR_GROUP_NAME

List the hosts that would be targeted by a command, don't do anything else:

    ansible-playbook -i example-hosts ci-provision.yml --list-hosts


Playbooks which do not alter hardware can often be tested in Docker instead of a full VM, for example by using the [omero-ssh](https://github.com/manics/ome-docker/blob/omero-ssh/omero-ssh/Dockerfile) image:

    docker run -d omero-ssh
    # Optional:
    ssh-copy-id omero@172.17.1.1
    # Pass -K if sudo requires a password, and -k if ssh keys aren't setup
    ansible-playbook -i etc/test-hosts -u omero ci-deployment.yml -bv
