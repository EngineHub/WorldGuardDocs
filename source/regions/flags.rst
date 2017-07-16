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

.. _组: flags.rst#组

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
    double, "浮点数（也就是小数或者整数，比如4.5、2.0、3.14159等等）"
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

如果某个flag为默认设置，则它的值是最符合常理的那个值，比如，当"item pickup"取默认值的时候，它的值为 *允许* 。

Flag 列表
============

列表将被分类。

Overrides
~~~~~~~~~

.. csv-table::
    :header: Flag, Type, description
    :widths: 10, 5, 30

    passthrough,state,"这个flag是 *passthrough build* 的缩写，与能不能进入某区域没有关系。

    * 默认值等同于 ``deny``；
    * 如果设置为 ``deny``，则区域保护开启，区域内的方块不能被更改；
    * 如果设置为 ``allow``，则区域保护关闭。

    这个flag拿来有什么用？

    * 当你在同时使用其他flag（比如PvP、healing之类的）并且不希望区域保护开启的时候就应该把 ``passthrough`` 设为 ``allow`` 
    
    为什么要用 passthrough 而不是把 ``build`` 设为 ``deny``？
    
    * 因为这样会覆盖其子域的值并且允许玩家建造！（待会会解释为什么）"

有关保护的flag
~~~~~~~~~~~~~~~~~~

.. csv-table::
    :header: Flag, Type, description
    :widths: 10, 5, 30

    build,state,"这是总开关，可以控制:

    * 方块能不能被破坏或者放置
    * 门、拉杆、压力板等等物品能否被使用
    * 实体或者方块能否与你互动（比如骑马、和村民交易等）
    * 是否允许PvP
    * 是否能在床上睡觉
    * 是否能开启箱子（包括箱子、陷阱箱、末影箱）
    * 是否能放置载具（船、矿车）
    * 等等"
    interact,state,"包括所有“使用”物品或实体的选项:

    * 门、拉杆、压力板等等物品能否被使用
    * 是否能开启箱子
    * 是否能使用载具（包括动物）
    * 等等"
    block-break,state,是否能破坏方块
    block-place,state,是否能放置方块
    use,state,"是否能使用门、拉杆等物品（不包括箱子）"
    damage-animals,state,"玩家是否能伤害友善的动物"
    chest-access,state,箱子是否能开启
    ride,state,"是否能使用载具（包括动物）"
    pvp,state,"是否允许PvP"
    sleep,state,是否允许使用床
    tnt,state,是否允许使用TNT
    vehicle-place,state,"载具是否能被放置（比如矿车、船）"
    vehicle-destroy,state,是否能破坏载具
    lighter,state,是否能使用打火石

.. warning::
    这个分类的所有flag都不只对玩家有效，举个例子，``block-break`` 这个flag如果被设置成 ``deny``，那么就连活塞也无法破坏方块！

    想一想，坏蛋可以把一个TNT用红石大炮从领域的外面抛到领域内来破坏你的建筑，就算你禁止了TNT爆炸，坏蛋依然可以造一台活塞蠕虫（指的是能自己推动自己向前走的机器，以活塞和红石为主要元件）来破坏你领域内的建筑。要想追踪到是哪个玩家造了这个TNT大炮是十分困难的。

    彻底禁止TNT爆炸或者彻底禁止活塞的工作并不是一个解决办法，或者甚至可以说是“懒政”“一刀切”之类的。红石和TNT在许多情况下是非常有用的工具，有时你甚至希望在你的领域内放置一个红石活塞门，这就要求活塞能在领域内工作。

    首先，记住谁能在领域内建造：**并不是** 玩家，而是 **成员**！成员与非成员放置的TNT或者活塞并无区别，那么 WorldGuard 是怎么区分它是成员建造的还是非成员建造的呢？Bingo~ **成员能在领域内建造，非成员不能！**
    
    当你创建一个新的区域而还没有给它设置任何flag的时候：

    * 领域成员可以建造
    * 非领域成员 **无法** 建造

    在领域内部的TNT大炮和活塞应该要能正常工作，因为它们是“领域成员”。
    现在想象一下一个新的玩家，Bobby。Bobby还不是领域的成员，所以他无法在领域内放置或摧毁任何方块，当你让他成为了领域的成员之后，他就拥有了建造的权限。
    
    当你设置了这个分类下的flag之后，这些flag会覆盖掉这一个默认值，就比方说当你把 ``block-break`` 设置成了 ``deny`` ，那么就连领域的成员也无法在领域内拆除什么东西了，也就是说，Bobby没办法破坏方块、TNT没办法破坏方块、活塞也无法破坏方块，换句话说，**活塞就没法正常工作了**。

    这就带来了两个问题：
    
    * **我要如何阻止玩家破坏我领域内的方块？** 什么都不要做，创建了你的领域之后，保持所有的flag都是默认值。
    * **我要怎么设置才能让这些flag只对玩家有效？** 你的问题应该是：“我要怎么设置才能让这些flag只对 **非领域成员** 有效？”嗯……这其实很简单啦，你可以回头去看看 组_。

