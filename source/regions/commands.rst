===============
Region Commands
===============

For a list of other (non-region protection related) commands, see the :doc:`../commands` page. Permissions for these commands is detailed on the :doc:`../permissions` page.

Some of these commands may run in the background and then later return results. The list of active background commands can be viewed with ``/wg running``. There is a hard limit to the number of running and queued commands.

Either ``/region`` or ``/rg`` can be used for commands. Some sub-commands have multiple aliases (for example, ``/rg d`` can be used a shortcut to ``/rg define``).

.. tip::
    You can use almost all of these commands from console, but you may need to specify the ``-w`` flag (when available).

    You can also use the `//world <https://worldedit.enginehub.org/en/latest/commands/#command-//world>`_ command from WorldEdit to set the target world (both from console and in-game).

Creating and Removing Regions
=============================

Define
~~~~~~

.. code-block:: text

    /rg define [-w <world>] [-g] <id> [<owner1>] [<owner2>] [... <ownerN>]
    /rg create (...)
    /rg def (...)
    /rg d (...)

Creates a new region with a given ID and an optional list of owners. Your current WorldEdit selection is used for the area of the region.

Region IDs are case-insensitive. Only one region can exist with a given name (per-world).

* ``-g`` will create a new "global" region (not the same as the :doc:`global-region`) that has no physical space, which is useful for creating template regions (see :doc:`priorities`)

.. topic:: Example: Creating a new "shop" region with "sk89q" and "wizjany" as owners
    
    .. code-block:: text

        /rg define shop sk89q wizjany

Remove
~~~~~~

.. code-block:: text

    /rg remove [-w <world>] [-f] [-u] <id>
    /rg rem (...)
    /rg delete (...)
    /rg del (...)

Removes a region.

If the specified region has child regions, then either ``-u`` or ``-f`` must be specified. Both options cannot be specified together.

* ``-u`` changes child regions of the specified region to have no parent
* ``-f`` removes child regions of the specified region

Redefine
~~~~~~~~

.. code-block:: text
    
    /rg redefine [-w <world>] [-g]
    /rg update (...) 
    /rg move (...) 

Changes the physical area associated with an existing region and replaces it with your current WorldEdit selection.

* ``-g`` will create a new "global" region (not the same as the :doc:`global-region`) that has no physical space, which is useful for creating template regions (see :doc:`priorities`)

Claim
~~~~~

.. code-block:: text

    /rg claim <id>

Claims a region, which is for self-serve player-created regions. See :doc:`claiming` for more information.

Editing Memberships
===================

Add Member
~~~~~~~~~~

.. code-block:: text

    /rg addmember [-w <world>] <id> <members...>
    /rg addmem (...)
    /rg am (...)

Adds any number of members to a region. Using ``g:<member>`` will add a permission group instead of a player.

.. topic:: Example: Adding the "builder" group and the player "sk89q" as members of a "spawn" region of the "lobby" world.

    .. code-block:: text

        /rg addmember -w lobby spawn g:builder sk89q

Add Owner
~~~~~~~~~

.. code-block:: text

    /rg addowner [-w <world>] <id> <owners...>
    /rg ao (...)

Adds any number of owners to a region. Using ``g:<owner>`` will add a permission group instead of a player.

.. topic:: Example: Adding the "admins" group and the player "eduardo" as members of a "spawn" region of the "lobby" world.

    .. code-block:: text

        /rg addowner -w lobby spawn g:admins eduardo

Remove Member
~~~~~~~~~~~~~

.. code-block:: text

    /rg removemember [-w <world>] [-a] <id> <members...>
    /rg remmember (...)
    /rg removemem (...)
    /rg remmem (...)
    /rg rm (...)

Removes any number of members from a region. As in the add command, use ``g:<member>`` to specify a permission group.

* ``-a`` will remove all members from the region, ignoring the <members...> argument

Remove Owner
~~~~~~~~~~~~

.. code-block:: text

    /rg removeowner [-w <world>] [-a] <id> <owners...>
    /rg remowner
    /rg ro (...)

Removes any number of owners from a region. As in the add command, use ``g:<owner>`` to specify a permission group.

* ``-a`` will remove all owners from the region, ignoring the <owners...> argument

Getting Information
===================

Select
~~~~~~

.. code-block:: text

    /rg select [-w <world>] [<id>]
    /rg sel (...)
    /rg s (...)

Replaces your current WorldEdit selection with the area of an existing region. If no region is specified, it tries to select the region that you are currently in.

Information
~~~~~~~~~~~

.. code-block:: text

    /rg info [-u] [-s] [-w <world>] [<id>]
    /rg i (...)

Displays information about a specified region, or if no region is specified, the region that you are currently in. If you are in several regions, then a list will shown instead.

* ``-s`` causes the command to select the region (see ``/rg select``)
* ``-u`` causes UUIDs to be shown rather than player's last seen names

.. topic:: Example: Showing information about the :doc:`global-region`
    
    .. code-block:: text

        /rg info __global__

Flags
~~~~~

.. code-block:: text

    /rg flags [-w <world>] [-p <page>] <id>

Displays a paginated, interactive list of flags for the given region.

Explicitly set flags are shown with white values, flags inherited from a parent region are shown in light gray, and unset flags are shown with their default value in dark gray.

Clicking on the values allows you to quickly set and unset flags, and the arrows at the bottom next to the page number can be used to navigate the list.

List
~~~~

