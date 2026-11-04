## 26. What is a Server Block in Nginx?
* A server block in Nginx is used to configure a virtual server. It defines how Nginx handles requests for a particular domain name or IP address and port.

* Server Block అంటే ఒకే Nginx server మీద multiple websites లేదా applications configure చేయడానికి ఉపయోగిస్తాము.

ఉదాహరణకు మన దగ్గర ఒకే Nginx server ఉంది. అందులో రెండు applications run చేయాలి.
- Application 1 – app1.example.com
- Application 2 – app2.example.com
వీటి కోసం separate server blocks create చేస్తాము.

Practical configuration:

server {
    listen 80;
    server_name app1.example.com;

    location / {
        proxy_pass http://192.168.1.20:8080;
    }
}

server {
    listen 80;
    server_name app2.example.com;

    location / {
        proxy_pass http://192.168.1.21:8080;
    }
}

ఇక్కడ ఏమవుతుంది?
- User app1 domain access చేస్తే first server block match అవుతుంది.
- User app2 domain access చేస్తే second server block match అవుతుంది.
Interview point: Server blocks are similar to virtual hosts in Apache.

##  Q27. What is a Location Block in Nginx?
* A location block is used to define how Nginx processes requests based on the URI path. It can serve static content, forward requests to backend servers, or apply specific access rules.
Location Block అనేది User request చేసే URL path ఆధారంగా ఏ action తీసుకోవాలో decide చేస్తుంది.

ఉదాహరణకు:

మన application లో మూడు paths ఉన్నాయనుకుందాం.
- /images/
- /api/
- /admin/
వీటికి different configurations ఇవ్వవచ్చు.

Practical configuration:

server {
    listen 80;
    server_name example.com;

    location /images/ {
        root /var/www/html;
    }

    location /api/ {
        proxy_pass http://192.168.1.20:8080;
    }

    location /admin/ {
        proxy_pass http://192.168.1.21:8080;
    }
}
తెలుగులో అర్థం:
- /images/ request వస్తే static files serve చేస్తుంది.
- /api/ request వస్తే backend 192.168.1.20 కి పంపుతుంది.
- /admin/ request వస్తే backend 192.168.1.21 కి పంపుతుంది.

## Q28. What is the difference between Server Block and Location Block?
* A server block defines a virtual server based on the requested domain and listening port, whereas a location block defines how requests are handled based on their URI paths within that server block.

* సింపుల్‌గా గుర్తుపెట్టుకోండి:
- Server Block = ఏ application లేదా domain request వచ్చింది?
- Location Block = ఆ application లో ఏ URL path request వచ్చింది?
Example:

https://example.com/api/users

ఇక్కడ:
- example.com → Server Block.
- /api/users → Location Block.


## Q29. What is the purpose of the Listen directive?
* The listen directive specifies the IP address and port on which the Nginx server block accepts incoming connections.

తెలుగులో వివరణ:

Listen directive అనేది Nginx ఏ port మీద requests accept చేయాలో చెబుతుంది.

Example:
server {
    listen 80;
    server_name example.com;
}

* ఇక్కడ Nginx port 80 మీద HTTP requests accept చేస్తుంది.

HTTPS example:
server {
    listen 443 ssl;
    server_name example.com;
}

* ఇక్కడ HTTPS requests port 443 మీద accept అవుతాయి.

Port 8080 configure చేయాలంటే:
server {
    listen 8080;
    server_name localhost;
}

## Q30. What is the purpose of the Server_name directive?
* The server_name directive defines the domain names or hostnames that a server block handles. Nginx uses it to select the appropriate server block based on the Host header of the incoming request.

* Server_name అంటే మన application కి సంబంధించిన domain name.

ఉదాహరణకు:
server {
    listen 80;
    server_name www.example.com;
}
* User browser లో:

http://www.example.com

Open చేస్తే Nginx Host header ఆధారంగా matching server block select చేస్తుంది.

Multiple domains configure చేయవచ్చు:
server {
    listen 80;

    server_name example.com
                www.example.com
                test.example.com;
}
* Important: DNS resolution కూడా correct గా ఉండాలి. Server_name configure చేసినంత మాత్రాన domain automatically work అవదు.

## Q31. What is the purpose of the Root directive?
* The root directive specifies the document root directory from which Nginx serves static files. Nginx appends the requested URI to the root path to locate the requested file.

