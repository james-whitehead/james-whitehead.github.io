---
published: true
layout: post
excerpt: Setting up applications to work with a corporate proxy
comments: true
---
## NTLM and CNTLM: A Tale of Two Proxies

If you're behind a corporate firewall, you might have seen this before. You might have seen this lots of times before.

```powershell
wget : Proxy Authentication Required
This server could not verify that you are authorized to access the document requested. Either you supplied the wrong credentials (e.g., bad password), or your browser doesn't understand how to supply the credentials required.
```

```powershell
pip install pep8
Collecting pep8
  Retrying (Retry(total=4, connect=None, read=None, redirect=None)) after connection broken by 'ConnectTimeoutError(<pip._vendor.requests.packages.urllib3.connection.VerifiedHTTPSConnection object at 0x0000023873264B70>, 'Connection to pypi.python.org timed out.)
No matching distribution found for pep8
```

```powershell
error: The requested URL returned error: 407 while accessing
http://github.com/james-whitehead/james-whitehead.github.io.git/info/refs
fatal: HTTP request failed
```
