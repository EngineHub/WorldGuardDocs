============
Region Flags
============

每个区域都可以有不同的flag设置，其中一些flag的用途包括：

* 用 ``pvp`` 来阻止玩家之间的战斗
* 用 ``entry`` 来阻止其他玩家进入你的领地（区域）
* 用 ``snow-melt`` 来禁用雪融化
* 用 ``receive-chat`` 阻止某区域内的玩家接收消息
* 用 ``vine-growth`` 停止藤曼的生长

一个区域可以有很多个不同的flag被设置，但是每个flag只能设置为一个确定的值。用 ``/region flag`` 命令来设置区域的flag，以下是一些例子::

    /region flag spawn pvp deny
    /region flag spawn greeting Welcome to spawn!
    /region flag hospital heal-amount 2

不给flag指定任何值可以把flag恢复成默认值::

    /region flag spawn pvp

用"info"命令来列出已设置的flag::

    /region info spawn

.. _组:

组
=============

有时候，你希望某一个flag只对某一组玩家生效，而不是所有进入此区域的玩家；这可以通过在设置flag时指定一个组达到。组的列表如下：

* all (everyone)
* members
* owners
* nonmembers
* nonowners

即

* 所有人
* 区域成员
* 区域主人
* 非区域成员
* 非区域主人

当设置flag时，使用 ``-g`` 参数来指定组，例如::

    /region flag spawn -g nonmembers pvp deny

你**不能**在同一个区域内为不同的组的同一个flag设置不同的值，如果你想要那样设置，可以考虑创建多个子域。

.. note::
    When there are multiple overlapping regions, a player must be a member of the region *on which the flag is set* or *on one of the region's child regions* (when region inheritance is involved). This is explained further in :doc:`priorities`.

.. tip::
    The entry and exit flags default to "non-member", meaning setting them to "deny" will prevent non-members from entering/exiting the region. The teleport and spawn location flags default to "members", which means that only members can take advantage of them by default. All other flags provided by WorldGuard default to "everyone".

Types of Flags
==============

Each flag is of a certain type that determines what kind of values it may take. For example, the *heal-amount* flag is an numeric flag, so you can only set numeric values for it.

.. csv-table::
    :header: Type, Kind of values
    :widths: 5, 30

    state, "Either 'allow' or 'deny' (explained later)"
    string, "Any form of text"
    integer, "A number that does not have decimals (5, but not 5.5)"
    double, "Numbers that may have decimals (5, 5.5, 2.425)"
    location, "A location in a world"
    boolean, "True or false"
    set, "A list of unique entries"

Internally, there are more types, but it should generally not be of concern.

Conflicting Flags
=================

Sometimes, a certain location may have multiple overlapping regions with different values for the same flag. The following rules are used to determine which values are selected:

* Regions will inherit the value of a flag from its parent, **if** the region did not have the flag set. 
* Higher priority regions will override lower-priority regions.
* The global region is considered like any other region, except it is at the lowest possible priority.

However, it is still possible for there to be conflicting flag values even after that process. Imagine two different regions at the same priority, for example. At that point, the value of the flag is decided differently depending on the type of flag:

* For state flags, if ``deny`` is present, the result is ``deny``. Otherwise, if ``allow`` is present, then the final value is ``allow``.
* For other flags, the result is not defined. For that reason, do not, for example, set two different greeting messages in the same area with the same priority.

If a flag is not defined at all, then the default behavior is whichever is most sensible. For example, if "item pickup" is not defined, WorldGuard defaults to allowing it.

Flag Listing
============

Flags are broken down into categories below.

Overrides
~~~~~~~~~

.. csv-table::
    :header: Flag, Type, description
    :widths: 10, 5, 30

    passthrough,state,"This flag is short for 'passthrough build'. It has nothing to do with movement.

    * If not set **(default)**, then the region protects it area.
    * If set to ``deny``, then the region protects its area.
    * If set to ``allow``, then the region **no longer** protects its area.

    Where does the flag come into use?

    * When you are using other flags (PvP, healing, etc.) and you don't want to prevent building.
    * Why not set ``build`` to ``allow`` (explained later) instead? That would override other regions and let people build!"

Protection-Related
~~~~~~~~~~~~~~~~~~

