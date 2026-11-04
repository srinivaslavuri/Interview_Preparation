## Q41. What is Load Balancing in Nginx?
* Load balancing is the process of distributing incoming client requests across multiple backend servers to improve application performance, scalability, and availability. Nginx supports different load-balancing methods such as Round Robin, Least Connections, and IP Hash.
* ఒక application ని రెండు లేదా అంతకంటే ఎక్కువ Tomcat servers లో deploy చేశాము అనుకుందాం.

ఉదాహరణకు:
- Tomcat 1 – 192.168.1.20:8080
- Tomcat 2 – 192.168.1.21:8080
- Tomcat 3 – 192.168.1.22:8080
ఇప్పుడు 1000 users application access చేస్తున్నారు.

అందరూ ఒకే Tomcat server కి వెళ్లకుండా Nginx ఆ requests ని మూడు servers మధ్య distribute చేస్తుంది.

దీనినే Load Balancing అంటారు.
Advantages:
1. Traffic distribution.
2. Better performance.
3. High availability.
4. Reduced load on individual servers.
5. Scalability.

## Q42. What are the different Load Balancing algorithms in Nginx?
* Nginx supports different load-balancing methods, including Round Robin, Weighted Round Robin, Least Connections, and IP Hash. These methods distribute client requests across upstream backend servers based on different criteria.
Nginx లో traffic distribute చేయడానికి different methods ఉంటాయి.

ముఖ్యమైనవి:
1. Round Robin.
2. Weighted Round Robin.
3. Least Connections.
4. IP Hash.
ప్రతి method ఎలా పనిచేస్తుందో చూద్దాం.
Algorithm	                            Working
Round Robin                 	వరుస క్రమంలో requests పంపుతుంది
Weighted Round Robin        	Server weight ఆధారంగా requests పంపుతుంది
Least Connections	            తక్కువ active connections ఉన్న server కి పంపుతుంది
IP Hash	Client IP               ఆధారంగా server select చేస్తుంది

## Q43. What is Round Robin Load Balancing?
* Round Robin is the default load-balancing method in Nginx. It distributes incoming requests sequentially across the configured backend servers.
* Round Robin అంటే ఒక request ఒక server కి, తర్వాత request ఇంకొక server కి పంపించడం.

Example:

మన దగ్గర రెండు Tomcat servers ఉన్నాయి.
- Server 1 – 8080
- Server 2 – 8081
Requests ఇలా distribute అవుతాయి:
Request         	Backend
Request 1	        Server 1
Request 2	        Server 2
Request 3	        Server 1
Request 4	        Server 2
Request 5	        Server 1
Request 6       	Server 2
- Configuration:
upstream backend {
    server 192.168.1.20:8080;
    server 192.168.1.21:8081;
}

server {
    listen 80;

    location / {
        proxy_pass http://backend;
    }
}

- Important: Round Robin is the default method when no other balancing method is specified. It distributes requests, not necessarily equal numbers of simultaneous active sessions.

## Q44. What is Weighted Round Robin?
* Weighted Round Robin distributes requests based on the weight assigned to each backend server. Servers with higher weights receive a larger share of incoming requests.
* మన దగ్గర రెండు servers ఉన్నాయని అనుకుందాం.

కానీ వాటి hardware capacity ఒకేలా లేదు.
- Server 1 – 8 GB RAM, 4 CPU.
- Server 2 – 16 GB RAM, 8 CPU.
Server 2 capacity ఎక్కువగా ఉంది.

అందుకే Server 2 కి ఎక్కువ requests ఇవ్వవచ్చు.

Configuration:
upstream backend {
    server 192.168.1.20:8080 weight=1;
    server 192.168.1.21:8081 weight=3;
}
అంటే సాధారణంగా ప్రతి 4 requests లో 1 request Server 1 కి, 3 requests Server 2 కి పంపబడతాయి
Weight distribution example
Server 1        25%

Server 2        75%
When do we use it?

