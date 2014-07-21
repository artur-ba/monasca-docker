Dockerfiles for building Monasca Images as well as [ansible](http://www.ansible.com) config for running the cluster.
These images are intended for development.

Ansible is used to simplify the start up of the entire cluster because its docker plugin allows for easy setup with proper links between different containers.
To install ansible, `pip install ansible` also add the python docker client library `pip install docker-py`

Run the playbook to start Monasca up `ansible-playbook mini-mon.yml`.
Stop and/or delete with the stopped and absent actions `ansible-playbook mini-mon.yml --extra-vars "action=absent"`
If the DOCKER_HOST environment variable is not defined you will need to define it as the default is not currently working.

The agent container can be attached to, `docker attach agent`, this will drop you to a shell from which you can run the smoke test `python /tests/smoke.py`.

## Tooling
  - [nsenter](https://github.com/jpetazzo/nsenter) allows you to attach to running containers. Docker is working on this functionality natively but isn't there yet.
    - Run like `docker-enter my_container /bin/bash`

# Building Docker Images
The majority of the installation work is done via chef-solo leveraging the same cookbooks used for vagrant and test baremetal setups.
The actual building of images is done with standard docker commands leveraging the various Dockerfiles.

## Details
- The base image used Ubuntu 14.04 with a modern chef and berksfile installed, largely copied from similar 12.04 images.
- The databases are setup within the image and are not setup to use a Docker volume, this is the easier setup for dev.

### Admin Access notes
- The Influxdb ui is available on its ip at port 8083 with user/pass root/root

# Todo
- Many containers should be changed to log to console rather than a log file
  - notification
  - persister
  - Probably others
- A number of containers start up mulitple processes and have sleeps in the startup scripts so dependencies are up in time. It would be much nicer
  to be smart about these waits.
  - kafka
  - notification
  - thresh
- I need to open up the various admin ports for the services.
- Automated builds for the containers could be a big advantage and in that case each image should have its own git repo.
- Setup clustered versions of the various containers. This will mean a custom zookeeper.

# Known Issues
- If DOCKER_HOST is not set the docker_url in the ansible playbook does not properly fall back to the default.
