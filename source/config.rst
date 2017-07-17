=============
配置
=============

很多的 WorldGuard 的功能都可以通过配置文档来开关（比如禁用药水、开启南瓜水肺等等），所有可用的配置选项都将在这一个页面中列出。

配置文件
===================

一旦你将 WorldGuard 加载之后，在 **./plugins/WorldGuard** 路径下就会生成一个默认配置文件，文件名是：

* ``config.yml``

并且每个世界都有自己独立的一个配置文件，类似于：

* ``worlds/world/config.yml``
* ``worlds/world_nether/config.yml``
* ``worlds/mining_world/config.yml``

如果你打开世界的独立配置文件，你会发现他们几乎都是空的，如果你想要对某些世界做一些特别的设置，你可以从总配置文档里把你想要更改的条目复制粘贴到独立配置文件中，然后再进行更改。

.. topic:: 例：做一个世界的独立设置

    假设在全局设置里，你将 ``block-creeper-block-damage`` 设为 ``true`` ::

        mobs:
            block-creeper-explosions: false
            block-creeper-block-damage: true
            block-wither-explosions: false

    但你希望它在地狱（下界）里为 ``false``，你可以打开 ``worlds/worlds_nether/config.yml`` 并将里面的内容替换为::

        mobs:
            block-creeper-block-damage: false

    你只需要复制关键行和它的父字段（此例中为 ``block-creeper-block-damage`` 这一行与开头的 ``mobs:``

设置项
========

.. note::
    
    此处列出的设置项仅供参考，你依然需要在全局配置文件里找到他们（可以尝试使用Ctrl+F快捷键，在大多数的编辑器里它都代表查找命令；在nano中这个命令的快捷键为^W），因为有些设置是嵌套于其他设置里的，如果被打乱顺序，则配置文件可能会出错。

.. csv-table::
    :header: Setting, Default, 描述
    :widths: 12, 5, 30

    op-permissions,TRUE,"是否给予op所有权限，即使权限管理插件没有给op任何权限"
    summary-on-start,TRUE,"当服务器启动时是否列出每个世界的WG的设定？当你有很多个世界的时候建议关闭"
    auto-invincible,FALSE,"当有 ``worldguard.auto-invincible`` 权限的玩家加入游戏的时候自动为其开启无敌模式"
    auto-invincible-group,FALSE,"当有 ``wg-invincible`` 权限组权限的玩家加入游戏的时候自动为其开启无敌模式"
    auto-no-drowning-group,FALSE,"当有 ``wg-amphibious`` 权限组权限的玩家加入游戏的时候为其开启水下呼吸模式"
    use-player-move-event,TRUE,"是否允许 WorldGuard 多占用一丢丢CPU时间来追踪玩家的位置，若要开启healing、feeding、greeting 等flag时必须开启，见 `flags <regions/flags.rst>`_"
    use-player-teleports,TRUE,"追踪玩家位置时是否追踪传送事件，有时玩家传送时不会产生传送事件，我也不知道为什么，这个现象是随机的"
    host-keys,,开启安全模式，详见 `host_keys <host-keys>`_.

security.*
~~~~~~~~~~

.. csv-table::
    :header: Setting, Default, Description
    :widths: 12, 5, 30

    deop-everyone-on-join,FALSE,Clear op status from all players that join.
    block-in-game-op-command,FALSE,Block the /op command from being used in-game.

build-permission-nodes.*
~~~~~~~~~~~~~~~~~~~~~~~~

.. csv-table::
    :header: Setting, Default, Description
    :widths: 12, 5, 30

    enable,FALSE,A feature that lets you block building based on giving players the proper permissions. See :doc:`build-perms`.
    deny-message,,"Concerning build permissions, this is the message that is sent when permission is denied. If a message is not set, a default one is used."

event-handling.*
~~~~~~~~~~~~~~~~

