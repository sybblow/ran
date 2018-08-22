Ran: a simple static web server written in Go
=============================================

![Ran](ran.gif)

Ran is a simple web server for serving static files.

## Features

- Directory listing
- Automatic gzip compression
- Digest authentication
- Access logging
- Custom 401 and 404 error file
- TLS encryption

## What is Ran for?

- File sharing in LAN or home network
- Web application testing
- Personal web site hosting or demonstrating

## Dependencies

- [github.com/abbot/go-http-auth](https://github.com/abbot/go-http-auth)
- [github.com/oxtoacart/bpool](https://github.com/oxtoacart/bpool)
- [github.com/m3ng9i/go-utils/http](https://github.com/m3ng9i/go-utils)
- [github.com/m3ng9i/go-utils/log](https://github.com/m3ng9i/go-utils)
- [github.com/m3ng9i/go-utils/possible](https://github.com/m3ng9i/go-utils)
- [golang.org/x/net/context](https://github.com/golang/net)

## Installation

Use the command below to install the dependencies mentioned above, and build the binary into $GOPATH/bin.

```bash
go get -u github.com/sybblow/ran
```

For convenience, you can move the ran binary to a directory in the PATH environment variable.

You can also call `./build.py` command under the Ran source directory to write version information into the binary, so that `ran -v` will give a significant result. Run `./build.py -h` for help.

## Download binary

You can also download the Ran binary without building it yourself.

[Download Ran binary from the release page](https://github.com/sybblow/ran/releases).

## Run Ran

You can start a web server without any options by typing `ran` and pressing return in terminal window. This will use the following default configuration:

Configuration               | Default value
----------------------------|--------------------------------
Root directory              | the current working directory
Port                        | 8080
Index file                  | index.html, index.htm
List files of directories   | false
Gzip                        | true
Digest auth                 | false
TLS encryption              | off

Open http://127.0.0.1:8080 in browser to see your website.

You can use the command line options to override the default configuration.

Options:

```
    -r,  -root=<path>        Root path of the site. Default is current working directory.
    -p,  -port=<port>        HTTP port. Default is 8080.
         -404=<path>         Path of a custom 404 file, relative to Root. Example: /404.html.
    -i,  -index=<path>       File name of index, priority depends on the order of values.
                             Separate by colon. Example: -i "index.html:index.htm"
                             If not provide, default is index.html and index.htm.
    -l,  -listdir=<bool>     When request a directory and no index file found,
                             if listdir is true, show file list of the directory,
                             if listdir is false, return 404 not found error.
                             Default is false.
    -sa, -serve-all=<bool>   Serve all paths even if the path is start with dot.
    -g,  -gzip=<bool>        Turn on or off gzip compression. Default value is true (means turn on).

    -am, -auth-method=<auth> Set authentication method, valid values are basic and digest. Default is basic.
    -a,  -auth=<user:pass>   Turn on authentication and set username and password (separate by colon).
                             After turn on authentication, all the page require authentication.
         -401=<path>         Path of a custom 401 file, relative to Root. Example: /401.html.
                             If authentication fails and 401 file is set,
                             the file content will be sent to the client.

         -tls-port=<port>    HTTPS port. Default is 443.
         -tls-policy=<pol>   This option indicates how to handle HTTP and HTTPS traffic.
                             There are three option values: redirect, both and only.
                             redirect: redirect HTTP to HTTPS
                             both:     both HTTP and HTTPS are enabled
                             only:     only HTTPS is enabled, HTTP is disabled
                             The default value is: only.
         -cert=<path>        Load a file as a certificate.
                             If use with -make-cert, will generate a certificate to the path.
         -key=<path>         Load a file as a private key.
                             If use with -make-cert, will generate a private key to the path.
```

Other options:

```
         -make-cert          Generate a self-signed certificate and a private key used in TLS encryption.
                             You should use -cert and -key to set the output paths.
         -showconf           Show config info in the log.
         -debug              Turn on debug mode.
    -v,  -version            Show version information.
    -h,  -help               Show help message.
```

If you want to shutdown Ran, type `ctrl+c` in the terminal, or kill it in the task manager.

### Examples

Example 1: Start a server in the current directory and set port to 8888

```bash
ran -p=8888
```

Example 2: Set root to /tmp, list files of directories and set a custom 404 page

```bash
ran -r=/tmp -l=true -404=/404.html
```

`-l=true` can be shorted to `-l` for convenience.

Example 3: Turn off gzip compression, set access username and password and set a custom 401 page

```bash
ran -g=false -a=user:pass -401=/401.html
```

Example 4: Set custom index file

```bash
ran -i default.html:index.html
```

Example 5: Turn on TLS encryption

If you want to turn on TLS encryption (https), you should use `-cert` to load a certificate and `-key` to load a private key.

The default TLS port is 443, you can use `-tls-port` to set it to another port.

The following command load a certificate and a private key, and set TLS port to 9999. It can be browsed at https://127.0.0.1:9999.

```bash
ran -cert=/path/to/cert.pem -key=/path/to/key.pem -tls-port=9999
```

Example 6: Control HTTP and HTTPS traffic

When you turn on TLS, you can choose to disable HTTP, redirect HTTP to HTTPS or let them work together.

You can use `-tls-policy` to control HTTP and HTTPS traffic:

- If set to "redirect", all HTTP traffic will be redirect to HTTPS.
- If set to "both", both HTTP and HTTPS are enabled.
- If set to "only", only HTTPS is enabled, HTTP is disabled.

If not provide `-tls-policy`, the default value "only" will be used.

An example:

```bash
ran -cert=cert.pem -key=key.pem -tls-policy=redirect
```

Example 7: Create a self-signed certificate and a private key

For testing purposes or internal usage, you can use `-make-cert` to create a self-signed certificate and a private key.

```bash
ran -make-cert -cert=/path/to/cert.pem -key=/path/to/key.pem
```

## Tips and tricks

### Execute permission

Before running Ran binary or build.py, make sure they have execute permission. If don't, use `chmod u+x <filename>` to set.

### download parameter

If you add `download` as a query string parameter in the url, the browser will download the file instead of displaying it in the browser window. Example:

```
http://127.0.0.1:8080/readme.html?download
```

### gzip parameter

Gzip compression is enabled by default. Ran will gzip file automaticly according to the file extension. Example: a `.txt` file will be compressed and a `.jpg` file will not.

If you add `gzip=true` in the url, Ran will force compress the file even if the file should not be compressed. Example:

```
http://127.0.0.1:8080/picture.jpg?gzip=true
```

If you add `gzip=false` in the url, Ran will not compress it even if it should be compressed. Example:

```
http://127.0.0.1:8080/large-file.txt?gzip=false
```

Read the source code of [CanBeCompressed()](https://github.com/m3ng9i/go-utils/blob/master/http/can_be_compressed.go) to learn more about automatic gzip compression.

## Changelog

- **v0.1.3**:

    - Add trailing slash if the request path is a directory and the directory contains a index file.
    - Add basic auth; add -am, -auth-method option.
    - Add -sa, -serve-all option to set if skip paths that start with dot.
    - Print the listening URLs after the server starts.

- **v0.1.2**: Add TLS encryption; add custom 401 file.
- **v0.1.1**: Fix bugs and typos.
- **v0.1**: Initial release.

## ToDo

The following functionalities will be added in the future:

- Load config from file
- IP filter
- Custom log format
- etc

## What's the meaning of Ran

It's a Chinese PinYin and pronounce 燃, means flame burning.

## Author

mengqi <https://github.com/m3ng9i>

If you like this project, please give me a star.

