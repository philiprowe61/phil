
```
NAME
	luarocks upload - Upload a rockspec to the public rocks repository.

SYNOPSIS
	luarocks upload [--skip-pack] [--api-key=<key>] [--force] <rockspec>

DESCRIPTION
	<rockspec>       Pack a source rock file (.src.rock extension),
	                 upload rockspec and source rock to server.
	--skip-pack      Do not pack and send source rock.
	--api-key=<key>  Give it an API key. It will be stored for subsequent uses.
	--force          Replace existing rockspec if the same revision of
	                 a module already exists. This should be used only 
	                 in case of upload mistakes: when updating a rockspec,
	                 increment the revision number instead.
```