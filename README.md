# traefik-demo-with-app

a project that will show a working traefik reverse proxy on a virtual private server. It will use Let's Encrypt
to generate wildcard domain certificates for this as well. 
We are using Version 2 of the Traefik API which is completely different then ALL 1.X versions so this ONLY 
applies to version to of the Traefik executable.

This example project is meant as a Guideline project to get you started and will not run "As-Is".   

You can add as many external docker-compose project as you want as long as they are run on the same server 
just copy the exampleapp directory and modifed to run ANY docker image.



## Required
1) docker installed
2) docker-compose installed
3) This project requires your to change the configuration everywhere there is a ***yourdomainname.com*** to the domain
   you are working with. Search and replace should work well

## VPS Virtual Private Server Example

This will deploy the traefik V2 to a host either local or on a remote virtual server. It will then listen for new containers to be 
started and route as needed via the settings in the app container's docker-compose.yml.  

You can verify operations with

https://monitor.yourdomainname.com

username: admin
pass: admin123

# testing

### you need control of a domain in order to do this

Required
1) API Key to your DNS server or AUTH Token
2) acme.json in 644 mode. in bash terminal  touch acme.json ; chmod 600 acme.json
3) external docker network   **docker network create traefik-net** should return a hash when done



### Testing this locally
add your hostnames to you local /etc/hosts
127.0.0.1        monitor.yourdomainname.com example.yourdomainname.com   

 

### Deploying the traefik executable 
1) monitor.yourdomainname.com will direct you to the traefik admin api. login with admin:admin123
2) test.yourdomainname.com will load the nginx base server and you should get the standard landing page

```bash
 from project main directory to make sure its not running
 % cd vps
 % docker-compose -p "traefik-demoapp" -f docker-compose.yml down 

 % docker-compose -p "traefik-demoapp" -f docker-compose.yml up  -d 
```

goto http://monitor.yourdomainname.com  this should redirect in browser to https://monitor.yourdomainname.com

This should load the traefik-admin after it load the certificates about 30 secs first time
 
executing docker logs traefikv2 should give you and example of whats going on with the certs. The traefikv2.toml
is configured for DEBUG mode to start out. Change this to INFO later   

 [log]   
   level = "DEBUG"   
   format = "common"