* Root directive అనేది website files ఎక్కడ ఉన్నాయో Nginx కి చెబుతుంది
server {
    listen 80;

    location / {
        root /var/www/html;
        index index.html;
    }
}
* మన directory structure:
/var/www/html/
    index.html
    about.html
    contact.html
    User ఈ URL access చేస్తే:

http://example.com/about.html

Nginx ఈ file కోసం వెతుకుతుంది:

/var/www/html/about.html

Remember: Root directive document root path define చేస్తుంది.

## Q32. What is the purpose of the Index directive?
* The index directive specifies the default file that Nginx serves when a client requests a directory without specifying a filename.

* User website open చేసినప్పుడు specific file name ఇవ్వకపోతే ఏ file display చేయాలో Index directive decide చేస్తుంది.

Example:
server {
    listen 80;

    root /var/www/html;

    index index.html index.htm;
}
User:

http://example.com

అని open చేస్తే Nginx ముందుగా index.html కోసం చూస్తుంది.

అది లేకపోతే index.htm కోసం చూస్తుంది.

Important: Index file లేకపోతే మరియు directory listing configure చేయకపోతే 403 రావచ్చు.

## Q33. What is the purpose of the Location directive?
* The location directive is used to match request URIs and apply specific configurations such as proxying, static file serving, access control, and caching.
Location directive URL path ఆధారంగా requests ని handle చేస్తుంది.

Nginx లో ముఖ్యమైన location matching types:
Syntax	                           Purpose

location /	General             prefix match
location = /login	            Exact match
location ^~ /images/        	Prefer this prefix over regex matches
location ~ \.php$	            Case-sensitive regex
location ~* \.jpg$	            Case-insensitive regex

Practical example:

server {
    listen 80;

    location = /login {
        return 200 "Login page";
    }

    location /api/ {
        proxy_pass http://127.0.0.1:8080;
    }

    location /images/ {
        root /var/www/html;
    }
}
- /login వస్తే exact match.
- /api/ వస్తే backend కి forward చేస్తుంది.
- /images/ వస్తే static content serve చేస్తుంది.
Interview point: Nginx uses location matching rules and precedence to select the applicable location block

######    Q34. What is the difference between Root and Alias directives?
* The root directive appends the complete request URI to the specified root directory, whereas the alias directive replaces the matched location prefix with the specified directory path.

* ఇది చాలా ముఖ్యమైన question. రెండు directives కూడా files location specify చేయడానికి ఉపయోగిస్తాము. కానీ path mapping వేర్వేరుగా ఉంటుంది.
1. Root example
location /images/ {
    root /var/www/html;
}
User request:

http://example.com/images/photo.jpg

Nginx వెతికే actual file:
/var/www/html/images/photo.jpg

2. Alias example
location /images/ {
    alias /data/pictures/;
}
అదే URL request వస్తే Nginx వెతికే actual file:
/data/pictures/photo.jpg


##  Q35. What is the purpose of Include directive?
* The include directive allows us to include configuration files into the main Nginx configuration. It helps organize configurations, avoid duplication, and manage multiple virtual hosts efficiently.

* Include directive అంటే ఒక configuration file లో మరో configuration file ని include చేయడం.

ఉదాహరణకు main configuration:

/etc/nginx/nginx.conf

