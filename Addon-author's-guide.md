**NOTE**: At this moment the addon system described in this page is not yet merged. It can be found in the "addon-prototype" branch of xiaq's clone (https://github.com/xiaq/luarocks/tree/addon-prototype). There are also some rough edges and planned features; they are noted below.

## Overview and Anatomy of Addons

Addons are used to introduce new, optional behavior to LuaRocks. They may introduce a new command and introduce new rockspec fields, and hook into various execution points of LuaRocks.

**TODO**: New flags for existing commands is a planned feature.

An addon named `$a` is the lua module `luarocks.addon.$a`. There are two entry points to the addon, `run` and `load`, which correspond to two ways of activating the addon, either by calling the eponymous subcommand of LuaRocks, or using the addon through a rockspec.

*   When an addon is installed, an eponymous subcommand of LuaRocks is automatically enabled; e.g. with `luarocks.addon.test` installed, the user may use `luarocks test ...`. When this subcommand is called, the `run` function of the addon is called with the command line arguments.

    **NOTE**: Some global options are processed before calling `run` and do not appear in its parameter list. The exact behavior should be documented later; the curious may consult relevant code in [`command_line.run_command`](https://github.com/xiaq/luarocks/blob/c513c3e924c810e52de0b88f4dc5d76c86067ed6/src/luarocks/command_line.lua#L61).

*   The `load` function is called when the name of the addon appears in the `addons` field of the rockspec.

## Relevant fields in rockspec

Two new fields are now available in the rockspec, `build_dependencies` and `addons`. The former follows exactly the same format as `dependencies`, except that they are satisfied at build-time and not required to install the built binary rocks. The latter is a list of addon names; named addons are loaded during the loading of the rockspec. To use an addon, its name should appear in `addons` and the name of the rock should appear in `build_dependencies`. For instance, to use an addon `test` provided by a rock `luarocks-addon-test`, the following is needed:

    build_dependencies = {"luarocks-addon-test"}
    addons = {"test"}

**NOTE**: This will change before the release. One problem is the duplication in the example above. Another more important problem is that addons are not really "build-time dependencies" but "load-time dependencies" (now build-time dependencies are treated like load-time dependencies).

## luarocks.api

The addon should then make use of the `luarocks.api` module to alter the behavior of LuaRocks. The most essential functions exposed through the module are:

*   `api.register_rockspec_field(name, typetbl, callback)`: register a new rockspec field. The first parameter, `name`, can be a dot-separated string to refer to subfields of existing fields. The parameter `typetbl` is a table that stipulates the type of the new field; it is documented in [the comment in `type_check.lua`](https://github.com/xiaq/luarocks/blob/c513c3e924c810e52de0b88f4dc5d76c86067ed6/src/luarocks/type_check.lua#L18). The parameter `callback` is called after the parsing of the rockspec is finished and is supplied the value of the registered field and the whole rockspec; it is not called if any part of the rockspec fails type-checking.

    For instance, the following code registers a new subfield `foo` in the `build` field, which is required to be a string, and prints the package name and value of the new field:

        api.register_rockspec_field(
            "build.foo", {_type="string"}, function(foo, rockspec)
                print("package="..rockspec.package..", foo="..foo)

    The new field must not collide with existing ones - neither builtin fields like `package` or `build`, nor those registered by other addons. In case of registering a new subfield, all but the last one component must exist; registering `a.b.c.d` requires that `a`, `a.b`, `a.b.c` exist, but `a.b.c.d` should not.

*   `api.register_hook(point, callback)`: register a new hook callback. The parameter `point` specifies the point at which the callback is to be called; the current possible values for `name` are `build.before` and `before.after`. Both are triggered during the building procedure. The former is triggered just after all dependencies of the rock has been satisfied and the source has been fetched. The latter is triggered just the rock has been built and installed.

    The callbacks are given the current rockspec as their sole parameters.

    **TODO**: The names and semantics of `build.before` and `build.after` might change before the release. More hook points should be introduced.

**TODO**: When callbacks are supplied the "rockspec", they are not supplied the original rockspec, but a intermediate version where some fields have been "parsed". Notably, the values in `dependencies` and `build_dependencies` fields are not strings but an internal representation. This will be fixed before the release.

There are other API functions exposed through this module. For instance `api.load_rockspec` is likely useful in the `run` function of an addon. For now, the reader has to consult the source of [`api.lua`](https://github.com/xiaq/luarocks/blob/c513c3e924c810e52de0b88f4dc5d76c86067ed6/src/luarocks/api.lua) and relevant functions in other files.

You should not `require` other `luarocks.*` modules, since the internal APIs are not guaranteed to be backward-compatible; doing so will end up with an error. (It is possible to circumvent this limitation, but then you are at your own risk.)

## Examples

Two sample addons have been developed, [luarocks-addon-moonc](https://github.com/xiaq/luarocks-addon-moonc) and [luarocks-addon-test](https://github.com/xiaq/luarocks-addon-test). Both addons work with the `builtin` build backend and look into the `build.modules` field:

*    The moonc addon checks the existence of the files named in `build.modules`. If a `.lua` file does not exist, but a corresponding `.moon` files does, it compiles the latter to the former. For instance, with the following in the rockspec:

        build = {
            type = 'builtin',
            modules = {
                ["foo"] = "src/foo.lua",
            },
        }

    If `src/foo.lua` does not exist but `src/foo.moon` does, the latter is compiled to the former. This compilation process is triggered either *before* building by having `moonc` in `addons` of the rockspec, or by calling `luarocks moonc [rockspec]` manually.

*    The test addon tries to find test files for files in `build.modules`. For instance, with the rockspec above, it will try to find `src/foo_test.lua` and run it. This testing process is triggered either *after* building by having `test` in `addons` of the rockspec, or by calling `luarocks test [rockspec]` manually.

There is also a [dinner](https://github.com/xiaq/dinner) module that uses the above two addons. All these modules are also available in [xiaq's modules registry](http://luarocks.org/modules/xiaq). (Weirdly they do appear on the root registry of luarocks.org.)

**TODO**: The design of the test addon is a bit confusing. If the tests are run after building, they may assume that the rock is installed and can be `require`d. If the tests are run manually, they may not assume so.