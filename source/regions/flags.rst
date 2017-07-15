============
Region Flags
============

每个区域都可以有不同的flag设置，其中一些flag的用途包括：

* 用 ``pvp`` 来阻止玩家之间的战斗
* 用 ``entry`` 来阻止其他玩家进入你的领地（区域）
* 用 ``snow-melt`` 来禁用雪融化
* 用 ``receive-chat`` 阻止某区域内的玩家接收消息
* 用 ``vine-growth`` 停止藤曼的生长

一个区域可以有很多个不同的flag被设置，但是每个flag只能设置为一个确定的值。用 ``/region flag`` 命令来设置区域的flag，以下是一些例子： ::

    /region flag spawn pvp deny
    /region flag spawn greeting Welcome to spawn!
    /region flag hospital heal-amount 2

不给flag指定任何值可以把flag恢复成默认值： ::

    /region flag spawn pvp

用"info"命令来列出已设置的flag： ::

    /region info spawn

.. 组:

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

当设置flag时，使用 ``-g`` 参数来指定组，例如 ::

    /region flag spawn -g nonmembers pvp deny

你 **不能** 在同一个区域内为不同的组的同一个flag设置不同的值，如果你想要那样设置，可以考虑创建多个子域。

.. note::
    When there are multiple overlapping regions, a player must be a member of the region *on which the flag is set* or *on one of the region's child regions* (when region inheritance is involved). This is explained further in :doc:`priorities`.

.. tip::
    *entry* 和 *exit* 两个flag的默认组是 "non-member"，也就是说，当你把这两个flag设置为 "deny" 的时候它将会阻止非区域成员进入/离开区域。 *teleport* 和 *spawn* 两个flag的默认组是 "members"，也就是说，只有区域成员能使用他们。其余的flag默认组都是 "everyone"。


flag 的类型
==============

每一个flag都有特定的类型来规定它能取什么样的值。比如说， *heal-amount* 这个flag是一个只能填入数值的flag。

.. csv-table::
    :header: 类型, 描述
    :widths: 5, 30

    state, "要么是 ``allow`` 要么是 ``deny`` "
    string, "字符串，也可以理解为一句话"
    integer, "整数值"
    double, "浮点数（也就是小数或者整数，比如4.5、2.0、3.14159等等"
    location, "某个世界内的某个坐标"
    boolean, "要么是 ``True`` 要么是 ``false`` "
    set, "一个列表（或者说，词典）"

实际上，还有很多种类型，但是使用除了上面表格内列出的类型之外的类型可能导致插件无法正常运作。

flag 的冲突
=================

有时候，一个位置可能会有几个重叠的域，每个域对同一个flag有不同的设置，这就是flag冲突。以下的规则可以用来帮你确定插件将会取的值：

* 当子域的flag未被设置，且其父域对应的flag有设置，那么子域将从其父域那里继承这个flag的值；
* 高优先级的域的设置将会覆盖低优先级的域的设置；
* 全局域拥有最低的优先级，除此之外，他和普通域没什么区别。

然而，即使有这些规则，flag冲突依然有可能会在两个重叠区域拥有同等优先级时发生。此时某些特定类型的flag可以拥有确定的取值：

* For state flags, if ``deny`` is present, the result is ``deny``. Otherwise, if ``allow`` is present, then the final value is ``allow``.
* 对于其他的flag类型，冲突的结果是不确定的，所以在设置flag的时候最好头脑清楚， 如果太困了就去洗把脸再来配置，或者先睡一觉。如果对同一个地点用两个同等优先级的域设置两个不同的欢迎语，结果就会如上文所说， **无法预料** 。

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
