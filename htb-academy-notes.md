# HackTheBox Academy Notes

## Table of Contents
1. [Web Requests](#web_requests)

## Web Requests <a name='web_requests'></a>

HTTP communication consists of a client and a server, where the client requests the server for a resource. The server processes the requests and returns the requested resource. The default port for HTTP communication is port 80, though this can be changed to any other port, depending on the web server configuration. The same requests are utilized when we use the internet to visit different websites. We enter a **Fully Qualified Domain Name (FQDN)** as a **Uniform Resource Locator (URL)** to reach the desired website, like www.hackthebox.com.

### URL

Resources over HTTP are accessed via a URL, which offers many more specifications than simply specifying a website we want to visit. Let's look at the structure of a URL:

`http://admin:password@inlanefreight.com:80/dashboard.php?login=true#status`

| Component | Example | Description |
| --- | --- | --- |
| Scheme | `http://` | This is used to identify the protocol being accessed by the client, and ends with a colon and a double slash (://) |
| User Info | `admin:password@` | This is an optional component that contains the credentials (separated by a colon :) used to authenticate to the host, and is separated from the host with an at sign (@) |
| Host | `inlanefreight.com` | The host signifies the resource location. This can be a hostname or an IP address |
| Port | `"80` | The Port is separated from the Host by a colon (:). If no port is specified, http schemes default to port 80 and https default to port 443 |
| Path | `/dashboard.php` | This points to the resource being accessed, which can be a file or a folder. If there is no path specified, the server returns the default index (e.g. index.html). |
| Query String | `?login=true` | The query string starts with a question mark (?), and consists of a parameter (e.g. login) and a value (e.g. true). Multiple parameters can be separated by an ampersand (&). |
| Fragment | `#status` | Fragments are processed by the browsers on the client-side to locate sections within the primary resource (e.g. a header or section on the page). |

### /etc/hosts file

Our browsers usually first look up records in the local '/etc/hosts' file, and if the requested domain does not exist within it, then they would contact other DNS servers. We can use the '/etc/hosts' to manually add records to for DNS resolution, by adding the IP followed by the domain name.

### cURL

cURL (client URL) is a command-line tool and library that primarily supports HTTP along with many other protocols. This makes it a good candidate for scripts as well as automation, making it essential for sending various types of web requests from the command line, which is necessary for many types of web penetration tests.

We can send a basic HTTP request to any URL by using it as an argument for cURL, as follows:
```bash
$ curl inlanefreight.com
```

