..
 This work is licensed under a Creative Commons Attribution 3.0 Unported
 License.

 http://creativecommons.org/licenses/by/3.0/legalcode

==========================================
Enable update router status of L3agent
==========================================

https://blueprints.launchpad.net/neutron/+spec/l3-router-status

This proposal enables update router status of l3agent conforming with
actual status of the router.

Problem Description
===================

Neutron l3router status is set ACTIVE only. The status value is always
ACTIVE if even l3agent failed to process a router which hosted by the
l3agent.

Proposed Change
===============

This proposal modify l3agent so that l3agent can change router status
confrming with actual process.

The proposal adds PENDING_UPDATE and ERROR to router status. The
router includes DVR and HA router.

* PENDING_UPDATE: Status under updating router
* ERROR: Status which l3agent failed to process

When neutron server creates a router, it creates DB with router status ACTIVE,
then it sends a notification to l3agent as the router is scheduled. The l3agent
receives the notification and processes configuration of the router. The
l3agent doesn't send a completion or failure notification to the server after
the process. This proposal changes the behaviour like the following.

1. Nuetron server receives router create API, then creates DB of router with
   status ACTIVE.
2. The server update DB of the router with status PENDING_UPDATE as the router
   is scheduled by something trigger.
3. The server sends a notification to l3agent.
4. L3agent processes configuration of the router.
5. L3agent sends a notification to the server about the result.
6. The server receives the notification, then update DB of the router. If it's
   a completion notification, the server change router status from
   PENDING_UPDATE to ACTIVE. If it's a failure notification, the server change
   router status from PENDING_UPDATE to ERROR.

This process can also apply to update process.

We know there are the other triggers for router status to be changed. For
example, router status must be ERROR when network node hosted the router is
down. However, this proposal doesn't take such triggers into consideration but
focuses on API triggers.

Users can check router status by GET API.

::

+-----------------------+--------------------------------------+
| Field                 | Value                                |
+-----------------------+--------------------------------------+
| admin_state_up        | True                                 |
| external_gateway_info |                                      |
| id                    | 9e8eec98-44a1-4a9c-9ab9-eef08f960772 |
| name                  | router                               |
| routes                |                                      |
| status                | PENDING_UPDATE                       |
| tenant_id             | 361a3a2efa3842a8b8153cf83a915a6d     |
+-----------------------+--------------------------------------+

+-----------------------+--------------------------------------+
| Field                 | Value                                |
+-----------------------+--------------------------------------+
| admin_state_up        | True                                 |
| external_gateway_info |                                      |
| id                    | 9e8eec98-44a1-4a9c-9ab9-eef08f960772 |
| name                  | router                               |
| routes                |                                      |
| status                | ERROR                                |
| tenant_id             | 361a3a2efa3842a8b8153cf83a915a6d     |
+-----------------------+--------------------------------------+

This proposal also assumes a case of DVR router and HA router. In the
situation, multiple l3agents manage a router which shares DB. Some l3agents
succeed in configuring a router, others may fail to do the router on the
agent. Neutron server changes the status to ERROR in the case. Router status is
able to be ACTIVE only when all agents succeed in configuring.

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

None

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


Dependencies
============

None

Testing
=======

Tempest Tests
-------------


Functional Tests
----------------


API Tests
---------


Documentation Impact
====================

User Documentation
------------------


Developer Documentation
-----------------------


References
==========

* https://blueprints.launchpad.net/neutron/+spec/l3-router-status
* https://bugs.launchpad.net/neutron/+bug/1341290


