========
Commands
========

WorldGuard provides five categories of commands:

* Gameplay-related (``/god``, ``/heal``, etc.)
* Working with :doc:`regions <regions/index>`
* Time-sensitive emergency response
* Troubleshooting
* WorldGuard-related (version information, reloading configuration)

See the :doc:`permissions page <permissions>` for the necessary permission nodes. Some commands come with built-in aliases, those are listed in the same row.

.. hint::
    When a parameter (like ``<player>``) is surrounded by ``[]``, it means that the parameter is optional. In addition, don't put ``<`` or ``>`` when entering the command.

Gameplay
========

.. csv-table::
    :header: Command, Parameters, Explanation
    :widths: 8, 8, 25

    /god,"[-s] [<player>]","Give yourself or another player invicinbility. ``-s`` can be optionally specified to have no message emitted."
    /ungod,"[-s] [<player>]","Remove invicinbility from yourself or another player. ``-s`` can be optionally specified to have no message emitted."
    /heal,"[-s] [<player>]","Heal yourself or another player. ``-s`` can be optionally specified to have no message emitted."
    /slay,"[-s] [<player>]","Slay yourself or another player. ``-s`` can be optionally specified to have no message emitted."
    /locate,"[<player>]","Have your compass point to another player (if specified), or pointed to spawn if no player is specified."
    "
    | /stack
    | /;",,"Organize your inventory and automatically stack items."

Selectors
~~~~~~~~~

The commands that take a player name support *selectors*, but they differ from Minecraft's selectors which came about 3 years later.

Players can be matched with:

* By default, players are matched if their name *starts with* the given name
* ``*`` to match all
* ``#<world>`` to match everyone on a certain world
* ``#near`` to match nearby players to the command's user
* ``@<name>`` to match a player's name exactly

Regions
=======

.. csv-table::
    :header: Command, Parameters, Explanation
    :widths: 8, 8, 25

    "
    | /wg flushstates
    | /wg clearstates",[<player>],"Debugging command that clears information about players that is stored for the purpose of applying :doc:`region flags <regions/flags>`. For example, for the ``entry`` flag to work, the player's last 'block' is stored every time they move, which is used to determine whether the player has entered a region. This command can be useful when a player is stuck inside or outside a region due to the ``entry`` or ``exit`` flags, especially after changes in membership, permission groups, etc. The optional player argument will reset states only for the provided player; if not supplied, all players' flag states will be reset."
    "
    | /rg bypass
    | /rg toggle-bypass",[on/off],"Toggles region protection bypass mode, allowing you to ignore region protections (except for PvP deny)"

Additional region commands can be found in the :doc:`regions section <regions/commands>`.

Emergency
=========

.. csv-table::
    :header: Command, Parameters, Explanation
    :widths: 8, 8, 25

    /stopfire,[<world>],"Immediately stop all fire spread on the current or given world."
    /allowfire,[<world>],"Lift the fire spread suspension."
    "
    | /stoplag
    | /halt-activity
    | /haltactivity",[-s],"Remove all entities in all worlds and enter a mode where all physics events and liquid flow events are stopped. In addition, when a chunk loads, all entities in that chunk are removed. The purpose of this command is to solve problems involving thousands of entities or items. However, when this command was added, tameable animals were not a thing and the loss of entities was not an issue, but this is no longer the case and this command should be reserved for extremely desperate occasions. ``-s`` can be provided to make the toggle not issue an announcement in chat."
    "
    | /stoplag
    | /halt-activity
    | /haltactivity",-c [-s],"Disable the 'stop lag' mode. ``-s`` can be provided to make the toggle not issue an announcement in chat."
    "
    | /stoplag
    | /halt-activity
    | /haltactivity",-i,"Display the status of the 'stop lag' mode."

.. _troubleshooting:

Troubleshooting
===============