.. csv-table::
    :header: Setting, Default, Description
    :widths: 12, 5, 30

    block-entity-spawns-with-untraceable-cause,FALSE,"As Bukkit does not always tell plugins the exact reason that an entity was spawned, it may be possible for a player to bypass protection to spawn an entity (such as with a spawn egg). This option blocks cases where the true cause cannot be determined. It is recommended that this option is left off because the number of cases where the cause is not known is quite large."
    interaction-whitelist,[],"A list of block types that should not be protected. For example, if chests were added to this list, then they would never be protected with :doc:`region protection <regions/index>`. This setting is useful primarily when non-vanilla functionality is present (game features added by other plugins or mods) and you don't want it blocked."
    emit-block-use-at-feet,[],"A list of items that, if used, will also require that the player have the permission to *theoretically* modify the block at his or her feet. This setting is useful primarily when there is some item from some plugin or mod that uses a projectile (that affects the world) but does not test permission with WorldGuard. However, this is not a proper solution because the player can still stand in an area where he or she has permission and shoot *into* the desired area."

.. topic:: Example: Disabling protection on workbenches

    The ``interaction-whitelist`` option can be used::

        interaction-whitelist: [workbench]

protection.*
~~~~~~~~~~~~

.. csv-table::
    :header: Setting, Default, Description
    :widths: 12, 5, 30

    item-durability,TRUE,"Set to ``false`` to have items never break."
    remove-infinite-stacks,FALSE,"Remove items with 'infinite' stack sizes, which is essentially any stack size less than 0 (which is the result of a number overflowing the maximum and wrapping into the negatives)."
    disable-xp-orb-drops,FALSE,"Whether to disable XP orb drops."
    disable-obsidian-generators,FALSE,"Whether to disable obsidian generators."

gameplay.*
~~~~~~~~~~

.. csv-table::
    :header: Setting, Default, Description
    :widths: 12, 5, 30

    block-potions,[],A list of potion types that cannot be used. The list of possible potion types can be `found in Bukkit <http://jd.bukkit.org/rb/apidocs/org/bukkit/potion/PotionEffectType.html>`_.
    block-potions-overly-reliably,FALSE,Whether WorldGuard should try extra hard to block the list of potions mentioned in `block-potions`. This is generally not needed and enabling this may block more than you want.

.. topic:: Example: Blocking the use of night vision and speed potions

    The names `found in Bukkit <http://jd.bukkit.org/rb/apidocs/org/bukkit/potion/PotionEffectType.html>`_ are used::

        block-potions: [night_vision, speed]

simulation.sponge.*
~~~~~~~~~~~~~~~~~~~

.. csv-table::
    :header: Setting, Default, Description
    :widths: 12, 5, 30

    enable,FALSE,"Whether to simulate sponge blocks similar to the way they worked in Minecraft Classic. Between the introduction of survival Minecraft and Minecraft 1.8 (several years), sponges did not work, but this setting is now obsolete and its use is no longer recommended."
    radius,3,The radius of the sponge's action.
    redstone,FALSE,Whether Redstone can control the simulated Sponge blocks.

default.*
~~~~~~~~~

