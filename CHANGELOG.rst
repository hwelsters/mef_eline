#########
Changelog
#########
All notable changes to the MEF_ELine NApp will be documented in this file.

[Unreleased]
************

Added
=====
- Added more content keys ``evc_id, name, metadata, active, enabled, uni_a, uni_z`` to events from ``mef_eline``
- Added ``uni_a`` and ``uni_z`` to ``attributes_requiring_redeploy``
- Added ``metadata`` to EVC schema
- Allow the creation of ``any`` and ``untagged`` EVC.
- Added api request ``POST /v2/evc/metadata`` to add metadata to EVCs
- Added api request ``DELETE /v2/evc/metadata/<key>`` to delete metadata from EVCs
- Subscribed to new event ``kytos/of_multi_table.enable_table`` as well as publishing ``kytos/mef_eline.enable_table`` required to set a different ``table_id`` to flows.
- Added ``settings.TABLE_GROUP_ALLOWED`` set containning the allowed table groups, for now ``'evpl', 'epl'`` are supported.
- Added ui support for primary and secondary constraints
- Exposed default ``SPF_ATTRIBUTE`` on settings.py, the default value is still `"hop"`. This value will be parametrized whenever ``primary_constraints.spf_attribute`` or ``secondary_constraints.spf_attribute`` isn't set

Changed
=======
- Moved request circuit ``k-button`` out of k-accordion-item since it's mandatory
- The traces being check rely on ``type``: ``last`` to be considered valid.
- ``dl_vlan`` value is mapped to an integer in range [1, 4095] for the ``/traces`` requests to ``sdntrace_cp``
- Augmented ``GET /v2/evc/`` to accept parameters ``metadata.key=item``
- Upgraded ``openapi-core`` to ``0.16.6`` from ``0.14.5``.
- Changed ``openapi.yml`` to be used as validation spec for request related methods ``updated()``, ``create_schedule()`` and ``update_schedule()``.
- ``mef_eline`` now supports table group settings from ``of_multi_table``
- Changed increasing amount of flows being sent, now it is fixed. Amount can be changed on ``settings.BATCH_SIZE``
- Changed ui constraints default values to pass the spec validation
- Changed intra-switch EVC with a disabled switch or interface is not longer allowed to be created
- Adapted ``mef_eline`` to ordered endpoints in a link. Endpoints for flow creation are compared with switch ids to overcome ordered endpoint.
- ``primary_constraints.spf_attribute`` and ``secondary_constraints.spf_attribute`` will only be set in the database if they've been set in the request.
- Changed UI spf_attribute to allow it to be ``default``, meaning an unset value

General Information
===================
- ``@rest`` endpoints are now run by ``starlette/uvicorn`` instead of ``flask/werkzeug``.

Removed
=======

Fixed
=====
- fixed ``minimum_flexible_hits`` EVC attribute to be persistent
- fixed attribute list for path constraints to include ``reliability``
- fixed unnecessary redeploy of an intra-switch EVC on link up events
- fixed ``check_list_traces`` to work with the new version of SDN traces
- fixed updating EVC to be an intra-switch with invalid switch or interface
- fixed EVC UI list to sort VLAN A and VLAN Z fields to acts as number
- fixed non-redeployment of circuit when patching with ``{"queue_id":null}``

General Information
===================
- Replaced ``@validate`` with ``@validate_openapi`` from kytos core

[2022.3.1] - 2023-02-14
***********************

Added
=====
- Added ``uni_a`` and ``uni_z`` to ``attributes_requiring_redeploy``

Fixed
=====
- fixed ``minimum_flexible_hits`` EVC attribute to be persistent
- fixed attribute list for path constraints to include ``reliability``
- fixed unnecessary redeploy of an intra-switch EVC on link up events


[2022.3.0] - 2023-01-23
***********************