.. csv-table::
    :header: Flag, Type, description
    :widths: 10, 5, 30

    build,state,"Everything:

    * Whether blocks can be mined or placed
    * Whether doors, levers, etc. (but not inventories) can be used
    * Whether entities and blocks can be interacted with
    * Whether player versus player combat is permitted
    * Whether sleeping in a bed is permitted
    * Whether inventories can be accessed
    * Whether vehicles (boats, minecarts) can be placed
    * etc."
    interact,state,"Everything that involves 'using' a block or entity:

    * Whether doors, levers, etc. (but not inventories) can be used
    * Whether inventories can be accessed
    * Whether vehicles (including animals) can be mounted
    * etc."
    block-break,state,Whether blocks can be mined
    block-place,state,Whether blocks can be placed
    use,state,"Whether doors, levers, etc. (but not inventories) can be used"
    damage-animals,state,"Whether players can harm friendly animals (cows, sheep, etc)"
    chest-access,state,Whether inventories can be accessed
    ride,state,Whether vehicles (including animals) can be mounted
    pvp,state,Whether player versus player combat is permitted
    sleep,state,Whether sleeping in a bed is permitted
    tnt,state,Whether TNT detonation or damage is permitted
    vehicle-place,state,"Whether vehicles (boats, minecarts) can be placed"
    vehicle-destroy,state,Whether vehicles can be destroyed
    lighter,state,Whether flint and steel can be used

.. warning::
    None of these flags are player-specific. For example, the block-break flag, if set to deny, **prevents pistons from breaking blocks**.

    To understand why, consider the fact that players can fling TNT into a region from outside, or a player can build an inchworm piston machine that moves into another region. While these actions were caused by a player, realistically attempting to figure which player built the TNT cannon or used it is much more difficult. However, you still want to prevent someone from blowing up spawn with a TNT cannon.

    Outright blocking TNT cannons or pistons is the wrong solution. Pistons and TNT cannons should be allowed in *some* cases. For example, a TNT cannon or piston inside should work *within* the region.

    First off, remember who can build in regions: it's **not** players, it's **members**. When we consider pistons or TNT, it should be no different. How does WorldGuard figure out whether a piston machine or TNT cannon is a member of a region? **If it's inside the region,** of course!

    When you create a region, before setting any flags on it:

    * Members may build
    * Non-members may **not** build

    TNT cannons and pistons inside are allowed to work because they are "members." An imaginary player, "Bobby," who isn't a member yet, is unable to place or break blocks. Once you add Bobby to the region, then Bobby can build.

    When you set the protection flags, you override this behavior. If you set ``block-break`` to ``deny``, then even members are unable to break blocks. Bobby cannot break blocks. A TNT cannon inside cannot break blocks. A piston inside cannot break blocks. **You break pistons.**

    That begs two questions:

    * **How do I prevent players from placing or breaking blocks?** Don't do anything. Don't change any flags! Remember, only members can build by default.
    * **How do I change a flag to only affect players?** You probably mean: how do you make a flag only affect *non-members*? Well, that's easy: use :ref:`region-groups`.

.. tip::
    Note: If the ``build`` flag is set to ``allow`` or ``deny``, it can still be overriden with a different flag (``block-break``, ``interact``, etc.). This is only the case with the build flag.

Mobs, Fire, and Explosions
~~~~~~~~~~~~~~~~~~~~~~~~~~

.. csv-table::
    :header: Flag, Type, description
    :widths: 10, 5, 30

    creeper-explosion,state,Whether creepers can do damage
    enderdragon-block-damage,state,Whether enderdragons can do block damage
    ghast-fireball,state,Whether ghast fireballs can do damage
    other-explosion,state,Whether explosions can do damage
    fire-spread,state,Whether fire can spread
    enderman-grief,state,Whether endermen will grief
    mob-damage,state,Whether mobs can hurt players
    mob-spawning,state,Whether mobs can spawn
    deny-spawn,set of entity types,A list of entity types that cannot spawn
    entity-painting-destroy,state,Whether non-player entities can destroy paintings
    entity-item-frame-destroy,state,Whether non-player entities can destroy item frames

.. topic:: Example: Preventing sheep and cows from spawning at spawn

    The entity types must be specified::

        /rg flag spawn deny-spawn cow,pig

Natural Events
~~~~~~~~~~~~~~

.. csv-table::
    :header: Flag, Type, description
    :widths: 10, 5, 30

    lava-fire,state,Whether lava can start fires
    lightning,state,Whether lightning can strike
    water-flow,state,Whether water can flow
    lava-flow,state,Whether lava can flow
    snow-fall,state,Whether snow will fall
    snow-melt,state,Whether snow will melt
    ice-form,state,Whether ice will form
    ice-melt,state,Whether ice will melt
    mushroom-growth,state,Whether mushrooms will grow
    leaf-decay,state,Whether leaves will decay
    grass-growth,state,Whether grass will grow
    mycelium-spread,state,Whether mycelium will spread
    vine-growth,state,Whether vines will grow
    soil-dry,state,Whether soil will dry