.. code-block:: text

    /rg list [-i <id search>] [-p <player>] [-w <world>] [-s] [<page>]

Lists the regions that have been created. A number can be provided to show a certain page.

If a player doesn't have permission to list all regions but has permission to list their own (ones the player is a member or owner of), then the command will automatically only list the player's own regions.

* ``-p <player>`` can be specified to filter on regions that the given player is a member or owner of
* ``-i <id search>`` can be specified to filter region IDs containing the search text
* ``-s`` can be specified to match only regions which physically intersect your WorldEdit selection

.. topic:: Example: Listing regions that "sk89q" is a member or owner of
    
    .. code-block:: text

        /rg list -p sk89q

Setting Region Options
======================

Flag
~~~~

.. code-block:: text

    /rg flag <id> <flag> [-w <world>] [-g <group>] [-e] [<value>]
    /rg f (...)

Sets a flag on a region (see :doc:`flags` for more information).

To unset a flag, don't specify a value.

To set a flag to a blank value, use ``-e``. This is useful for setting flags like ``greeting`` to a blank message to override the flag set in a different, larger, and lower priority region. If ``-e`` is specified in addition to a value, the value is discarded.

* ``-g <group>`` specifies the region group (see :doc:`flags`)
* ``-e`` sets an empty value

.. topic:: Example: Setting the ``pvp`` flag of "mall" to "deny" with region group "nonmembers"
    
    .. code-block:: text

        /rg flag mall pvp -g nonmembers deny

.. topic:: Example: Unsetting the ``greeting`` flag on "mall"
    
    .. code-block:: text

        /rg flag mall greeting

.. topic:: Example: Setting the ``greeting`` flag to an empty value
    
    .. code-block:: text

        /rg flag mall greeting -e

Priority
~~~~~~~~

.. code-block:: text

    /rg setpriority [-w <world>] <id> <priority>
    /rg priority (...)
    /rg pri (...)

Sets the priority of a region. See :doc:`priorities` for more information.

The default priority of a region is 0.

Parent
~~~~~~

.. code-block:: text

    /rg setparent [-w <world>] <id> [<parent>]
    /rg parent (...)
    /rg par (...)

Sets the parent of a region. See :doc:`priorities` for more information.

To unset a parent priority, specify no parent.

.. topic:: Example: Setting the parent of "plot1" to "mall"

    .. code-block:: text

        /rg setparent plot1 mall

.. topic:: Example: Removing the parent of "plot1"

    .. code-block:: text

        /rg setparent plot1

Miscellaneous Commands
======================

Teleport
~~~~~~~~

.. code-block:: text

    /rg teleport [-w <world>] [-c] [-s] <id>
    /rg tp (...)

Teleports yourself to the location specified by either the ``spawn`` or ``teleport`` :doc:`flags <flags>`.

* ``-s`` selects the spawn flag rather than the teleport flag
* ``-c`` teleports you to the geometric center of the region even if neither flag is set. This requires you to be in spectator mode.

Management Commands
===================

Load
~~~~

.. code-block:: text

    /rg load [-w <world>]
    /rg reload (...)

Reloads the region data from file or database. If recent changes were made in-game to the region data, this may cause data loss.

The load operation occurs in the background and will not pause the server. If the command is used before a previous load has completed, the new load will be queued. There is a limit to the maximum number of operations that can be queued.

Save
~~~~

.. code-block:: text

    /rg save [-w <world>]
    /rg write (...)

Saves the region data to disk.

.. tip::
    Region data is saved automatically soon after any changes are made, so this command does not need to be called explicitly.

The save operation occurs in the background and will not pause the server.  If the command is used before a previous save has completed, the new save will be queued. There is a limit to the maximum number of operations that can be queued.


Migrate Database
~~~~~~~~~~~~~~~~

.. code-block:: text

    /rg migratedb <from> <to>

Migrates from one type of storage driver (see :doc:`storage`) to another.

Valid choices for "from" and "to" are:

* ``yaml``
* ``mysql``

Migration does not automatically enable the target storage driver -- that must be done in the :doc:`../config`.

.. warning::
    Be sure to make a backup before running migration.

.. warning::
    This command does not run in the background and will pause the entire server. If your server software has server pause detection, this may kill the server during migration and abort the migration process. If migration is aborted or fails, you may need to empty the target storage before re-running migration.

Migrate UUID
~~~~~~~~~~~~

.. code-block:: text

    /rg migrateuuid

Converts player names in the region data to Mojang UUIDs.

Names that have no corresponding UUIDs will either be removed or left remaining depending on the :doc:`../config` (the setting is ``keep-names-that-lack-uuids``).

.. warning::
    Be sure to make a backup before running migration.

.. warning::
    This command does not run in the background and will pause the entire server. If your server software has server pause detection, this may kill the server during migration and abort the migration process.

Migrate Region Heights
~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: text

    /rg migrateheights [-w <world>]

Extends regions of the current or specified world that were physically defined from min y <= 0 to max y >= 255 (i.e. the world limits pre-MC 1.18) to the new world height limits.

Useful for updating from 1.17 or before to 1.18 or later if you had a lot of regions which were defined at the world limits. Note that if you intentionally had regions beyond the world limits before 1.18, you will need to move them beyond the new world limits manually.

.. warning::
    Be sure to make a backup before running migration.

.. warning::
    This command does not run in the background and will pause the entire server. If your server software has server pause detection, this may kill the server during migration and abort the migration process.
