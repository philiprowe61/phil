LuaRocks performs network access through either helper applications (typically `curl` on Mac OSX, or `wget` on other platforms), or using built-in modules LuaSocket and LuaSec. All of them use the same method to configure proxies: the `http_proxy`, `https_proxy` and `ftp_proxy` environment variables.

## Environment variable example

On Unix systems, you can set the ''http_proxy'' environment variable like this:

    export http_proxy=http://server:1234

On Windows systems, the command syntax is:

    set http_proxy=http://server:1234

## External references

* [curl manpage](http://www.hmug.org/man/1/curl.php)
* [How to use wget through proxy](http://blog.taragana.com/index.php/archive/how-to-use-wget-through-proxy/)
