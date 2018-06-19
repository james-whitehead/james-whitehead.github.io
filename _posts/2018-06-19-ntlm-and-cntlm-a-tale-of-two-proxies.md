---
published: false
---
## NTLM and CNTLM: A Tale of Two Proxies

If you're behind a corporate firewall, you might have seen this before. You might have seen this lots of times before.

```powershell
PS C:\&gt; wget https:&#x2F;&#x2F;www.google.co.uk
wget : Proxy Authentication Required
This server could not verify that you are authorized to access the document requested. Either you supplied the wrong
credentials (e.g., bad password), or your browser doesn&#x27;t understand how to supply the credentials required.
At line:1 char:1
+ wget https:&#x2F;&#x2F;www.google.co.uk
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : InvalidOperation: (System.Net.HttpWebRequest:HttpWebRequest) [Invoke-WebRequest],
    						  WebException
    + FullyQualifiedErrorId : WebCmdletWebResponseException,Microsoft.PowerShell.Commands.InvokeWebRequestCommand
```

```powershell
PS C:\&gt; pip install pep8
Collecting pep8
  Retrying (Retry(total=4, connect=None, read=None, redirect=None)) after connection broken by &#x27;
  	ConnectTimeoutError(&ltt;pip._vendor.requests.packages.urllib3.connection.VerifiedHTTPSConnection object
      at 0x0000023873264B70&gt;, &#x27;Connection to pypi.python.org timed out.
      (connect timeout=15)&#x27;)&#x27;: /simple/pep8/
  Retrying (Retry(total=3, connect=None, read=None, redirect=None)) after connection broken by &#x27;
  	ConnectTimeoutError(&ltt;pip._vendor.requests.packages.urllib3.connection.VerifiedHTTPSConnection object
      at 0x0000023873264B70&gt;, &#x27;Connection to pypi.python.org timed out.
      (connect timeout=15)&#x27;)&#x27;: /simple/pep8/
  Retrying (Retry(total=2, connect=None, read=None, redirect=None)) after connection broken by &#x27;
  	ConnectTimeoutError(&ltt;pip._vendor.requests.packages.urllib3.connection.VerifiedHTTPSConnection object
      at 0x0000023873264B70&gt;, &#x27;Connection to pypi.python.org timed out.
      (connect timeout=15)&#x27;)&#x27;: /simple/pep8/
  Retrying (Retry(total=1, connect=None, read=None, redirect=None)) after connection broken by &#x27;
  	ConnectTimeoutError(&ltt;pip._vendor.requests.packages.urllib3.connection.VerifiedHTTPSConnection object
      at 0x0000023873264B70&gt;, &#x27;Connection to pypi.python.org timed out.
      (connect timeout=15)&#x27;)&#x27;: /simple/pep8/
  Retrying (Retry(total=0, connect=None, read=None, redirect=None)) after connection broken by &#x27;
  	ConnectTimeoutError(&ltt;pip._vendor.requests.packages.urllib3.connection.VerifiedHTTPSConnection object
      at 0x0000023873264B70&gt;, &#x27;Connection to pypi.python.org timed out.
      (connect timeout=15)&#x27;)&#x27;: /simple/pep8/
  Could not find a version that satisfies the requirement pep8 (from versions: )
No matching distribution found for pep8
```

```powershell
PS C:\&gt; git clone http:&#x2F;&#x2F;github.com&#x2F;james-whitehead&#x2F;james-whitehead.github.io.git folder
Cloning into &#x27;folder&#x27;...
error: The requested URL returned error: 407 while accessing
http:&#x2F;&#x2F;github.com&#x2F;james-whitehead&#x2F;james-whitehead.github.io.git&#x2F;info&#x2F;refs
fatal: HTTP request failed
```