Added
=====
- Added ``service_level`` EVC attribute to set the service network convergence level, the higher the better
- EVCs with higher service level priority will be handled first during network convergence, including when running ``sdntrace_cp`` consistency checks.
- Added support for constrained paths for primary dynamic paths and failover paths, ``primary_constraints`` and ``secondary_constraints`` can be set via API.
- Added ``service_level`` UI component on ``k-toolbar`` and made it editable.
- Added ``sb_priority`` UI component on ``k-toolbar``.
- Added ``queue_id`` UI component on ``k-toolbar``.
- Documented ``GET /v2/evc?archived`` query arg on openapi.yml
- Added ``flow_removed_at`` and ``updated_at`` parameters in EVC.
- Added ``execution_rounds`` in EVC to be used by the consistency check. 
- Added logging message for ``link_up`` events.

Changed
=======
- ``priority`` has been renamed to ``sb_priority`` (southbound priority), ``./scripts/001_rename_priority.py`` can be used to update EVC documents accordingly
- ``GET /v2/evc?archived=true`` will only return archived EVCs
- k-toolbar UI component won't expose UNI tag type anymore, if a tag value is set, it'll assume it's tag type vlan.
- Consistency check uses the new ``PUT /traces`` endpoint from `sdntrace_cp` for bulk requests.

Removed
=======
- ``priority`` is no longer supported in the API spec

Fixed
=====
- Removed the failover path after removing flows
- Removed failover flows when an EVC gets deleted
- Validated ``queue_id`` on ``POST /v2/evc``
- Fixed found but unloaded message log attempt for archived EVCs
- Fixed EVC validation to catch nonexistent links interfaces
- Allowed ``primary_path`` to be empty on update when ``dynamic_backup_path`` is true and ``backup_path`` to be empty too


[2022.2.0] - 2022-08-12
***********************

Added
=====

- Reintroduced Q-in-Q when creating the flows for an EVC.
- Optimize list of circuits filters
- Migrated persistency from kytos/storehouse to MongoDB (integration with pymongo)
- ELineController and DB models
- Retries to handle database ``AutoReconnect`` exception.
- ``DynamicPathManager.get_disjoint_paths`` to calculates the maximum disjoint
  paths from a given "unwanted_path" (typically the currently in use path) using
  the approach described in blueprint EP029
- Fully dynamic EVCs can now benefit from Failover Paths (``failover_path``),
  which improves significantly the convergence performance when facing link down
- Refactored Link Down handler to dispatch Kytos Events to handle traditional
  EVCs (EVCs that are not eligible for failover) more efficiently.

Changed
=======

- ``DynamicPathManager.get_paths`` to also supports ``max_paths`` parameter and
  then request more paths from pathfinder (default to 2, which is also the
  default on pathfinder)

General Information
===================
- ``scripts/storehouse_to_mongo.py`` can be used to migrate data from storehouse to MongoDB


[2022.1.5] - 2022-02-11
***********************

Fixed
=====

- Adjust default value for `settings.WAIT_FOR_OLD_PATH` since now it measured
  in execution rounds instead of seconds


[2022.1.4] - 2022-02-11
***********************

Fixed
=====
-  Fix UI to keep kytos panel width with default value


[2022.1.3] - 2022-02-11
***********************

Fixed
=====
-  Fix UI to display the scrollbar in the autocomplete results list


[2022.1.2] - 2022-02-03
***********************

Fixed
=====
-  Fix UI to make tag fields optional and editable


[2022.1.1] - 2022-02-03
***********************

Fixed
=====
-  Fix UI list button not re-rendering the content


[2022.1.0] - 2022-01-31
***********************