```bash
traefikv2    | time="2019-12-12T22:32:43Z" level=debug msg="Domains [\"yourdomainname.com\" \"*.yourdomainname.com\"] need ACME certificates generation for domains \"yourdomainname.com,*.yourdomainname.com\"." providerName=first.acme
traefikv2    | time="2019-12-12T22:32:43Z" level=debug msg="Loading ACME certificates [yourdomainname.com *.yourdomainname.com]..." providerName=first.acme
traefikv2    | time="2019-12-12T22:32:43Z" level=debug msg="Creating middleware" entryPointName=web routerName=redirecttohttps@file middlewareName=pipelining middlewareType=Pipelining serviceName=noop
traefikv2    | time="2019-12-12T22:32:43Z" level=debug msg="Creating load-balancer" routerName=redirecttohttps@file serviceName=noop entryPointName=web
traefikv2    | time="2019-12-12T22:32:43Z" level=debug msg="Creating server 0 http://192.168.0.1" routerName=redirecttohttps@file serviceName=noop serverName=0 entryPointName=web
traefikv2    | time="2019-12-12T22:32:43Z" level=debug msg="Added outgoing tracing middleware noop" entryPointName=web routerName=redirecttohttps@file middlewareName=tracing middlewareType=TracingForwarder
traefikv2    | time="2019-12-12T22:32:43Z" level=debug msg="Creating middleware" routerName=redirecttohttps@file middlewareName=redirecthttps@file middlewareType=RedirectScheme entryPointName=web
traefikv2    | time="2019-12-12T22:32:43Z" level=debug msg="Setting up redirection to https " entryPointName=web routerName=redirecttohttps@file middlewareName=redirecthttps@file middlewareType=RedirectScheme
traefikv2    | time="2019-12-12T22:32:43Z" level=debug msg="Adding tracing to middleware" routerName=redirecttohttps@file middlewareName=redirecthttps@file entryPointName=web
traefikv2    | time="2019-12-12T22:32:43Z" level=debug msg="Creating middleware" middlewareName=traefik-internal-recovery middlewareType=Recovery entryPointName=web
traefikv2    | time="2019-12-12T22:32:43Z" level=debug msg="No entryPoint defined for this router, using the default one(s) instead: [web web-secure traefik]" routerName=redirecthttps@file
traefikv2    | time="2019-12-12T22:32:43Z" level=error msg="the service is missing on the router" entryPointName=traefik routerName=redirecthttps@file
traefikv2    | time="2019-12-12T22:32:43Z" level=debug msg="Creating middleware" middlewareName=traefik-internal-recovery middlewareType=Recovery entryPointName=traefik
traefikv2    | time="2019-12-12T22:32:43Z" level=debug msg="Added outgoing tracing middleware api@internal" entryPointName=web-secure routerName=dashboard@file middlewareName=tracing middlewareType=TracingForwarder
traefikv2    | time="2019-12-12T22:32:43Z" level=debug msg="Creating middleware" middlewareName=auth@file middlewareType=BasicAuth entryPointName=web-secure routerName=dashboard@file
traefikv2    | time="2019-12-12T22:32:43Z" level=debug msg="Adding tracing to middleware" routerName=dashboard@file entryPointName=web-secure middlewareName=auth@file
traefikv2    | time="2019-12-12T22:32:43Z" level=error msg="the service is missing on the router" entryPointName=web-secure routerName=redirecthttps@file
traefikv2    | time="2019-12-12T22:32:43Z" level=debug msg="Creating middleware" entryPointName=web-secure middlewareName=traefik-internal-recovery middlewareType=Recovery
traefikv2    | time="2019-12-12T22:32:43Z" level=error msg="the service is missing on the router" entryPointName=web routerName=redirecthttps@file
traefikv2    | time="2019-12-12T22:32:43Z" level=debug msg="Creating middleware" entryPointName=web middlewareName=traefik-internal-recovery middlewareType=Recovery
traefikv2    | time="2019-12-12T22:32:43Z" level=debug msg="No entryPoint defined for this router, using the default one(s) instead: [web web-secure traefik]" routerName=redirecthttps@file
traefikv2    | time="2019-12-12T22:32:43Z" level=debug msg="No default certificate, generating one"
traefikv2    | time="2019-12-12T22:32:44Z" level=debug msg="Building ACME client..." providerName=first.acme
traefikv2    | time="2019-12-12T22:32:44Z" level=debug msg="https://acme-staging-v02.api.letsencrypt.org/directory" providerName=first.acme
traefikv2    | time="2019-12-12T22:32:45Z" level=info msg=Register... providerName=first.acme
traefikv2    | time="2019-12-12T22:32:45Z" level=debug msg="legolog: [INFO] acme: Registering account for admin@yourdomainname.com"
traefikv2    | time="2019-12-12T22:32:45Z" level=debug msg="Using DNS Challenge provider: godaddy" providerName=first.acme
traefikv2    | time="2019-12-12T22:32:45Z" level=debug msg="legolog: [INFO] [yourdomainname.com, *.yourdomainname.com] acme: Obtaining bundled SAN certificate"
traefikv2    | time="2019-12-12T22:32:45Z" level=debug msg="legolog: [INFO] [*.yourdomainname.com] AuthURL: https://acme-staging-v02.api.letsencrypt.org/acme/authz-v3/26803798"
traefikv2    | time="2019-12-12T22:32:45Z" level=debug msg="legolog: [INFO] [yourdomainname.com] AuthURL: https://acme-staging-v02.api.letsencrypt.org/acme/authz-v3/26803799"
traefikv2    | time="2019-12-12T22:32:45Z" level=debug msg="legolog: [INFO] [*.yourdomainname.com] acme: use dns-01 solver"
traefikv2    | time="2019-12-12T22:32:45Z" level=debug msg="legolog: [INFO] [yourdomainname.com] acme: Could not find solver for: tls-alpn-01"
traefikv2    | time="2019-12-12T22:32:45Z" level=debug msg="legolog: [INFO] [yourdomainname.com] acme: Could not find solver for: http-01"
traefikv2    | time="2019-12-12T22:32:45Z" level=debug msg="legolog: [INFO] [yourdomainname.com] acme: use dns-01 solver"
traefikv2    | time="2019-12-12T22:32:45Z" level=debug msg="legolog: [INFO] [*.yourdomainname.com] acme: Preparing to solve DNS-01"
traefikv2    | time="2019-12-12T22:32:46Z" level=debug msg="legolog: [INFO] [*.yourdomainname.com] acme: Trying to solve DNS-01"
traefikv2    | time="2019-12-12T22:32:46Z" level=debug msg="legolog: [INFO] [*.yourdomainname.com] acme: Checking DNS record propagation using [127.0.0.11:53]"
traefikv2    | time="2019-12-12T22:32:46Z" level=debug msg="legolog: [INFO] Wait for propagation [timeout: 2m0s, interval: 2s]"
traefikv2    | time="2019-12-12T22:32:46Z" level=debug msg="Delaying 30000000000 rather than validating DNS propagation now."
traefikv2    | time="2019-12-12T22:33:19Z" level=debug msg="legolog: [INFO] [*.yourdomainname.com] The server validated our request"
traefikv2    | time="2019-12-12T22:33:19Z" level=debug msg="legolog: [INFO] [*.yourdomainname.com] acme: Cleaning DNS-01 challenge"
traefikv2    | time="2019-12-12T22:33:20Z" level=debug msg="legolog: [INFO] sequence: wait for 1m0s"
traefikv2    | time="2019-12-12T22:34:20Z" level=debug msg="legolog: [INFO] [yourdomainname.com] acme: Preparing to solve DNS-01"
traefikv2    | time="2019-12-12T22:34:20Z" level=debug msg="legolog: [INFO] [yourdomainname.com] acme: Trying to solve DNS-01"
traefikv2    | time="2019-12-12T22:34:20Z" level=debug msg="legolog: [INFO] [yourdomainname.com] acme: Checking DNS record propagation using [127.0.0.11:53]"
traefikv2    | time="2019-12-12T22:34:20Z" level=debug msg="legolog: [INFO] Wait for propagation [timeout: 2m0s, interval: 2s]"
traefikv2    | time="2019-12-12T22:34:20Z" level=debug msg="Delaying 30000000000 rather than validating DNS propagation now."
traefikv2    | time="2019-12-12T22:34:54Z" level=debug msg="legolog: [INFO] [yourdomainname.com] The server validated our request"
traefikv2    | time="2019-12-12T22:34:54Z" level=debug msg="legolog: [INFO] [yourdomainname.com] acme: Cleaning DNS-01 challenge"
traefikv2    | time="2019-12-12T22:34:54Z" level=debug msg="legolog: [INFO] [yourdomainname.com, *.yourdomainname.com] acme: Validations succeeded; requesting certificates"
traefikv2    | time="2019-12-12T22:34:56Z" level=debug msg="legolog: [INFO] [yourdomainname.com] Server responded with a certificate."
traefikv2    | time="2019-12-12T22:34:56Z" level=debug msg="Certificates obtained for domains [yourdomainname.com *.yourdomainname.com]" providerName=first.acme
traefikv2    | time="2019-12-12T22:34:56Z" level=debug msg="Configuration received from provider first.acme: {\"http\":{},\"tls\":{}}" providerName=first.acme
traefikv2    | time="2019-12-12T22:34:56Z" level=debug msg="Adding certificate for domain(s) yourdomainname.com,*.yourdomainname"
traefikv2    | time="2019-12-12T22:34:56Z" level=debug msg="No default certificate, generating one"
traefikv2    | time="2019-12-12T22:34:56Z" level=debug msg="Creating middleware" serviceName=noop middlewareName=pipelining middlewareType=Pipelining entryPointName=web routerName=redirecttohttps@file
traefikv2    | time="2019-12-12T22:34:56Z" level=debug msg="Creating load-balancer" serviceName=noop entryPointName=web routerName=redirecttohttps@file
traefikv2    | time="2019-12-12T22:34:56Z" level=debug msg="Creating server 0 http://192.168.0.1" entryPointName=web routerName=redirecttohttps@file serviceName=noop serverName=0
traefikv2    | time="2019-12-12T22:34:56Z" level=debug msg="Added outgoing tracing middleware noop" entryPointName=web routerName=redirecttohttps@file middlewareType=TracingForwarder middlewareName=tracing
traefikv2    | time="2019-12-12T22:34:56Z" level=debug msg="Creating middleware" middlewareType=RedirectScheme entryPointName=web routerName=redirecttohttps@file middlewareName=redirecthttps@file
traefikv2    | time="2019-12-12T22:34:56Z" level=debug msg="Setting up redirection to https " middlewareName=redirecthttps@file middlewareType=RedirectScheme entryPointName=web routerName=redirecttohttps@file
```