.. csv-table::
    :header: Setting, Default, Description
    :widths: 12, 5, 30

    pumpkin-scuba,FALSE,Whether players with pumpkins (but not Jack o' Lanterns) in their helmet slot will have water breathing.
    disable-health-regain,FALSE,Whether automatic health regeneration should be disabled.

physics.*
~~~~~~~~~

.. csv-table::
    :header: Setting, Default, Description
    :widths: 12, 5, 30

    no-physics-gravel,FALSE,"Whether gravel should not fall."
    no-physics-sand,FALSE,"Whether sand should not fall."
    vine-like-rope-ladders,FALSE,"Whether ladders will work like vines in that they won't break as long as the top most ladder block is in a valid location (it's on a wall)."
    allow-portal-anywhere,FALSE,"Whether portal blocks can be placed in invalid locations."
    disable-water-damage-blocks,[],"A list of block types that will not be broken by water."

.. topic:: Example: Preventing Redstone and Redstone torches from being damanged by water

    Material names are from `Bukkit's Material list <http://jd.bukkit.org/rb/apidocs/org/bukkit/Material.html>`_::

        physics:
            disable-water-damage-blocks: [redstone_wire, redstone_torch]

ignition.*
~~~~~~~~~~

.. csv-table::
    :header: Setting, Default, Description
    :widths: 12, 5, 30

    block-tnt,FALSE,Whether the detonation of TNT should be blocked.
    block-tnt-block-damage,FALSE,Whether TNT should do no block damage.
    block-lighter,FALSE,Whether the use of flint and steel should be disabled.

fire.*
~~~~~~

.. csv-table::
    :header: Setting, Default, Description
    :widths: 12, 5, 30

    disable-lava-fire-spread,TRUE,Whether lava should be able to create fires.
    disable-all-fire-spread,FALSE,Whether fire can spread.
    disable-fire-spread-blocks,[],"A list of block types that fire cannot spread to, or at least damage."
    lava-spread-blocks,[],"If set (as a list of block types), the only blocks on which lava could flow on (other than air) would be the ones in the list."

mobs.*
~~~~~~

.. csv-table::
    :header: Setting, Default, Description
    :widths: 12, 5, 30

    block-creeper-explosions,FALSE,"Whether the effects of Creeper explosions should be disabled."
    block-creeper-block-damage,FALSE,"Whether block damage caused by Creeper explosions should be disabled."
    block-wither-explosions,FALSE,"Whether the effects of Wither explosions should be disabled."
    block-wither-block-damage,FALSE,"Whether block damage caused by Wither explosions should be disabled."
    block-wither-skull-explosions,FALSE,"Whether the effects of Wither skulls should be disabled."
    block-wither-skull-block-damage,FALSE,"Whether block damage caused by Wither skulls should be disabled."
    block-enderdragon-block-damage,FALSE,"Whether block damage caused by Enderdragons should be disabled."
    block-enderdragon-portal-creation,FALSE,"Whether the ability of the Enderdragon to create a portal should be disabed."
    block-fireball-explosions,FALSE,"Whether the effects of fireball explosions should be disabled."
    block-fireball-block-damage,FALSE,"Whether block damage caused by fireball block damage should be disabled."
    anti-wolf-dumbness,FALSE,"Whether the wolf should be invincible in a number of situations, including, but not limited to, walking into lava and getting stuck. WHen wolves were first introduced into the game, Minecraft had very poor path finding and so wolves would frequently walk into lava or fire. However, as the AI of helper mobs in Minecraft have still much to improve, this setting may still prove to be useful."
    allow-tamed-spawns,TRUE,"Whether tamable mobs (wolves, horses, cats, etc.) should be spawnable."
    disable-enderman-griefing,FALSE,"Whether the ability of Endermen to pick up and place blocks should be disabled."
    disable-snowman-trails,FALSE,"Whether the feature of snowmen placing snow trails should be disabled."
    block-painting-destroy,FALSE,"Whether the ability of mobs to break paintings should be disabled."
    block-item-frame-destroy,FALSE,"Whether the ability of mobs to item frames should be disabled."
    block-plugin-spawning,TRUE,"Whether mobs spawned by plugins should be blocked when needed to apply some of these configuration options or to protect areas of the world."
    block-above-ground-slimes,FALSE,"Whether slimes spawning above ground should be disabled."
    block-other-explosions,FALSE,"Whether miscellaneous explosions should be disabled."
    block-zombie-door-destruction,FALSE,"Whether the ability for zombies to break doors should be disabled."
    block-creature-spawn,[],"A list of entity types that should not spawn."

player-damage.*
~~~~~~~~~~~~~~~

.. csv-table::
    :header: Setting, Default, Description
    :widths: 12, 5, 30

    disable-fall-damage,FALSE,"Whether fall damage should be disabled for players."
    disable-lava-damage,FALSE,"Whether lava damage should be disabled for players."
    disable-fire-damage,FALSE,"Whether fire damage should be disabled for players."
    disable-lightning-damage,FALSE,"Whether lightning damage should be disabled for players."
    disable-drowning-damage,FALSE,"Whether drowning damage should be disabled for players."
    disable-suffocation-damage,FALSE,"Whether suffocation damage should be disabled for players."
    disable-contact-damage,FALSE,"Whether contact damage (cacti, etc.) should be disabled for players."
    teleport-on-suffocation,FALSE,"Whether players should be teleported to a safe location (if found, and which is usually up) when they are suffocating. This feature potentially allows players to cross vertical barriers."
    disable-void-damage,FALSE,"Whether void damage (falling into the bottom of the world) should be disabled for players."
    teleport-on-void-falling,FALSE,"Whether players should be teleported to a safe location (if found) when they are falling into the void. This feature potentially allows players to enter areas that they normally may not be able to go."
    disable-explosion-damage,FALSE,"Whether explosion damage should be disabled for players."
    disable-mob-damage,FALSE,"Whether damage from mobs should be disabled for players."
    disable-death-messages,FALSE,"Whether death messages for players should be disabled."
    
crops.*
~~~~~~~

.. csv-table::
    :header: Setting, Default, Description
    :widths: 12, 5, 30

    disable-creature-trampling,FALSE,"Disable the trampling of crops by creatures."
    disable-player-trampling,FALSE,"Disable the trampling of crops by players."

weather.*
~~~~~~~~~

.. csv-table::
    :header: Setting, Default, Description
    :widths: 12, 5, 30

    prevent-lightning-strike-blocks,[],A list of block types where lightning should not be able to strike.
    disable-lightning-strike-fire,FALSE,"Whether fire caused by lightning should be blocked."
    disable-thunderstorm,FALSE,"Whether thunderstorms should never start."
    disable-weather,FALSE,"Whether weather events (including thunderstorms) should never start."
    disable-pig-zombification,FALSE,Whether the "zombification" of pigs when they are struck by lightning should be disabled.
    disable-powered-creepers,FALSE,Whether the possibility of creepers becoming powered when they are struck by lightning should be disabled.
    always-raining,FALSE,"Whether it should always be raining or snowing."
    always-thundering,FALSE,"Whether it should always be thundering."

dynamics.*
~~~~~~~~~~

.. csv-table::
    :header: Setting, Default, Description
    :widths: 12, 5, 30

    disable-mushroom-spread,FALSE,"Whether the spread of mushrooms should be disabled."
    disable-ice-melting,FALSE,"Whether the melting of ice should be disabled."
    disable-snow-melting,FALSE,"Whether the melting of snow should be disabled."
    disable-snow-formation,FALSE,"Whether the formation of snow should be disabled."
    disable-ice-formation,FALSE,"Whether the formation of ice should be disabled."
    disable-leaf-decay,FALSE,"Whether the decay of leaves should be disabled."
    disable-grass-growth,FALSE,"Whether the growth of grass should be disabled."
    disable-mycelium-spread,FALSE,"Whether the spread of mycelium should be disabled."
    disable-vine-growth,FALSE,"Whether the growth of vines should be disabled."
    disable-soil-dehydration,FALSE,"Whether the dehydration of soil should be disabled."
    snow-fall-blocks,[],"If set (as a list of block types), the only blocks on which snow can fall on would be the ones in the list."

chest-protection.*
~~~~~~~~~~~~~~~~~~

.. csv-table::
    :header: Setting, Default, Description
    :widths: 12, 5, 30

    enable,FALSE,"Enables :doc:`chest-protection`."
    disable-off-check,FALSE,"Even if chest protection is off, WorldGuard will block the creation of signs with ``[Lock]`` on them so that if chest protection is later enabled, players cannot have preemptively lock chests that they did not own to begin with. This option, if set to ``true``, disables this check when chest protection is off."

blacklist.*
~~~~~~~~~~~

.. csv-table::
    :header: Setting, Default, Description
    :widths: 12, 5, 30

    use-as-whitelist,FALSE,"Whether the purpose of the blacklist should be inverted, where the only things that can be done are the ones that are denied on the blacklist."

blacklist.logging.*
~~~~~~~~~~~~~~~~~~~

These settings determine what the "log" action in the :doc:`blacklist/index` does. Several logging targets can be enabled simultaneously.

.. csv-table::
    :header: Setting, Default, Description
    :widths: 8, 15, 15

    console:,
    "    enable",TRUE,Whether logging to the console should be enabled.
    database:,
    "    enable",FALSE,Whether logging to a database should be enabled.
    "    dsn",jdbc:mysql://localhost:3306/minecraft,The connection string for the database. ``minecraft`` in the default is the name of the database.
    "    user",root,The username to connect to the database with.
    "    pass",,The password to connect to the database with.
    "    table",blacklist_events,The table to use.
    file:,
    "    enable",FALSE,Whether logging to files should be enabled.
    "    path",worldguard/logs/%Y-%m-%d.log,The pattern for the log files.
    "    open-files",10,The maximum number of file handles to have open at once (file handles with the operating system).


regions.*
~~~~~~~~~

.. hint::
    You cannot override ``use-scheduler`` nor ``use-creature-spawn-event`` per-world.

.. csv-table::
    :header: Setting, Default, Description
    :widths: 18, 5, 26

    wand,334,"The ID of the item that is used to right click a block to inspect the regions affecting it. By default, this item is cow leather. Before, it was string but Minecraft added a use for string."
    invincibility-removes-mobs,FALSE,"If enabled, if a player is attacked while he or she is invincible due to the invincibility :doc:`region flag <regions/flags>`, then the attacking mob is removed from the world."
    fake-player-build-override,TRUE,"Whether players with a name that start with ``[`` and end with ``]`` should bypass all protection. This is only the case with third-party plugins and mods that use 'fake players' in lieu of firing the proper events."
    explosion-flags-block-entity-damage,TRUE,"Whether the various ``-explosion`` :doc:`region flags <regions/flags>` should also disable entity damage when enabled."
    high-frequency-flags,FALSE,"Whether to handle frequently occurring events such as fire spread or fluid flow in regards to :doc:`region flags <regions/flags>` (such as ``fire-spread``, ``water-flow``, and ``lava-flow``). This is disabled by default because those mentioned flags are rarely used and this setting may result in a performance impact in certain scenarios (many, many players and many, many regions)."
    protect-against-liquid-flow,FALSE,"Whether fluid flows between regions should be blocked. This prevents one player from griefing another by having lava or water flow into the other player's region. This setting must be enabled in tandem with ``high-frequency-flags`` for it to take effect."
    max-claim-volume,30000,"The maximum number of blocks in a region that can be claimed with :doc:`self-serve region claiming <regions/claiming>`."
    claim-only-inside-existing-regions,FALSE,"Whether players can only claim within existing regions."
    max-region-count-per-player:,,"The maximum number of regions that can be claimed by a player (via :doc:`self-serve region claiming <regions/claiming>`). This setting can differ per permission-group if new entries are added below (like for 'default'). 'default' is the default limit. If a player is a member of several groups that are listed, then the player receives the highest limit."
    "    default",7,(See above.)

.. topic:: Example: Setting different region count limits per group

    Each permission group is given its own entry::

        max-region-count-per-player:
            default: 7
            builders: 20
            moderators: 40

.. warning::
    There are also some additional settings in this category, but we do not recommend changing them:

    .. csv-table::
        :header: Setting, Default, Description
        :widths: 18, 5, 26

        enable,TRUE,"Whether support for defining regions should be enabled."
        use-scheduler,TRUE,"Whether to use a scheduler, which is critical to many WorldGuard's functions (heal, feed, and game mode :doc:`region flags <regions/flags>`, for example). Disabling it is not advised."
        use-creature-spawn-event,TRUE,"Whether the creature move event should be handled for applying some mob-related :doc:`region flags <regions/flags>`."

regions.uuid-migration.*
~~~~~~~~~~~~~~~~~~~~~~~~

These settings are used to migrate from old versions of WorldGuard and Minecraft. In the past, Minecraft accounts were identified purely by their name, but around the end of Minecraft 1.7, Minecraft moved to a system where players were (internally) identified by "UUIDs" and players *could change their names*. However, all older configuration files still referred to players by their name, so the following settings enable the conversion to UUID on server start for one time (the setting is automatically disabled).

UUID migration can be run repeatedly (with repeated changing of the setting) and it will only convert names that have not yet been converted to UUIDs. If there are no names to convert, then nothing will happen.

.. hint::
    You cannot override these settings per-world.

.. csv-table::
    :header: Setting, Default, Description
    :widths: 12, 5, 30

    perform-on-next-start,FALSE,"Whether UUID migration should be performed on next server start (once). If the configuration file does not yet have this setting, WorldGuard will assume its value is ``true``, perform the conversion, then set the setting to ``false``."
    keep-names-that-lack-uuids,TRUE,"Sometimes, a name that was added to a region does not actually exist. This option keeps names that can't be converted in the region data so that they can later be removed or re-converted (by adjusting the settings in this section to re-run the conversion)."

regions.sql.*
~~~~~~~~~~~~~

.. hint::
    You cannot override these settings per-world.

.. csv-table::
    :header: Setting, Default, Description
    :widths: 8, 15, 15

    use,FALSE,Whether MySQL should be used to store data (see :doc:`regions/storage`).
    dsn,jdbc:mysql://localhost/worldguard,The connection string for the database. ``worldguard`` in the default is the name of the database.
    username,worldguard,The username to connect to the database with.
    password,worldguard,The password to connect to the database with.
    table-prefix,,The table prefix.

Subpages
========
 
.. toctree::
    :maxdepth: 3
    :titlesonly:

    host-keys
