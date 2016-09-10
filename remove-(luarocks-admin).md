**[[luarocks-admin]] remove** - Add a rock or rockspec to a rocks server.

## Table of contents

* [Usage](#usage)
* [Examples](#examples)
    * [Basic example](#basic-example)
    * [Handling multiple repositories](#handling-multiple-repositories)

## Usage

`luarocks-admin remove [--server=<server>] [--no-refresh] {<rockspec>|<rock>}...`

Arguments are local files, which may be rockspecs or rocks.

The flag `--server` indicates which server to use. If not given, the default server set in the `upload_server` variable from the [[configuration file|Config file format]] is used instead.

The flag `--no-refresh` indicates the local cache should not be refreshed prior to generation of the updated manifest.

You need to have [rsync](https://rsync.samba.org/) installed in order to use this command.

## Examples

### Basic example

Remove a rockspec from your default configured upload server:

```
luarocks-admin remove lpeg-0.9-1.rockspec
```

### Handling multiple repositories

Assuming your `~/.luarocks/config.lua` file looks like this:

```lua
upload_server = "main"
upload_servers = {
   main = {
      rsync = "www.example.com/repos/main",
   },
    dev = {
      rsync = "www.example.com/repos/devel-rocks",
   },
}
```

you can specify which repository to use with the `--server` flag:

```
luarocks-admin remove --server=dev my_rock-scm-1.rockspec
```