NOTE: the included acme-example-generated.json is NOT valid. Its just an example. When project is properly configured with your domain name under your control it will generate a new acme.json


### Deploying the example nginx server executable 

```bash
 from project main directory to make sure its not running
 % cd exampleapp
 % docker-compose -p "example" -f docker-compose.yml down 

 % docker-compose -p "example" -f docker-compose.yml up  -d 
```


curl to http://example.yourdomainname.com should redirect to https://example.yourdomainname.com
If you are using the staging Let's encrypt server you will get a Certificate Exception
Commenting out the Server URL for certificates should fix this AFTER you delete all the data
in the current acme.json file. Let's encrypt has a rate limit on the production server so you do not want
to do you initial testing with production. Using staging server URL first.   
"#      caServer = "https://acme-staging-v02.api.letsencrypt.org/directory" << this will force production server"

**This is what you should see after the curl**   

Welcome to nginx!
If you see this page, the nginx web server is successfully installed and working. Further configuration is required.

For online documentation and support please refer to nginx.org.
Commercial support is available at nginx.com.

Thank you for using nginx.

## NON DNS challenge Method for Let's Encrypt

Change the traefikv2.toml file section to these types if you do not want to use DNS challenge and wildcard certificates
These will NOT allow wildcard certifcates to be used

**HTTP-Challenge**
[certificatesResolvers]
  [certificatesResolvers.first]
    [certificatesResolvers.first.acme]
      email = "admin@yourdomain.com"
      storage = "acme.json"
    [certificatesResolvers.first.acme.httpchallenge]
      entryPoint = "web"

**HTTPS-Challenge**
[certificatesResolvers]
  [certificatesResolvers.first]
    [certificatesResolvers.first.acme]
      email = "admin@yourdomain.com"
      storage = "acme.json"
    [certificatesResolvers.first.acme.tlsChallenge]
      entryPoint = "web-secure"
      
### Notes on DNS Challenge
When using the DNS challenge, The acme client will contact your DNS provider and make a TXT record entry, which a 
hash value. It will then wait (controlled by delayBeforeCheck in traefikv2.toml) to verify that it can look this up via the name server to make sure then entry was made
At that point it will then generate a certificate and delete the TXT record if it can. Some DNS providers have issues
when deleting but it not a long term issue and you can delete these on your own later via the DNS provider website admin.
in order to force a new certificate to be generated you must clear out acme.json to 0 bytes OR delete the entry
in acme.json that hold the json body for that domain you want regenerated.