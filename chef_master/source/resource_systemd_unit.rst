=====================================================
systemd_unit resource
=====================================================
`[edit on GitHub] <https://github.com/chef/chef-web-docs/blob/master/chef_master/source/resource_systemd_unit.rst>`__

Use the **systemd_unit** resource to create, manage, and run `systemd units <https://www.freedesktop.org/software/systemd/man/systemd.html#Concepts>`_.

**New in Chef Client 12.11.**

Syntax
=====================================================
The systemd_unit resource has the following syntax:

.. code-block:: ruby

   systemd_unit 'sysstat-collect.timer' do
     content <<-EOU.gsub(/^\s+/, '')
     [Unit]
     Description=Run system activity accounting tool every 10 minutes

     [Timer]
     OnCalendar=*:00/10

     [Install]
     WantedBy=sysstat.service
     EOU

     action [:create, :enable]
   end

The full syntax for all of the properties that are available to the **systemd_unit** resource is:

.. code-block:: ruby

   systemd_unit 'name.service' do
     content                String or Hash
     user                   String
     triggers_reload        Boolean
   end

where:

* ``systemd_unit`` is the resource.
* ``name`` is the name of the unit. Must include the type/suffix (e.g. `name.socket` or `name.service`).
* ``user`` is the user account that systemd units run under. If not specified, systemd units will run under the system account.
* ``content`` describes the behavior of the unit


Actions
=====================================================

The systemd_unit resource has the following actions:

``:create``
   Create a unit file, if it does not already exist.

``:delete``
   Delete a unit file, if it exists.

``:enable``
   Ensure the unit will be started after the next system boot.

``:disable``
   Ensure the unit will not be started after the next system boot.

``:nothing``
   Default. Do nothing with the unit.

``:mask``
   Ensure the unit will not start, even to satisfy dependencies.

``:unmask``
   Stop the unit from being masked and cause it to start as specified.

``:preset``
   Restore the preset "enable/disable" configuration for a unit.

   New in Chef Client 14.0.

``:reenable``
   Reenable a unit file.

   New in Chef Client 14.0.

``:revert``
   Revet to a vendor's version of a unit file.

   New in Chef Client 14.0.

``:start``
   Start a unit based in its systemd unit file.

``:stop``
   Stop a running unit.

``:restart``
   Restart a unit.

``:reload``
   Reload the configuration file for a unit.

``:try_restart``
   Try to restart a unit if the unit is running.

``:reload_or_restart``
   For units that are services, this action reloads the configuration of the service without restarting, if possible; otherwise, it will restart the service so the new configuration is applied.

``:reload_or_try_restart``
   For units that are services, this action reloads the configuration of the service without restarting, if possible; otherwise, it will try to restart the service so the new configuration is applied.

Properties
=====================================================

The systemd_unit resource has the following properties:

``user``
   **Ruby Type:** String

   The user account that the systemd unit process is run under. The path to the unit for that user would be something like
   ``/etc/systemd/user/sshd.service``. If no user account is specified, the systemd unit will run under a ``system`` account, with the path to the unit being something like ``/etc/systemd/system/sshd.service``.

``content``
   **Ruby Type:** String, Hash

   A string or hash that contains a systemd `unit file <https://www.freedesktop.org/software/systemd/man/systemd.unit.html>`_ definition that describes the properties of systemd-managed entities, such as services, sockets, devices, and so on. In Chef 14.4, repeatable options can be implemented with an array.

``triggers_reload``
   **Ruby Type:** True, False

   Specifies whether to trigger a daemon reload when creating or deleting a unit. Default is true.

``verify``
   **Ruby Type:** True, False

   Specifies if the unit will be verified before installation. Systemd can be overly strict when verifying units, so in certain cases it is preferable not to verify the unit. Defaults to true.

Examples
=====================================================

.. tag systemd_unit_examples

**Create etcd systemd service unit file**

.. code-block:: ruby

   systemd_unit 'etcd.service' do
     content({Unit: {
               Description: 'Etcd',
               Documentation: ['https://coreos.com/etcd', 'man:etcd(1)'],
               After: 'network.target',
             },
             Service: {
               Type: 'notify',
               ExecStart: '/usr/local/etcd',
               Restart: 'always',
             },
             Install: {
               WantedBy: 'multi-user.target',
             }})
     action :create
   end

.. end_tag