Different hardware capacity ఉన్న backend servers ఉన్నప్పుడు ఉపయోగిస్తాము.

## Q45. What is Least Connections Load Balancing?
Least Connections is a load-balancing method that sends incoming requests to the backend server with the fewest active connections. It is useful when requests have different processing times.
Least Connections అంటే ఏ server మీద ప్రస్తుతం తక్కువ active connections ఉన్నాయో ఆ server కి కొత్త request పంపిస్తుంది.

Example:
- Server 1 – 100 active connections.
- Server 2 – 30 active connections.
- Server 3 – 50 active connections.
ఇప్పుడు కొత్త request వస్తే Server 2 కి పంపుతుంది.

Configuration:
upstream backend {
    least_conn;

    server 192.168.1.20:8080;
    server 192.168.1.21:8081;
    server 192.168.1.22:8082;
}
When do we use it?

కొన్ని requests ఎక్కువ సమయం తీసుకుంటాయి, మరికొన్ని requests త్వరగా complete అవుతాయి.
అలాంటి applications లో Least Connections ఉపయోగపడుతుంది.
Interview point: Nginx selects the server with the fewest active connections, considering weights when configured.

## Q46. What is IP Hash Load Balancing?
* IP Hash is a load-balancing method that uses the client's IP address to determine which backend server receives the request. It can help maintain client-to-server consistency, but it does not guarantee application session persistence in every situation.
IP Hash అంటే Client IP address ఆధారంగా backend server select చేయడం.

Example:

Client IP: 192.168.1.100

ఈ client request వచ్చినప్పుడు సాధారణంగా అదే backend server కి పంపుతుంది.

మరొక client:

192.168.1.101

వేరే backend server కి వెళ్లవచ్చు.

Configuration:
upstream backend {
    ip_hash;

    server 192.168.1.20:8080;
    server 192.168.1.21:8081;
}
Why do we use it?

కొన్ని applications లో user session ఒక backend server లో maintain అవుతుంది.

అలాంటి సందర్భంలో client requests ఒకే backend కి వెళ్లడం ఉపయోగకరంగా ఉంటుంది.

Important: IP Hash అనేది perfect session persistence కాదు. NAT, proxy IPs, backend membership changes వంటి కారణాలతో routing మారవచ్చు. Shared sessions లేదా dedicated session management మరింత reliable.

## Q47. What is an Upstream Block in Nginx?
* An upstream block defines a group of backend servers that can be used by Nginx for load balancing and reverse proxying. It allows us to manage multiple backend servers under a single logical name.
* Upstream Block అంటే మన backend servers అన్నింటినీ ఒక group గా define చేయడం.

ఉదాహరణకు:

మన దగ్గర 3 Tomcat servers ఉన్నాయి.
upstream tomcat_backend {
    server 192.168.1.20:8080;
    server 192.168.1.21:8080;
    server 192.168.1.22:8080;
}
ఇక్కడ tomcat_backend అనేది upstream group name.

ఇప్పుడు proxy_pass లో అన్ని IP addresses మళ్లీ రాయాల్సిన అవసరం లేదు.
server {
    listen 80;

    location / {
        proxy_pass http://tomcat_backend;
    }
}
Advantages:
- Backend management easy.
- Load balancing support.
- Multiple servers configure చేయవచ్చు.
- Backend server changes easy అవుతాయి.

## Q48. How do you configure two backend servers in Nginx?
* We can configure two backend servers using the upstream directive. We define both backend IP addresses and ports in the upstream block and reference that group using proxy_pass.
* ఇది చాలా ముఖ్యమైన practical interview question.

