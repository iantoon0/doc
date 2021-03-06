.. _configure_providers:

Configurating Providers
=======================

The following information applies to the cloud :ref:`providers` in the UI.

WARNING: Provider configurations contain keys and tokens that allow users to request cloud resources.  This information should not be shared!  Do not screencast or share provider configuration unless you intent for this information to become public!

Protip: See :ref:`ug_add_provider` to upload providers from a CLI.

Configuration
-------------

Google Provider
~~~~~~~~~~~~~~~

To get the Google Key, navigate to API Manager...Credentials...Create Credentials for Service Account.  Choose JSON key and download the service account key.  Then update the path to point to your account file.  The upload system will automatically expand the file so the actual JSON appears in the Provider credentials.

Make sure that you use the project ID not the project name.  The project ID should include some extra digits that ensure uniqueness.

Packet Provider
~~~~~~~~~~~~~~~

The project ID for Packet is a full GUID.  This GUID can be found on the URL path when you select a project in the Packet UI.

OpenStack Provider(s)
~~~~~~~~~~~~~~~~~~~~~

NOTE: If there is no private network you should create one.  Ideally, it is named "private" or "internal"

In the Horizon Dashboard, you can quickly get the needed provider details from Download RC file.

* SSH user (defaults to root, centos, ubuntu) is needed if system nodes do not use standard user accounts.  For example, AWS disables root and requires users to start with ``aws-user``.
* Debug Flag will include your credentials in the log when true.
* Networking is not consistent for OpenStack; however, the _auto_ setting in Cloudwrap makes reasonable guesses about Network names including private and internal or public and external.  If the auto guess does not work, then simply provide the name of the network.  You can also provide _none_ so that Cloudwrap does not attempt to resolve the matching network.

When troubleshooting OpenStack, use the debug=true flag.  This will provide the full OpenStack CLI command (including your credentials!) in the log.  This allows you to duplicate the Cloudwrap container actions using Docker exec: ``docker exec -it compose_cloudwrap_1 [openstack snip....]``.  This is a very handy way to test the OpenStack provider details.

DreamCompute:

* Auth-url: ``https://iad2.dream.io:5000/v2.0``
* ssh user: ``dhc-user``
* public network: ``auto``
* private network: ``auto``

Datacenterd.io:

* Auth-url: ``https://compute.datacentred.io:5000/``
* ssh user: ``centos ubuntu``
* public network: ``public``
* private network: ``private``

Vexxhost:

* Auth-url: ``https://auth.vexxhost.net/v2.0/``
* ssh user: ``centos ubuntu root``
* public network: ``public``
* private network: ``pprivate``

Auro.io:

* Auth-url: ``https://api.tor1.auro.io:5000/v2.0``
* ssh user: ``centos ubuntu``
* public network: ``ext-net``
* private network: ``Private``

Bluebox:

* Auth-url: per config
* ssh user: ``ubuntu centos``
* public network: ``internal``
* private network: per config 


Rackspace:

* Auth-url: ``https://identity.api.rackspacecloud.com/v2.0/``
* ssh user: ``root ubuntu centos``
* public network: ``none``
* private network: ``none``

Note: Rackspace networking DOES create public and private networks but they do not show up in neutron.  Cloudwrap will handle the none-none case correctly here.

Debug Provider
~~~~~~~~~~~~~~

The Debug Provider creates Digital Rebar nodes without having a backing IaaS.  It is helpful for testing scale and general workloads.  By default, there is a delay in provisioning debug nodes to help simulate actual node creation.

If you provide a valid IP to the Debug Provider then it will be able to advance the node workflow.


.. _troubleshoot_providers:

Troubleshooting Tips
--------------------

If may take several attempts to get the Provider details exactly right.  This section helps resolve issues with the provider configuration.   You do NOT need to restart the Cloudwrap container when you change provider details: they automatically synchronize.

It is recommended to manually create nodes during the testing phase.

The Cloud Providers use the Cloudwrap container to create and destroy remote nodes.  There are two phases for Cloudwrap operations: create/destroy actions via the API (api.rb) and then waiting (waiter.rb) for created nodes to be available for provisioning.  

Digital Rebar creates a unique SSH key for each node.  When the node is available, a root account is created/updated with the Digital Rebar control key and the SSH key is removed.

Cloudwrap provides detailed logging in the container that helps to monitor the progress of Cloudwrap.  To monitor the logs, you must access the Docker container that runs Cloudwrap.  The logs will allow you to track the creation of nodes and the waiting process.  By watching the IaaS system, you can match provisioning actions and troubleshoot Cloudwap.

  ::

    cd ~/digitalrebar/deploy/compose
    docker-compose logs cloud_wrap

REMINDER: Digital Rebar relies on ICMP (ping) and SSH (port 22) to validate that the node is running.  These ports must be open between the Digital Rebar admin and the node.