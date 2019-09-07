=====================================================
About Nodes
=====================================================
`[edit on GitHub] <https://github.com/chef/chef-web-docs/blob/master/chef_master/source/nodes.rst>`__

.. tag node

A node is any machine---physical, virtual, cloud, network device, etc.---that is under management by Chef.

.. end_tag

.. tag node_types

The types of nodes that can be managed by Chef include, but are not limited to, the following:

.. list-table::
   :widths: 100 420
   :header-rows: 1

   * - Node Type
     - Description
   * - .. image:: ../../images/icon_node_type_server.svg
          :width: 100px
          :align: center

     - A physical node is typically a server or a virtual machine, but it can be any active device attached to a network that is capable of sending, receiving, and forwarding information over a communications channel. In other words, a physical node is any active device attached to a network that can run a Chef Infra Client and also allow that Chef Infra Client to communicate with a Chef Infra Server.
   * - .. image:: ../../images/icon_node_type_cloud_public.svg
          :width: 100px
          :align: center

     - A cloud-based node is hosted in an external cloud-based service, such as Amazon Web Services (AWS), OpenStack, Rackspace, Google Compute Engine, or Microsoft Azure. Plugins are available for knife that provide support for external cloud-based services. knife can use these plugins to create instances on cloud-based services. Once created, Chef Infra Client can be used to deploy, configure, and maintain those instances.
   * - .. image:: ../../images/icon_node_virtual_machine.svg
          :width: 100px
          :align: center

     - A virtual node is a machine that runs only as a software implementation, but otherwise behaves much like a physical machine.
   * - .. image:: ../../images/icon_node_type_network_device.svg
          :width: 100px
          :align: center

     - A network node is any networking device---a switch, a router---that is being managed by a Chef Infra Client, such as networking devices by Juniper Networks, Arista, Cisco, and F5. Use Chef to automate common network configurations, such as physical and logical Ethernet link properties and VLANs, on these devices.
   * - .. image:: ../../images/icon_node_type_container.svg
          :width: 100px
          :align: center

     - Containers are an approach to virtualization that allows a single operating system to host many working configurations, where each working configuration---a container---is assigned a single responsibility that is isolated from all other responsibilities. Containers are popular as a way to manage distributed and scalable applications and services.

.. end_tag

The key components of nodes that are under management by Chef include:

.. list-table::
   :widths: 100 420
   :header-rows: 1

   * - Component
     - Description
   * - .. image:: ../../images/icon_chef_client.svg
          :width: 100px
          :align: center

     - .. tag chef_client_summary

       Chef Infra Client is an agent that runs locally on every node that is under management by Chef Infra Server. When a Chef Infra Client is run, it will perform all of the steps that are required to bring the node into the expected state, including:

       * Registering and authenticating the node with Chef Infra Server
       * Building the node object
       * Synchronizing cookbooks
       * Compiling the resource collection by loading each of the required cookbooks, including recipes, attributes, and all other dependencies
       * Taking the appropriate and required actions to configure the node
       * Looking for exceptions and notifications, handling each as required

       .. end_tag

       .. tag security_key_pairs_chef_client

       RSA public key-pairs are used to authenticate Chef Infra Client with the Chef Infra Server every time a Chef Infra Client needs access to data that is stored on the Chef Infra Server. This prevents any node from accessing data that it shouldn't and it ensures that only nodes that are properly registered with the Chef Infra Server can be managed.

       .. end_tag

   * - .. image:: ../../images/icon_ohai.svg
          :width: 100px
          :align: center

     - .. tag ohai_summary

       Ohai is a tool that is used to collect system configuration data, which is provided to Chef Infra Client for use within cookbooks. Ohai is run by Chef Infra Client at the beginning of every Chef run to determine system state. Ohai includes many built-in plugins to detect common configuration details as well as a plugin model for writing custom plugins.

       The types of attributes Ohai collects include but are not limited to:

       * Operating System
       * Network
       * Memory
       * Disk
       * CPU
       * Kernel
       * Host names
       * Fully qualified domain names
       * Virtualization
       * Cloud provider metadata

       Attributes that are collected by Ohai are automatic level attributes, in that these attributes are used by Chef Infra Client to ensure that these attributes remain unchanged after Chef Infra Client is done configuring the node.

       .. end_tag

