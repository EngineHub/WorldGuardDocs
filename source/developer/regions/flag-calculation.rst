================
Flag Calculation
================

To check flags, an instance of ``ApplicableRegionSet`` (which contains a list of regions) must have been retrieved. That is documented in :doc:`spatial-queries`.

Querying Flags
==============

One issue with query flags given a list of regions is that there may be several regions with the same flag assigned (and with different values). If priorities and child inheritance are properly configured, then there might only be one "effective" value, but otherwise there will be several.

Getting All Values
~~~~~~~~~~~~~~~~~~

``queryAllValues(RegionAssociable, Flag)`` can be used to get all the values that have been set for a flag. Flags can be obtained from ``Flags``.

.. topic:: Example: Getting the greeting message flag, given a player

    .. code-block:: java

        LocalPlayer localPlayer = WorldGuardPlugin.inst().wrapPlayer(player);
        Collection<String> greetings = set.queryAllValues(localPlayer, Flags.GREET_MESSAGE);

Getting One Value
~~~~~~~~~~~~~~~~~

``queryValue(RegionAssociable, Flag)`` can be used to get one value. Depending on the flag type, this may be the first value found, or it may be a "best" value. As of writing, only ``StateFlags`` will actually pick a "best" value.

.. topic:: Example: Getting the greeting message flag, given a player

    .. code-block:: java

        LocalPlayer localPlayer = WorldGuardPlugin.inst().wrapPlayer(player);
        String greeting = set.queryValue(localPlayer, Flags.GREET_MESSAGE);

The returned value may be ``null`` if the flag is not set on any regions.

Getting StateFlag Values
~~~~~~~~~~~~~~~~~~~~~~~~

If you are trying to query a ``StateFlag`` (a flag with allow/deny), there are additional methods that you can use. They allow you to specify multiple state flags for automatic combination (remember, "deny" overrides "allow").

* ``queryState(RegionAssociable, StateFlag...)`` takes one or more flags and returns an allow, deny, or null
* ``testState(RegionAssociable, StateFlag...)`` does the same thing, but returns true if it's allow

You can still use ``queryValue``, but you can only specify one flag at a time.

.. topic:: Example: Testing the build flag

    .. code-block:: java

        LocalPlayer localPlayer = WorldGuardPlugin.inst().wrapPlayer(player);
        if (!set.testState(localPlayer, Flags.BUILD)) {
            event.setCancelled(true);
        }

Flags With No Player
====================

If you are trying to lookup a flag that doesn't use a player (for example, the ``creeper-explosion`` flag), then you can use ``null`` for the ``RegionAssociable``.

.. topic:: Example: Testing the creeper explosion flag

    .. code-block:: java

        if (!set.testState(null, Flags.CREEPER_EXPLOSION)) {
            event.setCancelled(true);
        }

.. hint::
    You can also pass in ``null`` for flags that do use a player, but then :doc:`flag region groups </regions/flags>` will not work correctly.

Also via RegionQuery
====================

The methods described on this page are also conveniently available directly on instances of ``RegionQuery``.

.. topic:: Example: Using a ``RegionQuery`` to directly query flags

    .. code-block:: java

        LocalPlayer localPlayer = WorldGuardPlugin.inst().wrapPlayer(player);
        Location loc = new Location(world, 10, 64, 100);
        RegionContainer container = WorldGuard.getInstance().getPlatform().getRegionContainer();
        RegionQuery query = container.createQuery();

        // No need to bother:
        // ApplicableRegionSet set = query.getApplicableRegions(loc);

        // Just directly test the flag
        query.testState(loc, localPlayer, Flags.BUILD);

In addition, you can use ``testBuild`` and so on as a shortcut to ``testState(..., Flags.BUILD, your flags)``.

Non-Player Actors
=================

Instead of passing in a player, you can instead pass in a (non-``LocalPlayer``) ``RegionAssociable``. This object is used to determine whether to use rules for owners, members, or non-members should be used.

