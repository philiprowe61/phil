**remove** - Uninstall a rock.

## Usage

> [[luarocks]] remove [--force|--force-fast] _name_ [_version_]

`name` is the name of a rock to be uninstalled.
If a `version` is not given, try to remove all versions at once.
Will only perform the removal if it does not break dependencies.

To override this check and force the removal, use `--force`.

To perform a forced removal without reporting dependency issues,
use `--force-fast`.

## Example

    luarocks remove --force luafilesystem 1.3.0