Added
=====
-  Added utils ``notify_link_available_tags``` function
-  Publish ``kytos/mef_eline.link.available_tags`` event
-  Hooked ``notify_link_available_tags`` when choosing or making vlans available


[2.6.0] - 2021-11-30
********************

Added
=====
- Parametrized ``force`` option as ``True`` when removing flows for reliability


[2.5.1] - 2021-05-28
********************

Fixed
=====
- Fixed UI to list and create EVCs
- Added locks to avoid race conditions


[2.5] - 2021-03-31
******************

Added
=====
- Queue ID can be defined when creating an EVC.
- Method to handle flow mod errors.
- Method to check if two EVCs have a common UNI.
- 2-byte prefix in cookie field.

Changed
=======
- Deployment of EVCs loaded on startup delayed.
- Required versions of python packages updated.
- Removed user VLAN encapsulation.
- EVC id reduced from 16 to 14 bytes.

Fixed
=====
- Thread locks when saving to the storehouse, avoiding race conditions.


[2.4] - 2020-07-23
******************

Added
=====
- Added EVC status check when deploying using schedule.
- Serialize circuit scheduler for storehouse.
- Fix VLAN availability on interfaces after using them.
- Documentation about delete method.
- Added '.travis.yml' to enable Travis CI.
- Added tags decorator to run tests by type and size.
- Install flows when UNIs are in the same switch.

Changed
=======
- Updated HTTP return messages and codes when an error happens.
- Accept EVCs where UNI has no tag.
- Path status now return disabled state if any of its links is disabled.
- Updated method to get the shortest path, now it returns more paths.
- Changed enable/enabled to update _enabled attribute and activate/active to
  update _active attribute.
- Updated OpenApi Models description and documentation.

Deprecated
==========
- Do not create a job when action is not ``create`` or ``remove``.

Removed
=======
- Removed dependencies.

Fixed
=====
- Fixed enable on update EVCs.


[2.3.1] - 2019-03-15
********************

Added
=====
- Scrutinizer running after every push to GitHub repository.
- Linter checking all python code.

Fixed
=====
- Fixed link up/down events from kytos/topology (#99 and #100).
- Load VLANs from storehouse (#101).
- Check path status using kytos/topology (#102).
- Fixed tests to mock call to get links from kytos/topology (#118).

[2.3.0] - 2018-12-14
********************

Added
=====
- Added more API documentation.
- Added EVC flow removal based on cookies.
- Added EVC deletion API method.

Fixed
=====
- Fixed circuit not being deployed.
- Fixed `current_path` changes not being saved on storehouse (#85).
- Fixed storehouse always creating a new box (#91).
- Fixed handling of link up/down events.

[2.2.2] - 2018-10-15
********************

Fixed
=====
- Fixed error when creating a circuit with scheduling and without `start_date`
   (#79 and #80)

[2.2.1] - 2018-09-06
********************
Added
=====
- Added endpoint to allow update circuit informations.
- Added structure to support ci integration: unittests, linter, tox and
  scrutinizer.
- Added some tests for the class already created.
- Added some LinkProtection features:
  - Added method to handle when links goes up or end_maintenance.
  - Added method to handle when links goes down or under_maintenance.
  - When primary_path and backup_path goes down or under_maintenance and
    `dynamic_backup_path` is setted as True a dynamic path is choosed using the
    PathFinder NApp when the primary and backup path is both down or not
    setted.
  - When the primary_path is down and backup_path exists and is UP the circuit
    will change from primary_path to backup_path.
  - When the primary_path change from DOWN to UP the circuits will change to
    the primary_path.
  - When the circuit is disabled the circuit will not be deployed.
  - Added method to looking for links affected was created using the python
    `set` class to be more fast to find the links affected.

Changed
=======
- Change deploy to use primary_path, backup_path or a dynamic_path.
- Improved the Schedule to use advanced python scheduler (APScheduler) library.
Thanks @ajoaoff for recommends this library.
- The attribute circuit_scheduler in the EVC class should have some instances
of CircuitScheduler, this instances will have the information about the
scheduler informations.

Fixed
=====
- Fixed the create circuit method when sending a invalid request
- Fixed some linter warnings.

[2.2.0] - 2018-06-15
********************
Added
=====
- Added EVC class to represent a circuit.
- Added Schedule class to schedule the circuit deploy.
- Added persistence with the NApp kytos/storehouse.

Changed
=======
- Refactor main.py and models.py

Fixed
=====
- Removed duplicated key in openapi.yml

[2.1.0] - 2018-04-20
********************
Added
=====
- Add Schedule class
- Add Mef-Eline component

Changed
=======
- Update openapi.yml
- Update README.rst

[2.0.0] - 2018-03-09
********************
Added
=====
- New /evc endpoint.
- Future endpoint URLs.
- EPL and EVPL support, with VLANs in both endpoints.

Changed
=======
- Method to install flows to the switches.
- List of links now represented by Link objects.

Removed
=======
- Old /circuit endpoints.