మన architecture:
- Nginx – 192.168.1.16
- Tomcat 1 – 192.168.1.20:8080
- Tomcat 2 – 192.168.1.21:8080
Step 1: Configure upstream
upstream tomcat_backend {
    server 192.168.1.20:8080;
    server 192.168.1.21:8080;
}
Step 2: Configure server block
server {
    listen 80;

    server_name example.com;

    location / {
        proxy_pass http://tomcat_backend;

        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
Step 3: Validate configuration ==> nginx -t
Step 4: Reload ==> systemctl reload nginx
Step 5: Test ==> curl -I http://localhost
తెలుగులో request flow:
1. User application access చేస్తాడు.
2. Request Nginx కి వస్తుంది.
3. Nginx upstream block లో backend servers చూస్తుంది.
4. Configured load-balancing method ప్రకారం server select చేస్తుంది.
5. Backend response Nginx ద్వారా user కి వెళ్తుంది.

## Q49. How does Nginx handle a backend server failure?
* In the open-source Nginx version, passive health checking is available. If a backend server fails to respond or returns certain configured unsuccessful responses, Nginx can mark it temporarily unavailable based on parameters such as max_fails and fail_timeout and try another eligible backend server.
* మన దగ్గర రెండు Tomcat servers ఉన్నాయని అనుకుందాం.
- Tomcat 1 – Running.
- Tomcat 2 – Down.
User request Tomcat 2 కి వెళ్లి connection failure వస్తే, Nginx configured failure handling ఆధారంగా ఆ server ని కొంతసేపు unavailable గా mark చేసి మరో eligible server ని try చేయవచ్చు.

Configuration:
upstream backend {
    server 192.168.1.20:8080 max_fails=3 fail_timeout=30s;
    server 192.168.1.21:8080 max_fails=3 fail_timeout=30s;
}
ఇక్కడ:
- max_fails=3 – 30 seconds window లో 3 unsuccessful attempts వచ్చినప్పుడు server temporarily unavailable గా mark అవుతుంది.
- fail_timeout=30s – Failure counting window మరియు temporary unavailability period ని specify చేస్తుంది.
Important: ఇది application-level health monitoring కాదు. Backend recovery check మరియు actual availability behavior Nginx version/configuration మీద ఆధారపడి ఉంటుంది.


## Q50. What are Active and Passive Health Checks?
* Passive health checks monitor backend failures during actual client requests. Active health checks periodically send separate health-check requests to backend servers to verify their availability. Native active health checks are available in Nginx Plus, while open-source Nginx supports passive health checks.
* ఇవి రెండూ backend server healthy గా ఉందా లేదా తెలుసుకోవడానికి ఉపయోగిస్తాము.
1. Passive Health Check

User actual request పంపినప్పుడు backend response ఆధారంగా health check జరుగుతుంది.

Example:
- User request Tomcat 1 కి వెళ్తుంది.
- Tomcat 1 connection refused ఇస్తుంది.
- Nginx failure record చేస్తుంది.
- Configured threshold reach అయితే server ని temporarily unavailable గా mark చేస్తుంది.
2. Active Health Check

Nginx Plus periodically backend కి separate health-check request పంపుతుంది.

ఉదాహరణకు ప్రతి 5 seconds కి health endpoint check చేస్తుంది.
Nginx
  |
  |---- Health check ----> Tomcat 1
  |
  |---- Health check ----> Tomcat 2

Tomcat 1 healthy గా ఉందా, Tomcat 2 healthy గా ఉందా అని monitor చేస్తుంది.
Passive                             	Active
Actual user requests ఆధారంగా       	Separate health requests
Open-source Nginx supports	        Native support in Nginx Plus
Failure తర్వాత detect అవుతుంది    	Periodically detect చేయవచ్చు


## Q51. What is proxy_pass in Nginx?
* The proxy_pass directive is used to forward client requests from Nginx to an upstream backend server or server group. It is commonly used when Nginx acts as a reverse proxy.
* proxy_pass అనేది Nginx నుంచి backend server కి request forward చేయడానికి ఉపయోగిస్తాము.

Example:

మన దగ్గర:
- Nginx – Port 80.
- Tomcat – Port 8080.
Configuration:
server {
    listen 80;

    location / {
        proxy_pass http://192.168.1.20:8080;
    }
}
ఇప్పుడు user Nginx port 80 కి request పంపిస్తాడు.
Nginx ఆ request ని Tomcat port 8080 కి forward చేస్తుంది.
Remember: proxy_pass is the most important directive in reverse proxy configuration.

## Q52. How do you configure Nginx as a Reverse Proxy?
* To configure Nginx as a reverse proxy, we define a server block, specify the listening port, configure a location block, and use proxy_pass to forward requests to the backend server.
Step-by-step చూద్దాం.
Step 1: Open configuration  ==> vi /etc/nginx/conf.d/app.conf
Step 2: Add configuration
server {
    listen 80;

    server_name example.com;

    location / {
        proxy_pass http://192.168.1.20:8080;

        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
Step 3: Test ==> nginx -t
Step 4: Reload ==> systemctl reload nginx
Step 5: Test backend connectivity ==> curl -I http://192.168.1.20:8080
Step 6: Test through Nginx ==>curl -I http://localhost
Request flow:
User → Nginx:80 → Tomcat:8080 → Nginx → User.

## Q53. How do you configure Nginx with Tomcat?
* We configure Nginx as a reverse proxy in front of Tomcat. Nginx listens on HTTP or HTTPS ports and forwards application requests to the Tomcat HTTP connector port, such as 8080.
ఇది Middleware Administrator కి చాలా ముఖ్యమైన real-time question.
మన దగ్గర Nginx మరియు Tomcat రెండు servers ఉన్నాయి అనుకుందాం.
Server           	IP              	Port
Nginx	        192.168.1.16	        80
Tomcat 1	    192.168.1.20	        8080
Tomcat 2	    192.168.1.21        	8080

upstream tomcat_backend {
    server 192.168.1.20:8080;
    server 192.168.1.21:8080;
}

server {
    listen 80;

    server_name example.com;

    location / {
        proxy_pass http://tomcat_backend;

        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
Advantages:
1. Nginx handles incoming requests.
2. Tomcat servers run Java applications.
3. Load balancing distributes requests.
4. Nginx can handle SSL termination.
5. Backend servers can be kept off direct public access.
Interview point: Nginx does not execute Java applications. Tomcat executes the Java applications and Nginx forwards the requests.

## Q54. What is the purpose of proxy_set_header?
* The proxy_set_header directive is used to pass or modify HTTP headers when forwarding requests from Nginx to backend servers. It helps preserve client and request information such as the original host, client IP address, and original protocol.
Nginx request ని Tomcat కి forward చేస్తున్నప్పుడు కొన్ని important client information headers ద్వారా పంపిస్తుంది.

ముఖ్యమైనవి:
1. Host
proxy_set_header Host $host;
Original requested hostname backend కి పంపుతుంది.

2. X-Real-IP
proxy_set_header X-Real-IP $remote_addr;
Nginx కి connect అయిన client IP address ని పంపుతుంది.

3. X-Forwarded-For
proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
Proxy chain లో client IP information preserve చేస్తుంది.

4. X-Forwarded-Proto
proxy_set_header X-Forwarded-Proto $scheme;
Original request HTTP ద్వారా వచ్చిందా HTTPS ద్వారా వచ్చిందా అనే information పంపిస్తుంది.

Full example:
location / {
    proxy_pass http://192.168.1.20:8080;

    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
}
Real-time example:

User HTTPS ద్వారా application access చేశాడు.
Nginx HTTPS connection terminate చేసి HTTP ద్వారా Tomcat కి request పంపింది.
X-Forwarded-Proto header లేకపోతే backend application original request HTTPS అని తెలుసుకోలేకపోవచ్చు.

## Q55. How do you troubleshoot Reverse Proxy issues?
* First, I will check whether the Nginx service is running and validate its configuration. Then I will verify connectivity between Nginx and the backend server using curl, telnet, or nc. I will check Nginx access and error logs, backend application logs, firewall rules, and proxy timeout settings to identify the root cause.

* ఇది చాలా important production troubleshooting question.

మన application Nginx ద్వారా access అవ్వడం లేదు అనుకుందాం.
ఈ steps follow అవుతాము.
Step 1: Check Nginx status
systemctl status nginx
Step 2: Check configuration
nginx -t
Step 3: Check Nginx port
ss -lntp | grep nginx
Step 4: Check backend connectivity
curl -I http://192.168.1.20:8080
Step 5: Check backend port connectivity
nc -zv 192.168.1.20 8080
Step 6: Check Nginx error log           tail -f /var/log/nginx/error.log
Step 7: Check access log                tail -f /var/log/nginx/access.log
Step 8: Check Tomcat logs               tail -f $CATALINA_BASE/logs/catalina.out
Step 9: Check firewall                  firewall-cmd --list-all
Step 10: Check timeout configuration
proxy_connect_timeout 10s;
proxy_send_timeout 60s;
proxy_read_timeout 60s;
ఈ values application requirements ఆధారంగా configure చేయాలి.

## 56 How do you configure SSL certificates in Nginx?
* మనం ఒక website ని HTTPS ద్వారా access చేయాలంటే Nginx server లో SSL/TLS certificate configure చేయాలి.
ఉదాహరణకు మన application architecture:
Client HTTPS request పంపినప్పుడు Nginx certificate ఉపయోగించి secure TLS connection establish చేస్తుంది.

Step 1: Certificate files తీసుకోవాలి

సాధారణంగా మనకు రెండు ముఖ్యమైన files ఉంటాయి:
File	Purpose
fullchain.pem	Server certificate + intermediate certificates
privkey.pem	Private key
ఈ files Certificate Authority (CA) లేదా certificate provider నుంచి వస్తాయి.
Step 2: Certificate files server లో place చేయాలి
ఉదాహరణకు:
sudo mkdir -p /etc/nginx/ssl
Certificate files ని ఈ directory లో ఉంచాలి. ==> ls -l /etc/nginx/ssl/
Expected files: 
fullchain.pem
privkey.pem
Step 3: Nginx configuration చేయాలి
Configuration file:
sudo vi /etc/nginx/conf.d/application.conf
Configuration:
server {
    listen 443 ssl;
    server_name example.com;

    ssl_certificate /etc/nginx/ssl/fullchain.pem;
    ssl_certificate_key /etc/nginx/ssl/privkey.pem;

    ssl_protocols TLSv1.2 TLSv1.3;

    location / {
        proxy_pass http://127.0.0.1:8080;

        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
Step 4: Configuration validate చేయాలి ==> sudo nginx -t
Output:  ==> syntax is ok   test is successful
Step 5: Nginx reload చేయాలి        ==> sudo systemctl reload nginx
Step 6: HTTPS check చేయాలి ==> curl -Iv https://example.com
Browser లో కూడా https://example.com open చేసి verify చేయాలి.

"To configure SSL certificates in Nginx, first I obtain the SSL certificate and private key from a Certificate Authority. I place the certificate files in a secure directory on the Nginx server. Then I configure the listen directive with SSL on port 443 and specify the certificate and private key paths using ssl_certificate and ssl_certificate_key. I also configure supported TLS protocols. Finally, I validate the configuration using nginx -t, reload Nginx, and verify HTTPS connectivity."

## What is an SSL certificate?
* SSL certificate అనేది ఒక digital certificate. ఇది website యొక్క identity ని verify చేయడానికి ఉపయోగపడుతుంది.
ఉదాహరణకు:
మీరు bank website open చేస్తున్నారని అనుకుందాం.
https://www.examplebank.com
మీ browser ఆ website నిజంగా ఆ domain కి చెందిన server నుంచే వచ్చిందా లేదా అని certificate ద్వారా verify చేస్తుంది.

Certificate లో ముఖ్యమైన information:
1. Domain name – ఏ website కి certificate ఇచ్చారు.
2. Public key – Secure communication కోసం ఉపయోగించే public key.
3. Issuer – ఏ Certificate Authority certificate ఇచ్చింది.
4. Validity – Certificate ఎప్పుడు start అవుతుంది, ఎప్పుడు expire అవుతుంది.
5. Digital signature – Certificate ని issuer authenticate చేసినట్లు verify చేయడానికి ఉపయోగపడుతుంది.
SSL certificate ఎందుకు అవసరం?
- Server identity verify చేయడానికి.
- Client మరియు server మధ్య communication secure చేయడానికి.
- Data encryption మరియు integrity అందించడానికి.
- Fake server లేదా impersonation ప్రమాదాన్ని తగ్గించడానికి.
Example:

Certificate లేకుండా HTTPS connection establish చేయడానికి browser కి అవసరమైన trusted identity verification లభించకపోవచ్చు. అందువల్ల certificate errors రావచ్చు.

Interview Answer – English:

"An SSL certificate is a digital certificate issued by a Certificate Authority. It verifies the identity of a website or server and contains information such as the domain name, public key, issuer, validity period, and digital signature. It helps establish secure HTTPS communication between clients and servers."

## What is the difference between SSL and TLS?
SSL అంటే Secure Sockets Layer.

TLS అంటే Transport Layer Security.

ఈ రెండూ client మరియు server మధ్య secure communication కోసం రూపొందించిన cryptographic protocols.

కానీ SSL పాత protocol. TLS అనేది దాని modern replacement.
SSL	                                    TLS
Secure Sockets Layer	        Transport Layer Security
Older protocol	                Modern protocol
SSL 2.0 and 3.0 are obsolete	TLS 1.2 and 1.3 are commonly used
Less secure	                    Improved security
Should not be enabled	        Recommended for production

Simple example:
మీరు Nginx configuration లో ఇలా పెడితే:
ssl_protocols TLSv1.2 TLSv1.3;
Nginx TLS 1.2 మరియు TLS 1.3 connections మాత్రమే accept చేయడానికి configure అవుతుంది.

Important interview point:

చాలామంది ఇప్పటికీ SSL certificate అని అంటారు. కానీ technical గా మనం ఉపయోగించేది TLS protocol.

Interview Answer – English:

"SSL stands for Secure Sockets Layer, whereas TLS stands for Transport Layer Security. Both are cryptographic protocols used to secure communication between clients and servers. SSL is an older and deprecated protocol. TLS is its modern replacement, and TLS 1.2 or TLS 1.3 is recommended for production environments."

## What is an SSL handshake?
* SSL handshake అని సాధారణంగా అంటాం. Technical గా ఇది TLS handshake.
Client మరియు server మధ్య secure connection establish అయ్యే process ని TLS handshake అంటారు.
ఉదాహరణకు మీరు browser లో:
https://example.com
open చేశారు.

అప్పుడు ఈ steps జరుగుతాయి.
1. Client Hello
   
   Browser తన supported TLS versions, cipher suites మరియు ఇతర connection parameters పంపిస్తుంది.
2. Server Hello
   
   Nginx supported options లో ఒక TLS version మరియు cipher suite select చేసి response ఇస్తుంది.
3. Server Certificate
   
   Nginx తన SSL certificate ని browser కి పంపిస్తుంది.
4. Certificate Verification
   
   Browser certificate validity, domain name, trust chain మరియు expiry date check చేస్తుంది.
5. Key Establishment
   
   Client మరియు server secure key agreement ద్వారా session keys establish చేసుకుంటాయి.
6. Encrypted Communication
   
   Handshake complete అయిన తర్వాత encrypted HTTP communication ప్రారంభమవుతుంది.

Simple real-time example:

మీరు online banking website open చేసినప్పుడు, browser మరియు server ముందుగా secure connection establish చేసుకుంటాయి. ఆ తర్వాత login credentials వంటి data encrypted రూపంలో పంపబడుతుంది.

Interview Answer – English:

"The TLS handshake is the process of establishing a secure connection between a client and a server. During the handshake, the client and server negotiate the TLS version and cipher suite. The server presents its certificate, which the client validates. Both sides establish session keys, and then encrypted communication begins."

## What is SSL termination?
SSL termination అంటే client నుంచి వచ్చే HTTPS connection ని Nginx దగ్గర terminate చేయడం.
అంటే client నుంచి వచ్చిన encrypted traffic ని Nginx decrypt చేసి backend application కి forward చేస్తుంది.
Example architecture:
ఇక్కడ:
- Client → Nginx: HTTPS.
- Nginx → Tomcat: HTTP.
- SSL/TLS connection Nginx దగ్గర terminate అవుతుంది.
ఎందుకు ఉపయోగిస్తాం?
1. SSL certificate management ఒకే చోట చేయవచ్చు.
2. Backend servers మీద SSL configuration అవసరం తగ్గుతుంది.
3. Centralized TLS management ఉంటుంది.
4. Nginx reverse proxy మరియు load balancing కూడా నిర్వహించగలదు.
Important: Nginx నుంచి backend కి HTTP వాడితే ఆ network segment లో traffic encrypted కాదు. Security requirement బట్టి backend కి కూడా HTTPS ఉపయోగించాలి.

Interview Answer – English:

"SSL termination is the process of decrypting incoming HTTPS traffic at the Nginx server. Nginx handles the TLS connection from the client and forwards the decrypted requests to backend servers using HTTP or HTTPS. It simplifies certificate management and centralizes SSL processing."


## What is the purpose of ssl_certificate?
ssl_certificate అనేది Nginx configuration directive.
దీని ద్వారా Nginx కి server certificate file location చెప్తాం.
Example:
ssl_certificate /etc/nginx/ssl/fullchain.pem;
ఇక్కడ:
- ssl_certificate – Configuration directive.
- /etc/nginx/ssl/fullchain.pem – Certificate file path.
Nginx HTTPS connection establish చేసేటప్పుడు ఈ certificate ని client కి పంపిస్తుంది.
సాధారణంగా fullchain.pem లో server certificate తో పాటు intermediate certificates ఉంటాయి.

Interview Answer – English:

"The ssl_certificate directive specifies the path of the server's SSL/TLS certificate file. Nginx presents this certificate to clients during the TLS handshake. In production, we generally configure the full certificate chain."

## What is the purpose of ssl_certificate_key?
ssl_certificate_key directive ద్వారా private key file location configure చేస్తాం.
Example:
Certificate లో public key ఉంటుంది. దానికి సంబంధించిన private key server దగ్గర secure గా ఉంటుంది.

Certificate vs Private Key:
Certificate	Private Key
Client కి పంపిస్తారు	Server దగ్గర secret గా ఉంచుతారు
Public information	Confidential information
Domain identity verify చేయడంలో సహాయపడుతుంది	TLS cryptographic operations లో ఉపయోగపడుతుంది
fullchain.pem	privkey.pem

Important: Private key leak అయితే certificate security compromise కావచ్చు. కాబట్టి proper file permissions అవసరం.

Interview Answer – English:

"The ssl_certificate_key directive specifies the location of the private key associated with the server certificate. It is used during TLS cryptographic operations. The private key must be kept confidential and protected with proper file

## How do you configure HTTP-to-HTTPS redirection?
## How do you redirect HTTP traffic to HTTPS?
* HTTP default port 80.
HTTPS default port 443.
ఒక user ఇలా website open చేశాడని అనుకుందాం:
http://example.com
మనకు automatic గా ఇలా మారాలి:
https://example.com
దీనికోసం Nginx లో HTTP server block configure చేస్తాం.

Configuration:
server {
    listen 80;
    server_name example.com www.example.com;

    return 301 https://$host$request_uri;
}
ఇప్పుడు HTTPS కోసం separate server block configure చేయాలి:
server {
    listen 443 ssl;
    server_name example.com www.example.com;

    ssl_certificate /etc/nginx/ssl/fullchain.pem;
    ssl_certificate_key /etc/nginx/ssl/privkey.pem;

    location / {
        proxy_pass http://127.0.0.1:8080;
    }
}
ఈ directives అర్థం:
Directive	            Purpose
listen 80	            HTTP requests accept చేస్తుంది
server_name	            Domain name define చేస్తుంది
return 301	            Permanent redirect
$host	                Requested hostname
$request_uri	        Original path and query string
listen 443 ssl	        HTTPS requests accept చేస్తుంది
Practical example:
Browser లో:  http://example.com/login
Request వస్తే Nginx ఇలా redirect చేస్తుంది:     https://example.com/login
Check command:
curl -I http://example.com

* Interview Answer – English:

"To redirect HTTP traffic to HTTPS, I create a server block listening on port 80 and configure a permanent 301 redirect using the return directive. I use $host and $request_uri to preserve the original hostname, path, and query string. I also configure a separate HTTPS server block listening on port 443 with the SSL certificate and private key."

## What is the purpose of proxy_set_header?
* Nginx reverse proxy గా పనిచేస్తున్నప్పుడు client నుంచి వచ్చిన request ని backend Tomcat లేదా WebLogic కి forward చేస్తుంది.
కానీ backend కి original client information అవసరం ఉంటుంది.
ఆ information ని HTTP headers రూపంలో backend కి పంపడానికి proxy_set_header ఉపయోగిస్తాం.
Configuration:
location / {
    proxy_pass http://127.0.0.1:8080;

    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
}
ప్రతి header వివరాలు:

1. Host
proxy_set_header Host $host;
Original hostname ని backend కి పంపిస్తుంది.

2. X-Real-IP
proxy_set_header X-Real-IP $remote_addr;
Nginx కి connection చేసిన client IP address ని backend కి పంపిస్తుంది.

3. X-Forwarded-For
proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
Proxy chain లో client IP information ని preserve చేస్తుంది.

4. X-Forwarded-Proto
proxy_set_header X-Forwarded-Proto $scheme;
Original request HTTP లేదా HTTPS ద్వారా వచ్చిందో backend కి తెలియజేస్తుంది.

Real-time example:

Client → Nginx → Tomcat

Client IP 192.168.1.50 అనుకుందాం.

Tomcat కి Nginx ద్వారా request వెళ్తుంది. Backend logs లో Nginx IP మాత్రమే కాకుండా original client IP తెలుసుకోవడానికి forwarded headers ఉపయోగపడతాయి.

Interview Answer – English:

"The proxy_set_header directive is used to pass HTTP headers from Nginx to backend servers. It helps preserve the original hostname, client IP address, proxy chain, and original request protocol. It is commonly used in reverse proxy configurations with Tomcat and WebLogic."

## What is the difference between $host and $http_host?
ఈ రెండూ Nginx variables. రెండూ hostname information కి సంబంధించినవే. కానీ వాటి behaviour కొంచెం different.

1. $host
- Nginx normalized hostname.
- Request line లేదా Host header నుంచి hostname తీసుకుంటుంది.
- అవసరమైతే matching server name fallback గా ఉపయోగిస్తుంది.
- సాధారణంగా port number ఉండదు.
2. $http_host
- Client పంపించిన original Host request header value.
- Port number కూడా ఉండవచ్చు.
- Host header లేకపోతే empty అవుతుంది.
Example:

Client request:
GET /login HTTP/1.1
Host: example.com:8080
Nginx variables:
Variable	Value
$host	example.com
$http_host	example.com:8080

Practical configuration:
location / {
    proxy_pass http://backend;

    proxy_set_header Host $host;
}
ఇక్కడ $host ఉపయోగించడం వల్ల backend కి normalized hostname పంపిస్తాం.

Interview Answer – English:

"$host contains the normalized hostname derived from the request, with a fallback to the configured server name when needed. $http_host contains the original Host header sent by the client, which may include the port number and can be empty if the header is missing."