However, let's first consider what happens with players. Given a player part of the build team, who has been made an owner of both spawn's region and the "builder's club," the association returned should be ``OWNER``, as illustrated below:

.. code-block:: java

    List<ProtectedRegion> regions = Arrays.asList(spawnRegion, buildersClub);
    builderPlayer.getAssociation(regions) == Association.OWNER;

As you may be aware, you cannot add entities or blocks as members to a region, so it can't work the same way. To do that, a special ``RegionAssociable`` is used for blocks and entities: it takes a list of **source regions** to determine whether the source regions should be considered a "member" of the target location. This is illustrated below.

.. code-block:: java

    Set deepInside    = newHashSet(spawn, mall);
    Set inside        = newHashSet(spawn);
    Set outside       = newHashSet(); // Empty set

    // outside -> inside = considered as "non-member"
    new RegionOverlapAssociation(outside).getAssociation(inside) == NON_MEMBER

    // inside -> inside = considered as "member"
    new RegionOverlapAssociation(inside).getAssociation(inside) == OWNER

    // inside -> deepInside = considered as "member"
    // Note that by default building is blocked in this case.
    // The association must be at least "member" for all regions individually.
    new RegionOverlapAssociation(inside).getAssociation(deepInside) == OWNER

    // inside -> outside = considered as "non-member"
    new RegionOverlapAssociation(inside).getAssociation(outside) == NON_MEMBER

    // deepInside -> inside = considered as "member"
    new RegionOverlapAssociation(deepInside).getAssociation(inside) == OWNER

Note that the ``nonplayer-protection-domains`` flag and region inheritance can override this behavior. The various ``test...`` and ``query...`` methods will handle this for you.

To summarize:

* Player (``LocalPlayer``) objects already implement ``RegionAssociable``
* For entities and blocks, WorldGuard uses the regions where the block or entity is (``RegionOverlapAssociation``)

There is also:

* ``ConstantAssociation`` uses a pre-set type of association (``new ConstantAssociation(Association.MEMBER)`` or ``Associables.constant(Association.MEMBER)``)
* ``DelayedRegionOverlapAssociation`` which works like ``RegionOverlapAssociation``, but doesn't do the spatial query for regions at the source until it is needed

.. topic:: Example: Examining how WorldGuard handles region protection

    First, the correct ``RegionAssociation`` must be created for the event. ``createRegionAssociable()`` described below takes an object and returns a ``RegionAssociable``.

    .. code-block:: java

        private RegionAssociable createRegionAssociable(Object cause) {
            if (cause instanceof Player) {
                return WorldGuardPlugin.inst().wrapPlayer((Player) cause);
            } else if (cause instanceof Entity entity) {
                RegionQuery query = WorldGuard.getInstance().getPlatform().getRegionContainer().createQuery();
                WorldConfiguration config = WorldGuard.getInstance().getPlatform().getGlobalStateManager().get(BukkitAdapter.adapt(entity.getWorld()));
                Location loc = entity.getLocation(); // getOrigin() can be used on Paper if present
                return new DelayedRegionOverlapAssociation(query, BukkitAdapter.adapt(loc), config.useMaxPriorityAssociation);
            } else if (cause instanceof Block block) {
                RegionQuery query = WorldGuard.getInstance().getPlatform().getRegionContainer().createQuery();
                WorldConfiguration config = WorldGuard.getInstance().getPlatform().getGlobalStateManager().get(BukkitAdapter.adapt(block.getWorld()));
                Location loc = block.getLocation();
                return new DelayedRegionOverlapAssociation(query, BukkitAdapter.adapt(loc), config.useMaxPriorityAssociation);
            } else {
                return Associables.constant(Association.NON_MEMBER);
            }
        }

    Let's see where it could be used:

    .. code-block:: java

        @EventHandler
        public void onPlayerBucketFill(PlayerBucketFillEvent event) {
            Player player = event.getPlayer();
            RegionAssociable associable = createRegionAssociable(player);

            if (!set.testState(associable, /* flags here */)) {
                event.setCancelled(true);
            }
        }
