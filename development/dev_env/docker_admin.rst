Developer Server (docker-admin)
===============================

Digital Rebar developers often run the system containers directly on there systems for development purposes.  In this mode, there is a convenience command that will start and stop the environment quickly.

To start the local admin, from an updated ``digitalrebar/core`` directory, use ``tools/docker-admin``.

This command will start a new shell session in the ``digitalrebar/deploy/compose`` directory with the correct containers running.  When you exit the command, it will remove and cleanup the environment.

There are several important command line flags to consider when using Docker Admin.

Compose Directives
~~~~~~~~~~~~~~~~~~

Add --no-pull to skip checking for updated containers.  This is very important if you are building your own containers.

Access Mode
~~~~~~~~~~~

By default, Docker Admin will start in forwarder mode.  Forwarder mode keeps all the services internal to Docker and makes it safer to run the full infrastructure.  ``tools/kvm-slave`` will still attach to the Docker network bridge and boot correctly.

Host mode is needed when you want to attach external services like Vagrant VMs to the Docker Admin or use the Reverse Proxy (this may be addressed by updates to the forwarder!).

Note: when using ``--access HOST`` you must supply the IP of your node as an input for the command: ``EXTERNAL_IP=[CIDR] tools/docker-admin access HOST``.  This will set the correct information in the ``access.env`` file.

Omitting Containers
~~~~~~~~~~~~~~~~~~~

You can add ``--no-$$$`` flags like ``--no-dhcp`` to omit containers from the start process. This is handy if you are in HOST mode with a competing DHCP server or want to skip the time it takes to spin up a provisioner.
