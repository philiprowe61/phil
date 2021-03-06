Tasks performed when making a new LuaRocks release:

* Edit `rockspec` to version it (`VER`)
* Edit `src/luarocks/cfg.lua` to version it (`cfg.program_version` and `cfg.program_series`)
* Run `makedist $VERSION`
* Copy `luarocks-$VERSION.tar.gz` and `luarocks-$VERSION-win32.zip` to `gh-pages/releases`
* Edit `gh-pages/releases/index.html` to add links to both files
* `git add` new files, `git commit` everything
* `git push` the gh-pages branch
* `git commit` changes to `rockspec` and `src/luarocks/cfg.lua`
* `git tag v$VERSION`
* `git push --tags`
* Edit `rockspec` and `src/luarocks/cfg.lua` back to "scm", commit and push (probably should start doing this with branches instead, but this keeps the release tags in the main linear history)
* Write a `luarocks-$VERSION-1.rockspec` file based on the previous one.
* Upload rockspec and .src.rock to luarocks.org
* Edit current version number in [[Download]] page in the wiki
* Run `git diff v$PREV_VERSION v$VERSION` to write a changelog summary
* Edit [[Release history]] to add new release, including changelog
* Submit PR to http://github.com/leafo/luarocks-site updating static/md/home.md to new release
* Write \[ANN\] email, usually a variation of the previous announcement, and send to lua-l@lists.lua.org, luarocks-developers@lists.sf.net and luajit@freelists.org (send as separate messages, avoid cross-posting)
