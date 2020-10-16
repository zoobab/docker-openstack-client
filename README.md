[![noswpatv3](http://zoobab.wdfiles.com/local--files/start/noupcv3.jpg)](https://ffii.org/donate-now-to-save-europe-from-software-patents-says-ffii/)
[![noswpatv3](http://zoobab.wdfiles.com/local--files/start/noupcv3.jpg)](https://ffii.org/donate-now-to-save-europe-from-software-patents-says-ffii/)
# OpenStack client tooling in Docker
Use a docker image for openstack client tooling!

Rather than futzing with all of the python dependencies to get the openstack cli tools working, use this Docker image as way to get started quickly.  There are other openstack-cli images available -- this one uses a smaller footprint by building off of the Alpine image.

## Quick Start

```bash
docker pull jmcvea/openstack-client

# $(PWD) is mounted to allow for actions requiring host filesystem access.  
# See 'Tips' section below
docker run -ti --rm -v $(PWD):/data jmcvea/openstack-client

# source the rc config file
$ source /data/openrc.sh
```


## Manual Build
Clone this repository, then:

```bash
# Build a local docker image
docker build -t openstack-client .

# Start a container from the image and launch into a shell in the container.
# Uses a dotenv file to manage the OpenStack rc env vars.
docker run -it --env-file ~/.osc_rc.env openstack-client

# If you like to mount your config files instead of passing via a dotenv file: (`openrc.sh` must be in the current directory)
docker run -it -v $(PWD):/data openstack-client
$ source /data/openrc.sh
```


## Tips

### Accessing a host directory
The `/data` directory is exposed as a VOLUME that can be mounted.  This is convenient for openstack
commands that might require reading/writing host filesystems.  It is important to remember that
commands such as `openstack image save` should ensure that the location where the image is saved is
in the `/data` folder when using the `--rm` command line option.  Example:

```bash
docker run -it --rm -v $(PWD):/data --env-file ${RC_ENV_FILE:-~/.osc_rc.env} jmcvea/openstack-client openstack image save --file /data/test_image.img ${IMAGE_GUID}
```


### Run one-off commands
Run individual commands easily by passing them as the command to run and overriding the default `/bin/sh` command.  For one-off commands, it's a good practice to remove the container with the `--rm` argument so that you don't collect a bunch of orphaned containers.  You will also want to ensure that the rc environment is configured as part of starting the container -- easiest via a .env file containing the openstack rc env vars.

```bash
docker run -ti --rm -v $(PWD):/data --env-file ${RC_ENV_FILE:-~/.osc_rc.env} jmcvea/openstack-client cinder list`
```


### Simplify your typing with aliases
```bash
# Get into a shell to run openstack commands
alias oscsh='docker run -ti --rm -v $(PWD):/data --env-file ${RC_ENV_FILE:-~/.osc_rc.env} jmcvea/openstack-client'
# Make it look like you're running openstack locally
alias openstack='oscsh openstack'
```


## Contributing

1. Fork ( http://github.com/jmcvea/docker-openstack-client/fork )
2. Create a feature branch (`git checkout -b new-feature`)
3. Commit changes (`git commit -am 'Adding a great new feature'`)
4. Push to the branch (`git push origin new-feature`)
5. Create a new Pull Request


## Copyright

Copyright (c) 2016 Jim McVea

Licensed under MIT
