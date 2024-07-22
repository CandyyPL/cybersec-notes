# HackTheBox Academy Notes

## Table of Contents
- [Web Requests](#web_requests)
    - [HTTP](#web_requests_http)
    - [HTTPS](#web_requests_https)

## Web Requests <a name='web_requests' />

### HTTP <a name='web_requests_http' />

HTTP communication consists of a client and a server, where the client requests the server for a resource. The server processes the requests and returns the requested resource. The default port for HTTP communication is port 80, though this can be changed to any other port, depending on the web server configuration. The same requests are utilized when we use the internet to visit different websites. We enter a **Fully Qualified Domain Name (FQDN)** as a **Uniform Resource Locator (URL)** to reach the desired website, like www.hackthebox.com.

### URL

Resources over HTTP are accessed via a URL, which offers many more specifications than simply specifying a website we want to visit. Let's look at the structure of a URL:

`http://admin:password@inlanefreight.com:80/dashboard.php?login=true#status`

| Component | Example | Description |
| --- | --- | --- |
| Scheme | `http://` | This is used to identify the protocol being accessed by the client, and ends with a colon and a double slash **(://)** |
| User Info | `admin:password@` | This is an optional component that contains the credentials **(separated by a colon :)** used to authenticate to the host, and is separated from the host with an at sign **(@)** |
| Host | `inlanefreight.com` | The host signifies the resource location. This can be a hostname or an IP address |
| Port | `:80` | The Port is separated from the Host by a colon **(:)**. If no port is specified, http schemes default to port 80 and https default to **port 443** |
| Path | `/dashboard.php` | This points to the resource being accessed, which can be a file or a folder. If there is no path specified, the server returns the default index (e.g. index.html). |
| Query String | `?login=true` | The query string starts with a question mark **(?)**, and consists of a parameter (e.g. login) and a value (e.g. true). Multiple parameters can be separated by an ampersand **(&)**. |
| Fragment | `#status` | Fragments are processed by the browsers on the client-side to locate sections within the primary resource (e.g. a header or section on the page). |

### HTTP Flow

```mermaid
sequenceDiagram
  participant u as User
  participant b as Browser
  participant ds as DNS Server
  participant ws as Web Server

  u ->> b: Go to inlanefreight.com
  b ->> ds: Where is inlanefreight.com?
  ds ->> b: 152.153.81.14
  b ->> ws: HTTP Request to 152.153.81.14:80
  ws ->> b: HTTP Response from inlanefreight.com
  b ->> u: index.html
```

### /etc/hosts file

Our browsers usually first look up records in the local **`/etc/hosts`** file, and if the requested domain does not exist within it, then they would contact other DNS servers. We can use the **`/etc/hosts`** to manually add records to for DNS resolution, by adding the IP followed by the domain name.

### cURL

cURL (client URL) is a command-line tool and library that primarily supports HTTP along with many other protocols. This makes it a good candidate for scripts as well as automation, making it essential for sending various types of web requests from the command line, which is necessary for many types of web penetration tests.

We can send a basic HTTP request to any URL by using it as an argument for cURL, as follows:
```bash
$ curl inlanefreight.com
```

We see that cURL does not render the HTML/JavaScript/CSS code, unlike a web browser, but prints it in its raw format. However, as penetration testers, we are mainly interested in the request and response context, which usually becomes much faster and more convenient than a web browser.

We may also use cURL to download a page or a file and output the content into a file using the -O flag. If we want to specify the output file name, we can use the **-o** flag and specify the name. Otherwise, we can use **-O** and cURL will use the remote file name, as follows:
```bash
$ curl -O inlanefreight.com
$ ls
index.html
```

As we can see, the output was not printed this time but rather saved into index.html. We noticed that cURL still printed some status while processing the request. We can silent the status with the **-s** flag, as follows:
```bash
$ curl -s -O inlanefreight.com
```

### HTTPS <a name='web_requests_https' />

One of the significant drawbacks of HTTP is that all data is transferred in clear-text. To counter this issue, the HTTPS (HTTP Secure) protocol was created, in which all communications are transferred in an encrypted format, so even if a third party does intercept the request, they would not be able to extract the data out of it.

### DNS note

Although the data transferred through the HTTPS protocol may be encrypted, the request may still reveal the visited URL if it contacted a clear-text DNS server. For this reason, it is recommended to utilize encrypted DNS servers (e.g. 8.8.8.8 or 1.1.1.1), or utilize a VPN service to ensure all traffic is properly encrypted.

### HTTPS Flow

```mermaid
sequenceDiagram
    User ->> Browser: Go to inlanefreight.com

    Browser ->> Server: HTTP Request to inlanefreight.com:80
    Server ->> Browser: Redirect to HTTPS (https://inlanefreight.com)
    Browser ->> Server: Connect to inlanefreight.com:443 (Client Hello)
    Server ->> Browser: Server Hello + Server Key Exchange
    Browser ->> Server: Client Key Exchange (Encrypted Handshake)
    Server ->> Browser: Encrypted Handshake (Finished)

    Note over Browser,Server: Encrypted HTTP Communication

    Browser ->> User: index.html
```

If we type **http://** instead of **https://** to visit a website that enforces HTTPS, the browser attempts to resolve the domain and redirects the user to the webserver hosting the target website. A request is sent to port **80** first, which is the unencrypted HTTP protocol. The server detects this and redirects the client to secure HTTPS port **443** instead. This is done via the **301 Moved Permanently** response code, which we will discuss in an upcoming section.

Depending on the circumstances, an attacker may be able to perform an HTTP downgrade attack, which downgrades HTTPS communication to HTTP, making the data transferred in clear-text. This is done by setting up a Man-In-The-Middle (MITM) proxy to transfer all traffic through the attacker's host without the user's knowledge.

### cURL for HTTPS

cURL should automatically handle all HTTPS communication standards and perform a secure handshake and then encrypt and decrypt data automatically. However, if we ever contact a website with an invalid SSL certificate or an outdated one, then cURL by default would not proceed with the communication to protect against the earlier mentioned MITM attacks:

```bash
$ curl https://inlanefreight.com

curl: (60) SSL certificate problem: Invalid certificate chain
More details here: https://curl.haxx.se/docs/sslcerts.html
...SNIP...
```

We may face such an issue when testing a local web application or with a web application hosted for practice purposes, as such web applications may not yet have implemented a valid SSL certificate. To skip the certificate check with cURL, we can use the -k flag:

```bash
$ curl -k https://inlanefreight.com

<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html><head>
...SNIP...
```

### HTTP Request <a name='web_requests_http_request' />

Let's start by examining the following example HTTP request:

`
GET /users/login.html HTTP/1.1 <br />
Host: inlanefreight.com <br />
User-Agent: Mozilla/5.0 (Ubuntu; Linux x86_64;) Firefox/78.0 <br />
Accept: text/html,application/xhtml+xml,application/xml <br />
Accept-Language: en-US,en;q=0.5 <br />
Accept-Encoding: gzip, deflate <br />
Content-Type: text/html; charset=UTF-8 <br />
Connection: close <br />
Cookie: PHPSESSID=c4ggt4jull9obt7aupa55o8vbf <br />
Upgrade-Insecure-Requests: 1 <br />
Cache-Control: max-age=0 <br />
`

The image above shows an HTTP GET request to the URL: `http://inlanefreight.com/users/login.html`


The first line of any HTTP request contains three main fields 'separated by spaces':

| Field | Example | Description |
| Method | GET | The HTTP method or verb, which specifies the type of action to perform. |
| Path | /users/login.html | The path to the resource being accessed. This field can also be suffixed with a query string (e.g. **?username=user**). |
| Version | HTTP/1.1 | The third and final field is used to denote the HTTP version. |

The next set of lines contain HTTP header value pairs, like Host, User-Agent, Cookie, and many other possible headers. These headers are used to specify various attributes of a request. The headers are terminated with a new line, which is necessary for the server to validate the request. Finally, a request may end with the request body and data.

HTTP version 1.X sends requests as clear-text, and uses a new-line character to separate different fields and different requests. HTTP version 2.X, on the other hand, sends requests as binary data in a dictionary form.

### HTTP Response <a name='web_requests_http_response' />


