**[[luarocks-admin|luarocks admin]] add** - Add a rock or rockspec to a rocks server.

## Table of contents

* [Usage](#usage)
* [Examples](#examples)
    * [Basic example](#basic-example)
    * [Handling multiple repositories](#handling-multiple-repositories)

## Usage

`luarocks-admin add [--server=<server>] [--no-refresh] {<rockspec>|<rock>}...`

Arguments may be local rockspecs or rock files. The flag `--server` indicates which server to use. If not given, the default server set in the `upload_server` variable from the [[configuration file|Config file format]] is used instead. You need to either explicitly pass a full URL to `--server` or configure an upload server in your configuration file prior to using the `add` command.

The flag `--no-refresh` indicates the local cache should not be refreshed prior to generation of the updated manifest.

## Examples

### Basic example

Add a rockspec to your default configured upload server:

```
luarocks-admin add lpeg-0.9-1.rockspec
```

### Handling multiple repositories

Assuming your `~/.luarocks/config.lua` file looks like this:

```lua
upload_server = "main"
upload_servers = {
   main = {
      http = "www.example.com/repos/main",
      sftp = "myuser@example.com/var/www/repos/main"
   },
   dev = {
      http = "www.example.com/repos/devel-rocks",
      sftp = "myuser@example.com/var/www/repos/devel-rocks"
   },
}
```

you can specify which repository to use with the `--server` flag:

```
luarocks-admin add --server=dev my_rock-scm-1.rockspec
```
