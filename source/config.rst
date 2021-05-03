=============
Configuration
=============

Many of WorldGuard's features exist as configuration options (potion blocking, scuba mode with pumpkins, etc.). Every configuration option available is listed on this page.

Configuration Files
===================

Once you have run your server with WorldGuard installed, you will find the main configuration file inside the **plugins/WorldGuard** folder:

* ``config.yml``

Then for every world, you will find per-world configuration files:

* ``worlds/world/config.yml``
* ``worlds/world_nether/config.yml``
* ``worlds/mining_world/config.yml``

If you open up the per-world configuration files, they will be nearly empty. When you wish to override a setting, you would copy it into the world's configuration file.

.. topic:: Example: Making a configuration option per-world

    In the main configuration file, you may have set ``block-creeper-block-damage`` to true::

        mobs:
            block-creeper-explosions: false
            block-creeper-block-damage: true
            block-wither-explosions: false

    But you want to set it to ``false`` in your nether world. Open up ``worlds/world_nether/config.yml`` and replace the file with::

        mobs:
            block-creeper-block-damage: false

    Only the relevant line, as well as any parent sections, needs to be copied over.


Settings
========

.. note::
    These options are presented here as a reference, but you should change a desired option by first finding it in ``config.yml`` because some settings may need to be nested under another setting.

.. csv-table::
    :header: Setting, Default, Description
    :widths: 12, 5, 30

    op-permissions,TRUE,"Whether players with op should be given all permissions for WorldGuard, even if the permissions plugin in use does not provide permission for ops."
    summary-on-start,TRUE,Show summary information about WG's settings for each world on server start. This is pretty noisy and it should be disabled if you have many worlds.
    auto-invincible,FALSE,Give players with the ``worldguard.auto-invincible`` permission invincibility mode on join.
    auto-invincible-group,FALSE,Give players in the ``wg-invincible`` permission group invincibility mode on join.
    auto-no-drowning-group,FALSE,Give players in the ``wg-amphibious`` permission group water breathing mode on join.
    use-player-move-event,TRUE,"Whether WorldGuard should use (a little) more CPU to handle features that require tracking player movement. This must be on to use healing, feeding, greeting, and some other :doc:`regions/flags`."
    use-player-teleports,TRUE,Whether teleport events should be considered when tracking player movement. This should always be on if you are using the player move event and any of the flags that require that.
    host-keys,,A list of hostnames that players must connect from. See :doc:`host-keys`.

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
    ignore-hopper-item-move-events,FALSE,"Disable protections relating to hoppers moving items to and from containers. This can be set to true to slightly improve performance if your server has many hoppers, but be warned that it will allow hoppers outside regions to pull items from inside regions if they are close enough to the border."
    break-hoppers-on-denied-move,TRUE,"If a hopper attempts to pull an item and is denied (as long as the previous config setting is false), WorldGuard will break the hopper block to prevent it from continuously attempting to pull items."

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
    use-max-priority-association,FALSE,"Wheter or not non-player associables, such as pistons, are only members of the regions with the highest priorities in which they are in. If set to ``false``, non-player associables are members of all regions in which they are in, which means that pistons, for example, can push blocks from inside a region outwards into a surrounding region (unless there is only a protected :doc:`regions/global-region` outside). If set to ``true``, pistons, for example, cannot push blocks from inside a region outwards into a surrounding protected region with a lower priority."

gameplay.*
~~~~~~~~~~

.. csv-table::
    :header: Setting, Default, Description
    :widths: 12, 5, 30

    block-potions,[],A list of potion types that cannot be used. The list of possible potion types can be `found in Bukkit <https://hub.spigotmc.org/javadocs/bukkit/org/bukkit/potion/PotionEffectType.html>`_.
    block-potions-overly-reliably,FALSE,Whether WorldGuard should try extra hard to block the list of potions mentioned in `block-potions`. This is generally not needed and enabling this may block more than you want.

.. topic:: Example: Blocking the use of night vision and speed potions

    The names `found in Bukkit <https://hub.spigotmc.org/javadocs/bukkit/org/bukkit/potion/PotionEffectType.html>`_ are used::

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

    Material names are from `Bukkit's Material list <https://hub.spigotmc.org/javadocs/bukkit/org/bukkit/Material.html>`_::

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

    disable-lava-fire-spread,FALSE,Whether lava should be able to create fires.
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
    reset-fall-on-void-teleport,FALSE,"Resets fall distance on void teleporting. Leaving this off will likely let the player die of fall damage (though their items will not be in the void, at least)."
    disable-explosion-damage,FALSE,"Whether explosion damage should be disabled for players."
    disable-mob-damage,FALSE,"Whether damage from mobs should be disabled for players."
    disable-death-messages,FALSE,"Whether death messages for players should be disabled."
    
crops.*
~~~~~~~

.. csv-table::
    :header: Setting, Default, Description
    :widths: 12, 5, 30

    disable-creature-trampling,FALSE,"Disable the trampling of farmland by creatures."
    disable-player-trampling,FALSE,"Disable the trampling of farmland by players."

turtle-egg.*
~~~~~~~~~~~~

Same options as the crops section, but for turtle eggs.

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
    disable-vine-growth,FALSE,"Whether the growth of vines and kelp should be disabled."
    disable-crop-growth,FALSE,"Whether the growth of wheat, carrots, melons, etc should be disabled."
    disable-soil-dehydration,FALSE,"Whether the dehydration of soil should be disabled."
    disable-coral-block-fade,FALSE,"Whether coral should remain alive when not in water."
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

    wand,leather,"The ID of the item that is used to right click a block to inspect the regions affecting it. By default, this item is cow leather. Before, it was string but Minecraft added a use for string."
    disable-bypass-by-default,FALSE,"Whether bypass permissions are disabled by default."
    announce-bypass-status,FALSE,"Whether a hint for ``/region bypass`` should be displayed at login."
    invincibility-removes-mobs,FALSE,"If enabled, if a player is attacked while he or she is invincible due to the invincibility :doc:`region flag <regions/flags>`, then the attacking mob is removed from the world."
    fake-player-build-override,TRUE,"Whether players with a name that start with ``[`` and end with ``]`` should bypass all protection. This is only the case with third-party plugins and mods that use 'fake players' in lieu of firing the proper events."
    explosion-flags-block-entity-damage,TRUE,"Whether the various ``-explosion`` :doc:`region flags <regions/flags>` should also disable entity damage when enabled."
    high-frequency-flags,FALSE,"Whether to handle frequently occurring events such as fire spread or fluid flow in regards to :doc:`region flags <regions/flags>` (such as ``fire-spread``, ``water-flow``, and ``lava-flow``). This is disabled by default because those mentioned flags are rarely used and this setting may result in a performance impact in certain scenarios (many, many players and many, many regions)."
    protect-against-liquid-flow,FALSE,"Whether fluid flows between regions should be blocked. This prevents one player from griefing another by having lava or water flow into the other player's region. This setting must be enabled in tandem with ``high-frequency-flags`` for it to take effect."
    use-paper-entity-origin,FALSE,"When on a `Paper server <https://papermc.io>`_, this option will treat entities as members of the region where they spawned, not where they currently are. This will automatically prevent mobs that wander into regions from accidentally destroying it. (Note that this is separate from mobs that are targetting players in regions - that is still dependent on the player's permission, not the mob's.)"
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
