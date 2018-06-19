---
published: true
layout: post
excerpt: Setting up applications to work with a corporate proxy
comments: true
title: NTLM and CNTLM - A Tale of Two Proxies
---
## Introducton

If you're behind a corporate proxy, you might have seen this before. You might have seen this lots of times before.

```cmd
curl : Proxy Authentication Required
This server could not verify that you are authorized to access the document requested. Either you supplied the wrong credentials (e.g., bad password), or your browser doesn't understand how to supply the credentials required.
```

```cmd
Collecting requests
  Retrying (Retry(total=4, connect=None, read=None, redirect=None)) after connection broken by 'ConnectTimeoutError(<pip._vendor.requests.packages.urllib3.connection.VerifiedHTTPSConnection object at 0x0000023873264B70>, 'Connection to pypi.python.org timed out. (connect timeout=15)')'
No matching distribution found for requests
```

```cmd
error: The requested URL returned error: 407 while accessing
http://github.com/james-whitehead/james-whitehead.github.io.git/info/refs
fatal: HTTP request failed
```

This means you're behind [NTLM,](https://en.wikipedia.org/wiki/NT_LAN_Manager) a very strict proxy that requires Windows authentication to let applications through. This works fine for browsers that support NTLM, but many applications (especially command-line applications such as curl, pip and git given in the examples) do not support this level of authentication and won't be able to get through the proxy.

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

If you don't know your username or domain, you can run `echo %USERNAME%` and `echo %USERDOMAIN%` in a command prompt, `$env:Username` and `$env:UserDomain` in Powershell, or `whoami` in either to return the values. Replace the `testuser` and `corp-uk` with your own username and domain, and uncomment the line by removing the `#` from the start.

### Proxy

The proxy values can be found in this section.

```python
# List of parent proxies to use. More proxies can be defined
# one per line in format <proxy_ip>:<proxy_port>
#
Proxy		10.0.0.41:8080
Proxy		10.0.0.42:8080
```

To find your own proxy values, search in the Start Menu for 'Internet Options', and under the Connections tab, open up the LAN Settings.

![proxy settings]({{ site.url }}/assets/img/proxy-settings.png)

If the IP address and port for the proxy aren't immediately visible, copy the URL in the address bar into a browser to download it, and open it up in a text editor. If the filename ends with `.pac` and the file consists of a JavaScrip function called `FindProxyForURL(url, host)`, then this is a [PAC file.](https://en.wikipedia.org/wiki/Proxy_auto-config)

To find your proxy within the PAC file, look for any lines with the following:

```javascript
return "PROXY <proxy_ip>:<proxy_port>";
```

. Each unique pair of IP address and port should replace the entry in the `cntlm.ini` file. If there is only one proxy, one of the lines in `cntlm.ini` can be deleted. Given the example in the Wikipedia page, The `cntlm.ini` file would look like this:

```python
# List of parent proxies to use. More proxies can be defined
# one per line in format <proxy_ip>:<proxy_port>
#
Proxy		fastproxy.example.com:8080
Proxy		proxy.example.com:8080
```

### Password

The password fields come before the proxy fields, but it's necessary to configure the proxy first. You could store your passsword in plain text, but that's a patently awful idea, so CNTLM lets you hash your password and use that.

Make sure you've saved your changes up to this point, and in the directory you installed CNTLM, open up a command prompt and run the following:

```cmd
C:\Program Files\CNTLM>.\cntlm.exe -c cntlm.ini -H
```

After entering the password for the account you gave in the `Username` and `Domain` fields, you should receive an output that looks like this:

```cmd
Password:
PassLM          1AD35398BE6565DDB5C4EF70C0593492
PassNT          77B9081511704EE852F94227CF48A793
PassNTLMv2      1F6839EE15E639893286CF65C5D0A65C    # Only for user 'testuser', domain 'corp-uk'
```

These are the hashed passwords that can be copied into your config file, in place of these values:

```python
# Example secure config shown below.
# PassLM          1AD35398BE6565DDB5C4EF70C0593492
# PassNT          77B9081511704EE852F94227CF48A793
### Only for user 'testuser', domain 'corp-uk'
# PassNTLMv2      D5826E9C665C37C80B53397D5C07BBCB
```

Don't forget to remove the `#` tags for the three fields!

### Verifying

We've changed a lot of settings, and it'd be nice to know if they've all worked. CNTLM has a utility for that too! Still in the CNTLM directory, run the following:

```cmd
C:\Program Files\CNTLM>.\cntlm.exe -M https://www.google.co.uk
```

Enter your password again, and if all your settings are correct, you should see an output like this:

```cmd
Config profile  1/4... OK (HTTP code: 200)
----------------------------[ Profile  0 ]------
Auth            NTLMv2
PassNTLMv2      D5826E9C665C37C80B53397D5C07BBCB
------------------------------------------------
```

Success! You can now go back to your password fields and re-comment out the two lines that don't match the auth type you see here.

If you see the following:

```cmd
Config profile  1/4... Credentials rejected
Config profile  2/4... Credentials rejected
Config profile  3/4... Credentials rejected
Config profile  4/4... Credentials rejected

Wrong credentials, invalid URL or proxy doesn't support NTLM nor BASIC.
```

Either some of the config settings are wrong, or your proxy is a different beast entirely, sorry.

## Using CNTLM

Now that CNTLM is set up and you've verified it's working, how do you actually use it? Beneath the proxy settings in `cntlm.ini`, you'll see a field titled `Listen`. The default value is `3128`. This is the port, which - along with your local host `127.0.0.1` - CNTLM runs on. Anywhere you see a proxy setting, the `<proxy_address>:<proxy_port>` pair can be replaced with `127.0.0.1:3128`, and CNTLM will handle the authentication!




