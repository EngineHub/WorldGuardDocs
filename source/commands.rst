========
命令
========

WorldGuard 提供了五种类别的命令：

* 游戏相关 (``/god``, ``/heal``, etc.)
* 与 :doc:`regions <regions/index>` 相关的
* Time-sensitive emergency response
* 错误排查相关
* 与插件本体相关的 (version information, reloading configuration)

查看 :doc:`permissions page <permissions>` 来了解权限节点的配置方法。

.. hint::
    当一个参数（比如 ``<player>``）被 ``[]`` 围绕，这意味着这个参数是可选的。另外，输入命令的时候不需要输入 ``<`` 或 ``>`` 。

Gameplay
========

.. csv-table::
    :header: 命令, 参数, 解释
    :widths: 8, 8, 25

    /god,"[-s] [<player>]","为你自己或者其他玩家开启无敌模式，若你不希望用户收到提示可以加上 ``-s`` "
    /ungod,"[-s] [<player>]","为你自己或其他玩家关闭无敌模式，若你不希望用户收到提示可以加上 ``-s`` "
    /heal,"[-s] [<player>]","治疗你自己或者其他玩家。若你不希望用户收到提示可以加上 ``-s`` "
    /slay,"[-s] [<player>]","杀死你自己或其他玩家。若你不希望用户收到提示可以加上 ``-s`` "
    /locate,"[<player>]","如果指定了玩家的话使你的指南针指向该玩家，否则指向出生点"
    /stack,"","整理你的背包并自动堆叠物品"
    /;,""," ``/stack`` 的别名"

匹配式
~~~~~~~~~

这些命令支持 *匹配式*，但它们与MC官方三年后出的匹配式有所不同。

以下情况中，玩家会被匹配到:

* 默认情况下，如果玩家ID的 *开头* 与输入的字符串相同则能够匹配
* ``*`` 来匹配所有玩家
* ``#<world>`` 来匹配某个世界中的所有玩家
* ``#near`` 来匹配使用命令的玩家周围的玩家
* ``@<name>`` 来匹配一个玩家的确切ID

Regions
=======

.. csv-table::
    :header: Command, Parameters, Explanation
    :widths: 8, 8, 25

    /wg flushstates,,"Debugging command that clears information about players that is stored for the purpose of applying :doc:`region flags <regions/flags>`. For example, for the ``entry`` flag to work, the player's last 'block' is stored every time he or she moves, which is used to determine whether the player has entered a region. This command can be useful when a player is stuck inside or outside a region due to the ``entry`` or ``exit`` flags."

Additional region commands can be found in the :doc:`regions section <regions/index>`.

Emergency
=========

.. csv-table::
    :header: Command, Parameters, Explanation
    :widths: 8, 8, 25

    /stopfire,[<world>],"Immediately stop all fire spread on the current or given world."
    /allowfire,[<world>],"Lift the fire spread suspension."
    /stoplag,[-s],"Remove all entities in all worlds and enter a mode where all physics events and liquid flow events are stopped. In addition, when a chunk loads, all entities in that chunk are removed. The purpose of this command is to solve problems involving thousands of entities or items. However, when this command was added, tameable animals were not a thing and the loss of entities was not an issue, but this is no longer the case and this command should be reserved for extremely desperate occasions. ``-s`` can be provided to make the toggle not issue an announcement in chat."
    /stoplag,-c [-s],"Disable the 'stop lag' mode. ``-s`` can be provided to make the toggle not issue an announcement in chat."
    /stoplag,-i,"Display the status of the 'stop lag' mode."

.. _troubleshooting:

Troubleshooting
===============

.. csv-table::
    :header: Command, Parameters, Explanation
    :widths: 8, 8, 25

    /wg report,[-p],"Writes a report file (placed at ``plugins/WorldGuard/report.txt``) detailing information about the server setup (list of plugins, their versions, world settings) as well as WorldGuard's configuration. This command is to easily provide information to others for support purposes. The report does not contain sensitive data. Use ``-p`` to also submit the report to a pastebin site and have a link generated that you can give to others."
    /wg profile,[-p] [-t <name>] [<minutes>],"Starts profiling CPU usage of the current running server, and runs the profiler for the given duration (defaulting to 5 minutes if a duration is not specified). The profiler is based on `WarmRoast <https://github.com/sk89q/warmroast>`_.

    By default, results are only collected for the main thread where the world is 'ticked', but ``-t`` can be specified to filter by a different thread name (case in-sensitive). An asterisk (``*``) can be specified instead to profile all threads (i.e. ``-t *``).

    Use ``-p`` to also submit the profiling result to a pastebin site and have a link generated that you can give to others. This is recommended because the output of the profiler is formatted by the pastebin site to make it readable."
    /wg debug testbreak,[-t] <player>,"Simulates a 'block break' event. This is explained below."
    /wg debug testplace,[-t] <player>,"Simulates a 'block place' event. This is explained below."
    /wg debug testinteract,[-t] <player>,"Simulates a 'block interact' event. This is explained below."
    /wg debug testdamage,[-t] <player>,"Simulates an 'entity damage' event (use to test PvP and PvE). This is explained below."

Event Simulation
~~~~~~~~~~~~~~~~

The event simulation commands are useful if, for example, players can't break blocks for some reason and you cannot identify the plugin causing it (without more drastic measures). Use of the ``/wg debug testbreak`` command would simulate the block break and record which, if any, plugins chose to block the virtual block break.

In order to use the commands, a player must be provided. The player is the *source* of the event. The target of the event (i.e. the block that being "broken" or the entity that is being "attacked"), however, is from the perspective of the person running the command. However, if ``-t`` is specified, then the target is from the perspective of the source player. The target is whichever block or entity is in the player's crosshair.

Several plugins may be listed in the output of the command, but only the first entry matters. This is because, for example, if Plugin A blocks the action, and Plugin B, which runs afterwards, "unblocks" the action, then Plugin A has no effect. WorldGuard lists the last running plugins first.

.. topic:: An example

    If you want to see why PvP seems to be blocked, have another player (who is not able to PvP) look at you and then run the command::

        /wg debug testdamage -t other_player_name

Be aware that the tests are not entirely complete. This is because Bukkit sometimes throws *other* events for some actions. For example, when filling a bucket, Bukkit fires a *bucket fill event*, which WorldGuard currently does not provide a way to simulate. Lastly, sometimes Minecraft features (like adventure mode or the built-in spawn protection) may be at play.

.. warning::

    While the events are simulations in that Minecraft won't place or break the block in question, plugins do act upon the events. For example, a plugin may allow you to right click specially marked signs to teleport, and simulating an interact event on a teleport sign may possibly cause the victim player to be teleported.


Miscellaneous
=============

.. csv-table::
    :header: Command, Parameters, Explanation
    :widths: 8, 8, 25

    /wg version,,"Show WorldGuard's version."
    /wg reload,,"Reload WorldGuard's configuration, blacklist, and region data."
    /wg running,,"Show WorldGuard's running tasks. An example of a running task is a UUID lookup of a player that occurs in the background."