.. warning::
    The ``fire-spread``, ``water-flow`` and ``liquid-flow`` flags require that the "high frequency flags" option be enabled in the :doc:`configuration <../config>`. This is because these events can be very frequent, requiring more region lookups, and potentially slowing down your server (or at least warming the server room a bit more).

Map Making
~~~~~~~~~~

.. csv-table::
    :header: Flag, Type, description
    :widths: 10, 5, 30

    item-pickup,state,Whether items can be picked up
    item-drop,state,Whether items can be dropped
    exp-drops,state,Whether XP drops are permitted
    deny-message,string,The message issued to players that are denied an action
    entry,state,Whether players can enter the region
    exit,state,Whether players can exit the region
    greeting,string,The message that appears upon entering the region
    farewell,string,The message that appears upon leaving the region
    enderpearl,state,Whether enderpearls can be used
    invincible,state,Whether players are invincible
    game-mode,gamemode,"The gamemode (survival, creative, adventure) that will be applied to players that enter the region"
    time-lock,integer,"Time of day in ticks (between 0 and 24000) that players will see the world as while in the region. Use + or - for time relative to the world time."
    weather-lock,weather,Type of weather players will see when in the region. This does not affect world mechanics. Valid values are ``downfall`` and ``clear``.
    heal-delay,integer,The number of seconds between heals (if ``heal-amount`` is set)
    heal-amount,integer,The amount of half hearts to heal (...or hurt if negative) the player at the rate of ``heal-delay``
    heal-min-health,double,The minimum number of half hearts that damage (via ``heal-amount``) will not exceed
    heal-max-health,double,The maximum number of half hearts that healing (via ``heal-amount``) will not exceed
    feed-delay,integer,"See equivalent heal flag, except this is for food"
    feed-amount,integer,"See equivalent heal flag, except this is for food"
    feed-min-hunger,integer,"See equivalent heal flag, except this is for food"
    feed-max-hunger,integer,"See equivalent heal flag, except this is for food"
    teleport,location,The location to teleport to when the ``/rg teleport`` command is used within the region
    spawn,location,The location to teleport to when a player dies within the region
    blocked-cmds,set of strings,A list of commands to block
    allowed-cmds,set of strings,A list of commands to permit

.. warning::
    The healing, feeding, greeting, and farewell message flags require that the "use player move event" option **not** be disabled in the :doc:`configuration <../config>`.

.. topic:: Example: Changing the message players receive when an action they try is blocked
    
    Set the ``deny-message`` flag::

        /rg flag spawn deny-message Sorry! You are at spawn. If you want to find a place to call home, use the rail station to leave spawn.

.. topic:: Example: Blocking the "/tp" and "/teleport" commands at spawn
    
    The commands in question can be blocked with::

        /rg flag spawn blocked-cmds /tp,/teleport

.. topic:: Example: Preventing non-members of a "secret_club" region from entering it
    
    The key is to set the region group to "nonmembers"::

        /rg flag secret_club entry -g nonmembers deny

.. topic:: Example: In a "hospital" region, heal players one heart every second up to half their health bar
    
    Without any buffs, the player's maximum health is 20, so 10 is half of that::

        /rg flag hospital heal-amount 2
        /rg flag hospital heal-max-health 10

Miscellaneous
~~~~~~~~~~~~~

.. csv-table::
    :header: Flag, Type, description
    :widths: 10, 5, 30

    pistons,state,Whether pistons can be used
    send-chat,state,Whether players can send chat
    receive-chat,state,Whether players can receive chat
    potion-splash,state,Whether potions can have splash effects
    notify-enter,boolean,Whether players with the ``worldguard.notify`` permission are notified when another player enters the region
    notify-leave,boolean,Whether players with the ``worldguard.notify`` permission are notified when another player leaves the region

Unused
~~~~~~

.. csv-table::
    :header: Flag, Type, description
    :widths: 10, 5, 30

    allow-shop,state,"Not used by WorldGuard at this time, but third-party plugins may use it"
    buyable,boolean,"Not used by WorldGuard at this time, but third-party plugins may use it"
    price,double,"Not used by WorldGuard at this time, but third-party plugins may use it"
