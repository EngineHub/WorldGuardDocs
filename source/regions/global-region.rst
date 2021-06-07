=============
Global Region
=============

The global region is a special region that:

* Encompasses the entire world
* Has the lowest possible priority
* Has some special behavior

Every world has its own global region. However, the global region does not exist until you attempt to modify it by referring to the global region with the name ``__global__``.

For example, setting a flag on the global region would create the region automatically::

    /rg flag __global__ pvp deny

Properties
==========

Think of the global region as a region that is always there. Flags set on the global region, aside from a few flags, work like they would on any other region.

However, unlike normal regions, the ``passthrough`` :doc:`region flag <flags>` is implicitly set to *allow*. If you recall, setting passthrough to allow makes the region a non-protection region, so players can build in the region as long as there are not other regions preventing it.

Changing ``passthrough``
~~~~~~~~~~~~~~~~~~~~~~~~

If you change the ``passthrough`` :doc:`flag <flags>` to deny, making the global region act like a regular region, players must be owners or members of the global region in order to build in it. Because the global reason encompasses the entire world, that means building is denied by default.

Because the global region is considered the lowest priority region, any other normal regions made will override the global region and thus allow building if a player is a member of that region.

.. note::
    The passthrough flag has nothing to do with movement. It means "passthrough of build permissions," as better explained in :doc:`flags`.

.. topic:: Example: Preventing building in the "wilderness"

    The ``passthrough`` flag can be set to ``deny``::

        /rg flag __global__ passthrough deny

Adding Members
~~~~~~~~~~~~~~

Due to legacy reasons, adding owners or members to the global region implicitly sets ``passthrough`` to deny. That means that you do not have to actually change the flag, although it is recommended anyway.

Non-Player Associables
~~~~~~~~~~~~~~~~~~~~~~

Non-player associables, such as pistons, are usually members of either all regions in which they are in or only of the regions with the highest priorities in which they are in, depending on the ``use-max-priority-association`` :doc:`../config` setting. However, non-player associables are no longer members of the global region if they are in at least one other region. This means that pistons, for example, cannot push blocks from inside a region into a protected global region. Thus the global region always behaves as if ``use-max-priority-association`` is set to ``true``.

The membership of non-player associables can additionally be adjusted by setting the ``nonplayer-protection-domains`` flags of the regions. However, this flag works differently on the global region. If there is at least one domain to which a normal region and the global region belong to, this implies that non-player associables that are members of the normal region, will also be members of the global region but **not** vice versa.

Build Flag
~~~~~~~~~~

Setting the ``build`` flag to ``allow`` has **no** effect as there should be no reason to. Setting the flag to ``deny`` works like it does with any other region, but be aware that setting ``build`` to deny on any region essentially means that *nothing* can break or place blocks, therefore breaking things like pistons. Since the global region encompasses the entire world, it would break all pistons that are not within another region.

.. warning::

    Setting the ``build`` flag on the global region is strongly discouraged. If you want to protect the world by default, set the ``passthrough`` flag.

Overriding Defaults
===================

Flags in WorldGuard come with certain *defaults*. For example, the ``exp-drop`` flag defaults to allow if no regions set it, which means that experience drops are permitted even when a player is not a member or owner of the region.

If you want to override the ``exp-drops`` flag by setting it to ``deny`` and having it apply to only non-members, setting it on the global region would not work. For example, you may be tempted to use ``/rg flag __global__ exp-drop -g nonmembers deny``, but this will **not** work. When you specify non-members, it refers to non-members of the *global region*. Thus, if you make, for example, a plot region, XP drops would be denied for the plot's members because the plot's members are not members of the global region. (Remember, the global region is of a low priority. However, regions do not inherit from it so flags do not propagate.)

In these cases, it is recommend that you create a "template region" as explained :doc:`in the priorities and inheritance page <priorities>`.
