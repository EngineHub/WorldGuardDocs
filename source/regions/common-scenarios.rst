================
Common Scenarios
================

These are common scenarios that you may come across. Some of these scenarios have been already been described in some other pages.

.. contents::
    :local:
    :backlinks: none

.. tip::
    Be sure to also to check out the :doc:`../faq` page for more solutions to common problems.

General
=======

How do I allow the usage of doors, levers, etc.?
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If you want everyone to be able to use doors, levers, buttons, pressure plates, and so on, set the ``use`` flag::

    /rg flag REGION_NAME use allow

If you want to apply it to **all** regions, set it on the :doc:`global-region`::

    /rg flag __global__ use allow

How do I let people ride horses and Minecarts?
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If you want everyone to be able to use all vehicles, set the ``ride`` flag::

    /rg flag REGION_NAME ride allow

If you want to apply it to **all** regions, set it on the :doc:`global-region`::

    /rg flag __global__ ride allow

.. warning::

    This would allow players to take horses from others' regions! Only use this if you want that, or have other protections against stealing horses.

How do I prevent building in the wilderness?
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

As described on the :doc:`global-region` page, you can set the ``passthrough`` flag to ``deny``::

    /rg flag __global__ passthrough deny

Do **not** set the ``build`` flag.

How can I only deny exit for non-members?
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

You want to set the region group of the flag (note that this is the default)::

    /rg addmember example_region sk89q
    /rg flag example_region exit -g nonmembers deny

How can I let players exit one side of an exit=deny region?
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Make two regions:

* One region would have ``exit=deny``.
* The other region would border the side of the deny region, extending a bit outside, on the sides that you want to let players exit out of. This region would have ``exit-overide=true``.

How do I unprotect all enchantment tables?
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

In the :doc:`../config`, adjust the ``interaction-whitelist`` setting and add the enchantment table. Get the proper names that you can use from `Bukkit's Material <https://hub.spigotmc.org/javadocs/bukkit/org/bukkit/Material.html>`_.

How do I allow mining but prevent block placing?
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Set the ``block-break`` flag to ``allow``::

    /rg flag mining_area block-break allow

How do I allow players to read lectern books, but not take them?
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

First, make sure you are on WorldGuard 7.0.1+. Earlier versions of WorldGuard released before 1.14 (and thus before lecterns) existed.

* Set the ``use`` flag to ``allow``: ``/rg flag <region> use allow``

How do I allow breaking of only certain block types?
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Sorry, this is not supported per region yet! However, this can either be done per world in whitelist mode as described in :doc:`../blacklist/index` or using :doc:`../build-perms`.

Plot Setups
===========

How can I create a setup with plots?
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If you want to create plots, you'd make use of region inheritence as described in :doc:`priorities`. There are two regions that you'd want to create, followed by the plot regions themselves.

You will have the following regions:

* The outer region (``mall``)
* An optional "template" region that all plot regions will inherit from (``shop_template``)
* The plots

Create the mall::

    /rg define mall

Create the shop template. However, we'll use ``-g`` to make it a non-physical region since we're not actually using it to protect any area.

.. code-block:: text

    /rg define shop_template -g

Let's make some plots::

    /rg define shop1
    /rg define shop2
    /rg define shop3

Now, you will need to set parents::

    /rg setparent shop_template mall
    /rg setparent shop1 shop_template
    /rg setparent shop2 shop_template
    /rg setparent shop3 shop_template

.. hint::
    An alternative is to set the mall region to a lower priority (``/rg setpriority mall -1``).

How do I prevent door, etc. usage in public areas of the mall?
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The goal here is:

* Set the ``use`` flag to ``deny`` in the mall: ``/rg flag mall use deny``
* Since that prevents usage in the plots too, we need to change ``use`` within the plots only
* Since we created the template region, that makes it easy: ``/rg flag shop_template use allow``

Due to the inheritance, the plots inherit the ``use`` flag from the template, which overrides the ``use`` flag on the mall.

Problems
========

Why don't pistons work?
~~~~~~~~~~~~~~~~~~~~~~~

Did you set the ``build`` flag? You probably do not want to do that. Be sure to remove it::

    /rg flag __global__ build

* If you are trying to prevent building, you don't actually have to do anything! By default, when a region is made, it is protected.
* If you want to prevent building in the wilderness, you can set the ``passthrough`` flag to ``deny``::

    /rg flag __global__ passthrough deny

.. warning::
    At this time, it is not possible for a piston to push from one region into another. This issue is tracked as `WORLDGUARD-3234 <https://github.com/EngineHub/WorldGuard/issues/1047>`_ on the issue tracker.

