**[[luarocks]] search** - Query the LuaRocks servers

## Usage

`luarocks search [--procelain] [--source] [--binary] { <query> [<version>] | --all }`

Lists files available on LuaRocks servers. `<query>` is a substring of a rock name to filter by.
The search can be narrowed further narrowed down using a version. To list files for
all rocks pass `--all`.

If `--porcelain` is passed, machine-friendly output is produced.

If `--source` is passed, only rockspecs and source rocks are shown.

If `--binary` is passed, only binary and pure-Lua rocks are shown.

Rocks not supporting version of Lua used by LuaRocks are not listed.

The [[search]] command queries remote repositories. To query your local repository (the rocks you have installed), use [[list]].

## Example

```
luarocks search socket
```
