YES BRUHH 🔥 100% POSSIBLE! You can run portals on Server 1 and Server 2 using the same domain and different URL paths.

Your exact scenario:

* Server 1: `https://appdev01.maricoapps.biz/coprarates`

* Server 2: `https://appdev01.maricoapps.biz/gatepass`

### Solution: Reverse Proxy (Nginx / Load Balancer)

Users / Developers

`appdev01.maricoapps.biz`

DNS points to the proxy, not both app servers directly

Nginx Reverse Proxy

Path-based routing

`/coprarates`

Server 1

CopraRates portal

`/gatepass`

Server 2

GatePass portal

### How to implement it

* DNS: Keep one DNS record for `appdev01.maricoapps.biz`, pointing to the reverse proxy.

* Nginx: Configure path-based routing to the appropriate backend server.

* Servers: Host each portal on its respective server, with network access from the proxy.

* HTTPS: Configure the certificate on the proxy and preserve the required host headers.

Example Nginx configuration:

```nginx
server {
    listen 443 ssl;
    server_name appdev01.maricoapps.biz;

    location /coprarates/ {
        proxy_pass http://SERVER1_IP/;
        proxy_set_header Host $host;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }

    location /gatepass/ {
        proxy_pass http://SERVER2_IP/;
        proxy_set_header Host $host;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```

Important: This is the basic routing example. The trailing slashes in `location` and `proxy_pass` strip the respective path prefix. If either application generates root-relative URLs such as `/main.js`, redirects, or authentication callbacks, extra configuration may be required.

### One critical question

Where is Nginx currently installed?

* A) On a separate proxy server

* B) On Server 1

* C) On Server 2

* D) No Nginx yet — currently using IIS

Tell me this first. I'll give you the safest implementation for your existing setup without disrupting the other portals.
