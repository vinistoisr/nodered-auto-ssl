Running this stack will result in a node-red V12 container, accessilble at https://nodered.yourdomain.com, over SSL port 443 with a free certificate provided by Let's Encrypt, automatically managed by Traefik V2.2 web proxy. Traefik will completely manage the cert, including renewals, as well as the proxied ingress from your public facing port 443 to the node-red container on port 1880. 

A traefik Dashboard will be presented at https://traefik.yourdomain.com, using traefik itself as its proxy as described above. 

Only the most basic security is provided by this stack: a source IP whitelist. Further protection of the traefik dashboard and node-red UI is up to you. 


Deployment requirements:

1. You own a domain that you have pointed to cloudflare (free account is ok)

2. You have a wildcard 'A' record in cloudflare pointing to your public IP address:

```
A              *          222.222.222.222             Auto
```

3. You have a cloudflare API zone token with the correct permissions and scope:

```
Edit zone DNS	Zone.Zone Settings, Zone.Zone, Zone.DNS	All zones
```

3. port 80 and 443 in your router are forwarded to your docker host from a public ip, and your host has those ports opened in its firewall.

4. You have deployed swarm mode on your docker host(s)

```docker swarm init```

5. You have created a docker overlay network named 'traefik-public'

6. Supply the required env vars to the stack and deploy:

```
docker stack deploy -c docker-compose.yml nodered
```

When you successfully get a lets-encrypt staging cert, you can comment out the staging line in its entirety, delete the stack, and deploy the stack again. 

You will need to hard-refresh your browser for it to pick up your new cert (some browsers are bad for this - use incognito mode to verify your cert)

Some routers will need a dns record added for accessing your site from inside your network. 

Coming Next:

- Oauth w/ google login
