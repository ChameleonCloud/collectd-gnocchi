==================
 collectd-gnocchi
==================
.. image:: https://img.shields.io/pypi/v/collectd-gnocchi.svg
    :target: https://pypi.python.org/pypi/collectd-gnocchi

.. image:: https://img.shields.io/travis/gnocchixyz/collectd-gnocchi.svg
    :target: https://travis-ci.org/gnocchixyz/collectd-gnocchi

This is an output plugin for `collectd`_ that send metrics to `Gnocchi`_. It
will create a resource type named _collectd_ (by default) and a new resource
for each of the host monitored.

Each host will have a list of metrics created dynamically using the following
name convention::

  plugin-plugin_instance/type-type_instance-value_number

In order for the metric to be created correctly, be sure that you have matching
`archive policies rules`_.

.. _`collectd`: http://collectd.org
.. _`Gnocchi`: http://gnocchi.xyz
.. _archive policies rules: http://gnocchi.xyz/rest.html#archive-policy-rule

Installation
============

From sources using::

  pip install .

In order to use this plugin you will need a server running the **Gnocchi 3.1**
or greater.

Configuration
=============

Once installed, you need to enable it in your `collectd.conf` file this way::

.. include:: collectd-gnocchi.conf

You can also copy the provided `collectd-gnocchi.conf` from this repository in
e.g. `/etc/collectd.d` if your distribution supports it.

Without tuning the Gnocchi configuration, metrics are only readable by their
owner project, rather than by every user. Modify /etc/gnocchi/policy.json by
adding a rule matching the project creating the power metrics::

  "power_metrics_project": "'<power_metrics_project_id>':%(created_by_project_id)s",

Update the following rules to grant permission if the rule we just created is valid::

  "get resource": "rule:admin_or_creator or rule:resource_owner or rule:power_metrics_project",
  "get metric": "rule:admin_or_creator or rule:metric_owner or rule:power_metrics_project",
  "get measures":  "rule:admin_or_creator or rule:metric_owner or rule:power_metrics_project",

Retrieving measures
===================

Install the OpenStack client, as well as the Gnocchi client::

  pip install --user python-openstackclient
  pip install --user gnocchiclient

You can retrieve power measurements for a node using its UUID::

  openstack metric measures show power --resource-id=<node_uuid>
