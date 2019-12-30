=================================
Custom Flags and Session Handlers
=================================

As of WorldGuard 6.2, custom flags and session handlers are supported. This allows third-party plugins to create their own flags for WorldGuard regions.

Registering New Flags
=====================

Make sure that you have added WorldGuard as a :doc:`../dependency` first. The order of registration is very important, so WorldGuard must load before your plugin for this to work.

Flags have to be registered with WorldGuard's ``FlagRegistry`` with the ``register(Flag<?> flag)`` method. The parameter should be an instance of any flag object, whether you use one of the default types or your own type.

Registering has to be done before WorldGuard is **enabled**. Thus, it is highly recommended that you register when your plugin **loads**. After WorldGuard is enabled, the FlagRegistry is locked and no new flags can be registered.

.. topic:: Example: Registering a custom flag

    .. code-block:: java

        // declare your flag as a field accessible to other parts of your code (so you can use this to check it)
        // note: if you want to use a different type of flag, make sure you change StateFlag here and below to that type
        public static StateFlag MY_CUSTOM_FLAG;

        @Override
        public void onLoad() {
            // ... do your own plugin things, etc

            FlagRegistry registry = WorldGuard.getInstance().getFlagRegistry();
            try {
                // create a flag with the name "my-custom-flag", defaulting to true
                StateFlag flag = new StateFlag("my-custom-flag", true);
                registry.register(flag);
                MY_CUSTOM_FLAG = flag; // only set our field if there was no error
            } catch (FlagConflictException e) {
                // some other plugin registered a flag by the same name already.
                // you can use the existing flag, but this may cause conflicts - be sure to check type
                Flag<?> existing = registry.get("my-custom-flag");
                if (existing instanceof StateFlag) {
                    MY_CUSTOM_FLAG = (StateFlag) existing;
                } else {
                    // types don't match - this is bad news! some other plugin conflicts with you
                    // hopefully this never actually happens
                }
            }
        }

Once your flag is registered, WorldGuard will take care of loading and saving it from the region database, allowing users to set it via the ``/rg flag`` commands, and so on. Even if your plugin is removed from the server, WorldGuard will keep the flag saved to any regions it was set on, but it will be rendered inert until your plugin is loaded again.

Using Session Handlers
======================

In general, your flags will be used in event handlers by :doc:`querying values <flag-calculation>`. However, some flags may be designed for specific tasks such as:

* Running some method periodically on all players in regions with the given flag (e.g. heal flag)
* Responding to a player entering or leaving a region with the flag set (e.g. greeting flag)

Custom handlers can be registered any time after WorldGuard enables. A handler is instantiated by a factory method for each session (player) when it is created. There are also some methods in the Handler class which your custom handler has to override. If you are using a handler for the second type of behavior, it is recommended that you extend the FlagValueChangeHandler class instead, which handles a lot of the region-border-crossing logic for you.

.. topic:: Example: Creating and registering a custom handler

    .. code-block:: java

        public class CustomHandler extends FlagValueChangeHandler<State> {
            public static final Factory FACTORY = new Factory();
            public static class Factory extends Handler.Factory<CustomHandler> {
                @Override
                public CustomHandler create(Session session) {
                    // create an instance of a handler for the particular session
                    // if you need to pass certain variables based on, for example, the player
                    // whose session this is, do it here
                    return new CustomHandler(session);
                }
            }
            // construct with your desired flag to track changes
            public CustomHandler(Session session) {
                super(session, MyPlugin.MY_CUSTOM_FLAG);
            }
            // ... override handler methods here
        }


    .. code-block:: java

        SessionManager sessionManager = WorldGuard.getInstance().getPlatform().getSessionManager();
         // second param allows for ordering of handlers - see the JavaDocs
        sessionManager.registerHandler(MyCustomHandler.FACTORY, null);

.. tip::
     WorldGuard's inbuilt handlers can be found in the `com.sk89q.worldguard.session.handler <https://github.com/EngineHub/WorldGuard/tree/master/worldguard-core/src/main/java/com/sk89q/worldguard/session/handler>`_ package. These should serve as good examples for implementing your own handlers.

.. note::
    Not all of WorldGuard's Session uses are flag-related. WorldGuard also uses Sessions to manage god mode, for example.