Manage Nodes
=====================================================

.. This section is just tossed in here to keep track of it. Probably needs a super-heavy edit. And much of it probably lives elsewhere.

There are several ways to manage nodes directly: via knife, Chef Automate, or by using Chef Infra Client's command-line tools.

* knife can be used to create, edit, view, list, tag, and delete nodes.
* knife plug-ins can be used to create, edit, and manage nodes that are located on cloud providers.
* Chef Automate can be used to create, edit, view, list, tag, and delete nodes. In addition, node attributes can be modified and nodes can be moved between environments.
* Chef Infra Client can be used to manage node data using the command line and JSON files. Each JSON file contains a hash, the elements of which are added as node attributes. In addition, the ``run_list`` setting allows roles and/or recipes to be added to the node.
* chef-solo can be used to manage node data using the command line and JSON files. Each JSON file contains a hash, the elements of which are added as node attributes. In addition, the ``run_list`` setting allows roles and/or recipes to be added to the node.
* The command line can also be used to edit JSON files and files that are related to third-party services, such as Amazon EC2, where the JSON files can contain per-instance metadata that is stored in a file on-disk and then read by chef-solo or Chef Infra Client as required.

Node Objects
=====================================================

For Chef Infra Client, two important aspects of nodes are groups of attributes and run-lists. An attribute is a specific piece of data about the node, such as a network interface, a file system, the number of clients a service running on a node is capable of accepting, and so on. A run-list is an ordered list of recipes and/or roles that are run in an exact order. The node object consists of the run-list and node attributes, which is a JSON file that is stored on the Chef Infra Server. Chef Infra Client gets a copy of the node object from the Chef Infra Server during each Chef Infra Client run and places an updated copy on the Chef Infra Server at the end of each Chef Infra Client run.

.. tag node_attribute

An attribute is a specific detail about a node. Attributes are used by Chef Infra Client to understand:

* The current state of the node
* What the state of the node was at the end of the previous Chef Infra Client run
* What the state of the node should be at the end of the current Chef Infra Client run

Attributes are defined by:

* The state of the node itself
* Cookbooks (in attribute files and/or recipes)
* Roles
* Environments

During every Chef Infra Client run, Chef Infra Client builds the attribute list using:

* Data about the node collected by Ohai
* The node object that was saved to the Chef Infra Server at the end of the previous Chef Infra Client run
* The rebuilt node object from the current Chef Infra Client run, after it is updated for changes to cookbooks (attribute files and/or recipes), roles, and/or environments, and updated for any changes to the state of the node itself

After the node object is rebuilt, all of the attributes are compared, and then the node is updated based on attribute precedence. At the end of every Chef Infra Client run, the node object that defines the current state of the node is uploaded to the Chef Infra Server so that it can be indexed for search.

.. end_tag

About Node Names
=====================================================
The name of a node is required as part of the authentication process to the Chef Infra Server. The name of each node must be unique within an organization, but otherwise can be any string that matches the following regular expression::

   /^[\-[:alnum:]_:.]+$/

The name of a node can be obtained from the ``node_name`` attribute in the client.rb file or by allowing Ohai to collect this data during a Chef Infra Client run. When Ohai collects this data during a Chef Infra Client run, it uses the node's FQDN, which is always unique within an organization, as the name of the node.

Using the FQDN as the node name, and then allowing Ohai to collect this information during each Chef Infra Client run, is the recommended approach and the easiest way to ensure that the names of all nodes across the organization are unique.

Related Content
===================================
* `Chef Overview </chef_overview.html>`_
* `Chef Infra Client Overview </chef_client_overview.html>`_
* `Chef Infra Server </server_overview.html>`_
* `Run Lists </run_lists.html>`_
* `Attributes </attributes.html>`_

Next Steps
===================================
* `Bootstrap Nodes </install_bootstrap.html>`_