.. tip::
    
    提示：即使 ``build`` 设置为 ``allow`` 或者 ``deny``，它依然会被这个分类下的其他flag覆盖（比如 ``block-break``、``interact``）。
    
怪物、火焰和爆炸
~~~~~~~~~~~~~~~~~~~~~~~~~~

.. csv-table::
    :header: Flag, Type, description
    :widths: 10, 5, 30

    creeper-explosion,state,苦力怕爆炸是否破坏方块
    enderdragon-block-damage,state,末影龙是否能破坏方块
    ghast-fireball,state,恶魂是否能破坏方块
    other-explosion,state,爆炸是否能破坏方块
    fire-spread,state,火焰是否会扩散
    enderman-grief,state,末影人是否会偷方块
    mob-damage,state,怪物是否会伤害玩家
    mob-spawning,state,怪物是否会生成
    deny-spawn,set of entity types,一个被阻止生成的怪物的列表
    entity-painting-destroy,state,Whether non-player entities can destroy paintings
    entity-item-frame-destroy,state,Whether non-player entities can destroy item frames

.. topic:: 例：在出生点阻止牛和猪生成

    需要指定实体的列表 ::

        /rg flag spawn deny-spawn cow,pig

自然事件
~~~~~~~~~~~~~~

.. csv-table::
    :header: Flag, Type, description
    :widths: 10, 5, 30

    lava-fire,state,岩浆是否能引起火焰
    lightning,state,闪电是否能击中区域内方块
    water-flow,state,水是否能流动
    lava-flow,state,岩浆是否能流动
    snow-fall,state,是否会下雪
    snow-melt,state,雪是否会融化
    ice-form,state,是否会结冰
    ice-melt,state,冰是否会融化
    mushroom-growth,state,蘑菇是否会长大
    leaf-decay,state,树叶是否会枯萎
    grass-growth,state,草是否会长出来
    mycelium-spread,state,蘑菇是否会扩散
    vine-growth,state,藤曼是否会成长 
    soil-dry,state,土壤是否会变干

.. warning::
    ``fire-spread``，``water-flow`` 和 ``lava-flow`` 这三个flag需要打开 `configuration <../config.rst>`_ 里的 "high frequency flags" 选项，因为这三个事件可能会更新得非常频繁，并且需要更多的region查询，可能会导致服务器卡顿（至少会使你的服务器机房变得更热）。
    
Map Making
~~~~~~~~~~

