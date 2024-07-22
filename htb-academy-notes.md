# HackTheBox Academy Notes

## Web Requests

HTTP communication consists of a client and a server, where the client requests the server for a resource. The server processes the requests and returns the requested resource. The default port for HTTP communication is port 80, though this can be changed to any other port, depending on the web server configuration. The same requests are utilized when we use the internet to visit different websites. We enter a Fully Qualified Domain Name (FQDN) as a Uniform Resource Locator (URL) to reach the desired website, like www.hackthebox.com.

### URL

Resources over HTTP are accessed via a URL, which offers many more specifications than simply specifying a website we want to visit. Let's look at the structure of a URL:

`http://admin:password@inlanefreight.com:80/dashboard.php?login=true#status`

`http` - *scheme*
`admin:password` - *user*
`inlanefreight.com` - *host*
`80` - *port*
`dashboard.php` - *path*
`login=true` - *query string*
`status` - *fragment*