.. csv-table::
    :header: Command, Parameters, Explanation
    :widths: 8, 8, 25

    /wg report,[-p],"Writes a report file (placed at ``plugins/WorldGuard/report.txt``) detailing information about the server setup (list of plugins, their versions, world settings) as well as WorldGuard's configuration. This command is to easily provide information to others for support purposes. The report does not contain sensitive data. Use ``-p`` to also submit the report to a pastebin site and have a link generated that you can give to others."
    /wg profile,[-p] [-i <milliseconds>] [-t <name>] [<minutes>],"Starts profiling CPU usage of the current running server, and runs the profiler for the given duration (defaulting to 5 minutes if a duration is not specified). The profiler is based on `WarmRoast <https://github.com/sk89q/warmroast>`_.

    By default, results are only collected for the main thread where the world is 'ticked', but ``-t`` can be specified to filter by a different thread name (case in-sensitive). An asterisk (``*``) can be specified instead to profile all threads (i.e. ``-t *``). By default, the profiler uses an interval of 20 milliseconds, but ``-i`` can be used to specify a custom interval in milliseconds between 1 and 100. Lower intervals are more accurate but more performance intensive, while larger intervals are less accurate and less performance intensive.

    Use ``-p`` to also submit the profiling result to a pastebin site and have a link generated that you can give to others. This is recommended because the output of the profiler is formatted by the pastebin site to make it readable."
    /wg stopprofile,,Stops a currently running profiler.
    /wg debug testbreak,[-t] [-s] <player>,"Simulates a 'block break' event. This is explained below."
    /wg debug testplace,[-t] [-s] <player>,"Simulates a 'block place' event. This is explained below."
    /wg debug testinteract,[-t] [-s] <player>,"Simulates a 'block interact' event. This is explained below."
    /wg debug testdamage,[-t] [-s] <player>,"Simulates an 'entity damage' event (use to test PvP and PvE). This is explained below."

Event Simulation
~~~~~~~~~~~~~~~~

The event simulation commands are useful if, for example, players can't break blocks for some reason and you cannot identify the plugin causing it (without more drastic measures). Use of the ``/wg debug testbreak`` command would simulate the block break and record which, if any, plugins chose to block the virtual block break.

In order to use the commands, a player must be provided. The player is the *source* of the event. The target of the event (i.e. the block that being "broken" or the entity that is being "attacked"), however, is from the perspective of the person running the command. However, if ``-t`` is specified, then the target is from the perspective of the source player. The target is whichever block or entity is in the player's crosshair.

Several plugins may be listed in the output of the command, but only the first entry matters. This is because, for example, if Plugin A blocks the action, and Plugin B, which runs afterwards, "unblocks" the action, then Plugin A has no effect. WorldGuard lists the last running plugins first.

.. topic:: An example

    If you want to see why PvP seems to be blocked, have another player (who is not able to PvP) look at you and then run the command::

        /wg debug testdamage -t other_player_name

Be aware that the tests are not entirely complete. This is because Bukkit sometimes throws *other* events for some actions. For example, when filling a bucket, Bukkit fires a *bucket fill event*, which WorldGuard currently does not provide a way to simulate. Lastly, sometimes Minecraft features (like adventure mode or the built-in spawn protection) may be at play.

Use ``-s`` to print a stacktrace with additional information to the console.

.. warning::

    While the events are simulations in that Minecraft won't place or break the block in question, plugins do act upon the events. For example, a plugin may allow you to right click specially marked signs to teleport, and simulating an interact event on a teleport sign may possibly cause the victim player to be teleported.


Miscellaneous
=============

.. csv-table::
    :header: Command, Parameters, Explanation
    :widths: 8, 8, 25

    /wg version,,"Show WorldGuard's version."
    /wg reload,,"Reload WorldGuard's configuration, blacklist, and region data."
    "
    | /wg running
    | /wg queue",,"Show WorldGuard's running tasks. An example of a running task is a UUID lookup of a player that occurs in the background."
