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