దీనిలో ఇలా ఉంటుంది:
http {
    include /etc/nginx/conf.d/*.conf;
}
ఇప్పుడు /etc/nginx/conf.d/ లో ఉన్న .conf files ని include చేస్తుంది.

Example directory:
/etc/nginx/
    nginx.conf

/etc/nginx/conf.d/
    app1.conf
    app2.conf
    test.conf

Benefits:
- Configuration management easy అవుతుంది.
- Multiple applications separate గా maintain చేయవచ్చు.
- Main configuration file clean గా ఉంటుంది.
- Changes manage చేయడం easy అవుతుంది.

##  Q36. How do you configure multiple websites in Nginx?
* We can host multiple websites on a single Nginx server by creating separate server blocks with different server_name values. Nginx selects the appropriate server block based on the incoming Host header.

* ఒకే Nginx server లో multiple websites run చేయవచ్చు.

Example:
- Website 1 – app1.example.com
- Website 2 – app2.example.com
- Website 3 – app3.example.com

server {
    listen 80;
    server_name app1.example.com;

    root /var/www/app1;
    index index.html;
}

server {
    listen 80;
    server_name app2.example.com;

    root /var/www/app2;
    index index.html;
}

server {
    listen 80;
    server_name app3.example.com;

    root /var/www/app3;
    index index.html;
}

తెలుగులో అర్థం:

- మూడు domains కి మూడు separate server blocks ఉన్నాయి.
- అన్ని ఒకే Nginx server మీద run అవుతాయి.
- Important: ప్రతి domain DNS record కూడా Nginx server IP కి point చేయాలి.

## Q37. How do you change the default Nginx port?
* We can change the default Nginx listening port by modifying the listen directive in the server block. After making the change, we validate the configuration and reload Nginx.

* Nginx default port 80.

దీనిని 8080 కి మార్చాలంటే configuration లో listen directive change చేయాలి.

Step 1: Open configuration
vi /etc/nginx/conf.d/default.conf

Step 2: Change port

- Before:
server {
    listen 80;
}
- After
server {
    listen 8080;
}

- Step 3: Test configuration  = nginx -t
- Step 4: Reload Nginx        = systemctl reload nginx
- Step 5: Verify port         = ss -lntp | grep 8080
- Step 6: Test                = curl -I http://localhost:8080
* Production point: Firewall rules మరియు SELinux policies కూడా అవసరమైతే update చేయాలి. SELinux enabled RHEL-based systems లో custom HTTP ports కి http_port_t permission అవసరం కావచ్చు.

## Q38. What is Virtual Hosting in Nginx?
* Virtual hosting allows multiple websites or applications to run on a single Nginx server using different domain names, IP addresses, or ports. It is implemented using server blocks.

* ఒకే physical server మీద multiple websites host చేయడాన్ని Virtual Hosting అంటారు.

ఉదాహరణకు:

మన దగ్గర ఒక Linux server ఉంది.

IP: 192.168.1.16

అందులో:
- www.company1.com
- www.company2.com
- www.company3.com
మూడు websites run చేయవచ్చు.

ప్రతి website కి separate server block configure చేస్తాము.

Remember: Virtual Hosting is achieved through server blocks in Nginx.

## Q39. How do you troubleshoot Nginx configuration errors?
* First, I will validate the configuration using nginx -t. If any syntax error occurs, I will check the file name and line number mentioned in the output. After correcting the issue, I will test the configuration again and reload Nginx.

* మనము Nginx configuration change చేశాము అనుకుందాం.

కానీ reload చేస్తున్నప్పుడు error వస్తోంది.

అప్పుడు ఈ procedure follow అవుతాము.

Step 1: Test configuration  ==> nginx -t
Step 2: Check detailed configuration  ==>  nginx -T
Step 3: Check error logs  ==> tail -100 /var/log/nginx/error.log
Step 4: Check syntax
Common issues:
- Missing semicolon.
- Incorrect brackets.
- Invalid directive.
- Wrong file path.
- Duplicate listen configuration.
- Incorrect SSL certificate path.
Step 5: Correct and test again  ==> nginx -t
Step 6: Reload ==>  systemctl reload nginx
* Real-time interview answer:

"I will first validate the Nginx configuration using nginx -t. If the test fails, I will identify the file and line number from the error message, correct the configuration, validate it again, and then reload the service."

## Q40. How do you organize Nginx configuration files?
* We organize Nginx configuration files by keeping global settings in nginx.conf and maintaining individual application or virtual host configurations in separate files. We use the include directive to load these configuration files.

* Production environment లో అన్ని application configurations ఒకే file లో పెట్టడం కంటే separate files లో maintain చేయడం మంచిది.
    Example structure:
    /etc/nginx/
│
├── nginx.conf
│
├── conf.d/
│   ├── app1.conf
│   ├── app2.conf
│   └── app3.conf
│
├── mime.types
│
└── snippets/
    ├── ssl.conf
    └── proxy.conf
- nginx.conf:
http {
    include /etc/nginx/mime.types;
    include /etc/nginx/conf.d/*.conf;
}
Advantages:
1. Easy maintenance.
2. Application-wise configuration.
3. Easy troubleshooting.
4. Less configuration duplication.
5. Better change management.
Important: snippets directory is commonly used for reusable configuration fragments, but it must be included explicitly wherever needed.
