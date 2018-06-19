---
published: true
layout: post
excerpt: Setting up applications to work with a corporate proxy
comments: true
---
## Introducton

If you're behind a corporate proxy, you might have seen this before. You might have seen this lots of times before.

```
curl : Proxy Authentication Required
This server could not verify that you are authorized to access the document requested. Either you supplied the wrong credentials (e.g., bad password), or your browser doesn't understand how to supply the credentials required.
```

```
Collecting pep8
  Retrying (Retry(total=4, connect=None, read=None, redirect=None)) after connection broken by 'ConnectTimeoutError(<pip._vendor.requests.packages.urllib3.connection.VerifiedHTTPSConnection object at 0x0000023873264B70>, 'Connection to pypi.python.org timed out. (connect timeout=15)')'
No matching distribution found for pep8
```

```
error: The requested URL returned error: 407 while accessing
http://github.com/james-whitehead/james-whitehead.github.io.git/info/refs
fatal: HTTP request failed
```

This means you're behind [NTLM](https://en.wikipedia.org/wiki/NT_LAN_Manager), a very strict proxy that requires Windows authentication to let applications through. This works fine for browsers that support NTLM, but many applications (especially command-line applications such as curl, pip and git given in the examples) do not support this level of authentication and won't be able to get through the proxy.

Luckily, there's a solution. A utility that sits in between your applications and the proxy and handles all the authentication, called [CNTLM.](http://cntlm.sourceforge.net/)

## Setting up CNTLM

CNTLM acts as a local proxy. You pass it your authenication details, and it sets up a proxy on your `127.0.0.1` address on a specified port.

After installing CNTLM, we'll have to edit the `cntlm.ini` file, so make a backup of it first just in case. The important values are all at the top of the file, and we'll go through each of them in order.

### Username and Domain

The first values we need to set are the username and domain fields. You should see something that looks like this at the top of the file:

```python
#
# Cntlm Authentication Proxy Configuration
#
# NOTE: all values are parsed literally, do NOT escape spaces,
# do not quote. Use 0600 perms if you use plaintext password.
#

# Username	testuser
# Domain	corp-uk
```
If you don't know your username or domain, opening up Powershell and running `$env:Username` and `$env:UserDomain` will return the values. Replace the `testuser` and `corp-uk` with your own username and domain, and uncomment the line by removing the `#` from the start.

### Proxy

The proxy values can be found in this section.

```
# List of parent proxies to use. More proxies can be defined
# one per line in format <proxy_ip>:<proxy_port>
#
Proxy		10.0.0.41:8080
Proxy		10.0.0.42:8080
```

To find your own proxy values