.. csv-table::
    :header: Flag, Type, description
    :widths: 10, 5, 30

    item-pickup,state,掉落物是否能被捡起
    item-drop,state,物品是否能被丢弃
    exp-drops,state,是否掉落经验
    deny-message,string,当拒绝玩家的某个操作时给玩家提示的消息
    entry,state,玩家是否能进入此区域
    exit,state,玩家是否能离开此区域
    greeting,string,当玩家进入区域时提示的消息
    farewell,string,当玩家离开区域时提示的消息
    enderpearl,state,是否能使用末影珍珠
    invincible,state,玩家是否无敌
    game-mode,gamemode,"当玩家进入区域时应用的游戏模式 (survival, creative, adventure)"
    time-lock,integer,"玩家在region内看到的游戏时间，用tick来表示 (between 0 and 24000)；或者用“+”或者“-”来调整为相对于region外的时间调快/慢多少tick"
    weather-lock,weather,"玩家在区域内看到的天气。这不会影响区域外的天气。有效的值有 ``downfall`` （下雨）和 ``clear`` （晴天）"
    heal-delay,integer,"两次回血之间间隔的时间(假如设置了 ``heal-amount`` 的话)"
    heal-amount,integer,"每次回血恢复多少个“半格” (...如果设置为负数则会掉血)"
    heal-min-health,double,"血量少于多少个“半格”之后不再掉血（ ``heal-amount`` 为负数时）"
    heal-max-health,double,"血量大于多少个“半格”之后不再回血（ ``heal-amount`` 为正数时）"
    feed-delay,integer,"与 ``heal-*`` 类似，只不过这里是恢复饥饿值"
    feed-amount,integer,"同上"
    feed-min-hunger,integer,"同上"
    feed-max-hunger,integer,"同上"
    teleport,location,"当玩家使用 ``/rg teleport`` 的时候将玩家传送到此区域的什么位置"
    spawn,location,"当玩家在区域内死亡并且复活之后将玩家传送到此区域的什么位置"
    blocked-cmds,set of strings,"一个在区域内不允许使用的命令的列表"
    allowed-cmds,set of strings,"一个在区域内允许使用的命令的列表"

.. warning::
    *healing、feeding、greeting、farewell* 四个flag需要启用 `configuration <../config.rst>`_ 里的"use player move event"（默认为启用）。

.. topic:: 例：改变一个玩家的操作被禁止时收到的消息： ::

        /rg flag spawn deny-message Sorry! You are at spawn. If you want to find a place to call home, use the rail station to leave spawn.

.. topic:: 例：在出生点禁用 "/tp" 和 "/teleport" 两个命令： ::

        /rg flag spawn blocked-cmds /tp,/teleport

.. topic:: 例：禁止非成员进入 "secret_club" 区域：
    
    关键是设置 region group 为 "nonmembers"::

        /rg flag secret_club entry -g nonmembers deny

.. topic:: 在一个“医院”区域内，每秒给玩家恢复一颗心的血量，直到达到玩家最大血量的一半：
    
    没有任何加成的时候，玩家的最大血量为20，它的一半就是10 ::

        /rg flag hospital heal-amount 2
        /rg flag hospital heal-max-health 10

其他的
~~~~~~~~~~~~~

.. csv-table::
    :header: Flag, Type, description
    :widths: 10, 5, 30

    pistons,state,活塞是否能使用
    send-chat,state,玩家是否能发送消息
    receive-chat,state,玩家是否能接收消息
    potion-splash,state,药水是否有喷溅效果
    notify-enter,boolean,"当有其他玩家进入你的领域的时候是否给你发送提醒（需要 ``worldguard.notify`` 权限）"
    notify-leave,boolean,"当有其他玩家离开你的领域的时候是否给你发送提醒（需要 ``worldguard.notify`` 权限）"

不再使用的
~~~~~~~~~~~~~~~~

.. csv-table::
    :header: Flag, Type, description
    :widths: 10, 5, 30

    allow-shop,state,"WorldGuard 已经不再使用这个flag，但是其他的插件可能需要使用"
    buyable,boolean,"WorldGuard 已经不再使用这个flag，但是其他的插件可能需要使用"
    price,double,"WorldGuard 已经不再使用这个flag，但是其他的插件可能需要使用"
