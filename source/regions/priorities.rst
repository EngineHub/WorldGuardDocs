========================
Priority and Inheritance
========================

As noted in the :doc:`quick-start`, regions can overlap. When there are overlapping regions, a player must have permission to build in **all** overlapping regions. With priorities and inheritance, however, that rule does not have to be satisfied.

Priorities
==========

Every region defaults to a priority of 0, but it can be adjusted to be positive or negative. Higher numbers imply higher priorities. The valid range of priorities is -2147483648 to 2147483647, inclusive, but you will probably use more reasonable numbers like -2, 10, 15, or 100.

* In terms of membership and who can build: When a certain location has overlapping regions, only the regions of the highest priority are considered.
* In terms of :doc:`region flags <flags>`, the highest priority region with the flag defined is used.

Priorities can be set with ``/rg setpriority``::

    /rg setpriority example 5

.. topic:: Example: Creating an "pub" region at spawn where only those in the "pub group" can build, even though spawn has already has region where "builders" can build

    If you simply make a region overlapping spawn, then a player would have to be both a member of "pub" *and* "builders" in order to build within the pub. Therefore, you want to make the pub region higher priority.

    1. Select the pub area.
    2. Create the "pub" region::

        /rg define pub

    3. Raise the priority of "pub" to some number higher than 0, the priority of the spawn region::

        /rg setpriority pub 10

.. topic:: Example: Making a special "heal" area where PvP is denied within an "arena" region where PvP is permitted

    The goal here is to override the ``pvp = allow`` flag of the arena.

    1. Select the heal area.
    2. Create the "heal" region::

        /rg define heal

    4. Set the ``pvp`` flag to ``deny``::

        /rg flag heal pvp deny

    3. Raise the priority of "heal" to some number higher than 0, the priority of the arena region::

        /rg setpriority heal 10

    Note: Since *deny* overrides *allow* when there is a conflict, you don't actually need to raise the priority of "heal." However, you would need to set the priority if you wanted to override *deny* with *allow*.

Inheritance
===========

Following from the pub example above, what if you want to allow *both* builders and pub members to build in the pub? You could add the builders group to the pub region, or instead, you could have the pub *inherit* its members from the spawn region.

When a region is given a parent:

* It inherits the parent's members and owners
* It inherits the parent's flags *if* if the flag isn't defined on the child region

This comes in handy for:

* A main area and small plots inside this main area
* Creating a template region by which child regions will inherit the flags

Every region can have at most one parent. Set parents with ``/rg setparent``::

    /rg setparent region_name parent_name

To remove a parent, don't specify a parent name::

    /rg setparent region_name

WorldGuard will detect circular inheritance and prevent it.

.. topic:: Example: Creating a "mall" with multiple plots inside of it

    Once the mall, plot1, and plot2 regions have been created, parents can be set on the plots::

        /rg setparent plot1 mall
        /rg setparent plot2 mall

    Let's say you have a "mall_owners" group that you add to the mall region::

        /rg addowner mall g:mall_owners

    The mall owners will be able to build within the plots.

    If you add a member or owner to one of the plots, the player will be able to build only within their plot::

        /rg addowner plot1 sk89q

.. note::
    Non-player associables, such as pistons, can also be members of regions. Member inheritance is not limited to players only. This means that pistons in parent regions, for example, can push blocks into their children.

Template Regions
~~~~~~~~~~~~~~~~

As previously mentioned, because flags are inherited from their parents, a parent region can act as a base template for all of its child regions.

However, you may want the template region to *not actually physically exist*, since you're not using it to protect an area. One way to do this is to create a "global region," which is a region that has no actual physical size. Create global regions with the ``-g`` switch on the region creation command::

    /rg define -g plot_template

.. topic:: Example: Having the plots extend a "plot_template" region in the mall example
    
    Create the plot template region::

        /rg define -g plot_template

    Have the plots inherit from the plot template::

        /rg setparent plot1 plot_template
        /rg setparent plot2 plot_template

    Have the plot template inherit from mall::

        /rg setparent plot_template mall

    Let's say you want to let players access any chest in the mall, *except* those in plots. First, you'd use the ``chest-access`` flag on the mall region::

        /rg flag mall chest-access allow

    However, now you need to deny ``chest-access`` in every plot. Fortunately, you have the plot template that you can use::

        /rg flag plot_template chest-access deny

Inheritance versus Priorities
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

A higher priority parent region will override its children, so inheritance only properly works when the children are of the same or higher priority compared to their parent regions.

Region Groups and Overlapping Regions
=====================================

As you may be aware, flags can be made to apply to only certain groups::

    /rg flag mall pvp -g nonmembers deny

When there is only one region, it's clear which players are members and which are not. However, it's less obvious when there are overlapping regions: does a player only need to be a member of one of the regions?

The answer is no. The player must be a member of the region *on which the flag is set*. 

For example, let's imagine two overlapping regions:

* Spawn, with flag ``pvp -g nonmembers deny`` and no members
* Market, with member "sk89q"

PvP would be denied for sk89q because sk89q is not a member of spawn.

In the Context of Inheritance
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

When a region has a parent set, both flags and the list of members are inherited, so a player *can* be a member of one of the child regions too.

For example, if there are two regions:

* Market, with flag ``pvp -g nonmembers deny`` and no members
* Shop1, with member "sk89q," **inheriting** from the market

Is sk89q a member? Yes, so sk89q is allowed to PvP.

.. note::
    As mentioned previously, parent regions should **not** have a higher priority than their children, otherwise this will not work correctly. PvP would be denied for sk89q because the parent region would override the child.