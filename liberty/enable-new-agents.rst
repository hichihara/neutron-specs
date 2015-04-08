..
 This work is licensed under a Creative Commons Attribution 3.0 Unported
 License.

 http://creativecommons.org/licenses/by/3.0/legalcode

==========================================
Add enable_new_agents to agent conf
==========================================

https://blueprints.launchpad.net/neutron/+spec/enable-new-agents

This proposal adds enable_new_agents config for operator maintenance to
l3-agent and dhcp-agent in network node.

Problem Description
===================

Neutron doesn't have a way to test a newly added network node by deploying test
resource before any customer resource on the node is deployed. When Cloud
operators control network resources, they want to prevent users controlling the
resources. For example, they may try to create test resources on a new node
while deploying the new node. Neutron can prevent users to create the resources
on the node by agent's admin_state_up=False, but Neutron cannot start agent
with admin_state_up=False. Neutron always starts agent with
admin_state_up=True. Nova and Cinder has the setting of "enable_new_services"
in each conf to disable the initial service status to archive this.

Neutron also has a problem for the maintenance scenario. Neutron can
prevent all users to create the resources on the agent with
admin_state_up=False, but Neutron usualy cannot allow admin user only
to create the resources. Currently, if
enable_services_on_agents_with_admin_state_down configuration
parameter is True, admin can create the resources on the agent.

Proposed Change
===============

This proposal adds enable_new_agents config for operator maintenance
to l3-agent and dhcp-agent in network nodes. Neutron agent's
admin_state_up is controlled by this config while starting.

* Neutron agent starts with admin_state_up=True when enable_new_agents=True.
  This behaviour is general.
* Neutron agent starts with admin_state_up=False when enable_new_agents=False.
  This behaviour is maintenance mode.

The default value is True because this proposal don't intend to change a
traditional behaviour.

The proposal also presupposes that
enable_services_on_agents_with_admin_state_down is True in Neutron
servers.

The following is maintenance scenario.

Precondition: Neutron servers run with
enable_services_on_agents_with_admin_state_down=True.

1. Admin prepares a new network node.
2. Admin adds enable_new_agents=False to l3-agent and dhcp-agent config
   and starts their agents.
3. They run with admin_state_up=False.
4. Admin creates network resources on the agents.
5. Admin creates VM connected the network resources, then admin
   confirms the capability.
6. Admin deletes all resources and update agents to be
   admin_state_up=True after the test.


Data Model Impact
-----------------

None

REST API Impact
---------------

None

Security Impact
---------------

None

Notifications Impact
--------------------

None

Other End User Impact
---------------------

None

Performance Impact
------------------

None

IPv6 Impact
-----------

None

Other Deployer Impact
---------------------

This proposal adds enable_new_agents to neutron agent config(dhcp_agent.ini,
l3_agent.ini). This config is set True as default value. It doesn't change a
traditional befaviour of the agents.

Developer Impact
----------------

None

Community Impact
----------------

None

Alternatives
------------

None

Implementation
==============

Assignee(s)
-----------

ichihara-hirofumi

Work Items
----------

* Add enable_new_agents config and the implements

Dependencies
============

None

Testing
=======

Tempest Tests
-------------

None

Functional Tests
----------------

None

API Tests
---------

None

Documentation Impact
====================

User Documentation
------------------

The new config options will be documented.

Developer Documentation
-----------------------

None

References
==========

* https://blueprints.launchpad.net/neutron/+spec/enable-new-agents
* http://lists.openstack.org/pipermail/openstack-operators/2015-March/006434.html
