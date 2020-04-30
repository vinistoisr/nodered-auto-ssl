Running this stack will result in a node-red container, pulled from nodered/node-red:latest accessilble at https://nodered.yourdomain.com, over SSL port 443 with a free certificate provided by Let's Encrypt, automatically managed by Traefik V2.2 web proxy. Traefik will completely manage the cert, including renewals, as well as the proxied ingress from your public facing port 443 to the node-red container on port 1880. 

A traefik Dashboard will be presented at https://traefik.yourdomain.com, using traefik itself as its proxy as described above. 

To deployments are available:

docker-compose.yml: Only the most basic security is provided by this stack: a source IP whitelist. Further protection of the traefik dashboard and node-red UI is up to you. 

oauth-docker-compose.yml:  This stack includes thomseddon/traefik-forward-auth, which is a A minimal forward authentication service that provides OAuth/SSO login and authentication for the traefik reverse proxy/load balancer. In other words, login to node-red with your google creds. 


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

or, for oauth, add the config file via portainer or manually, making sure the contents are as per this repo

```
docker config create traefik-auth.toml
```
then:
```
docker stack deploy -c oauth-docker-compose.yml nodered
```

When you successfully get a lets-encrypt staging cert, you can comment out the staging line in its entirety, delete the stack, and deploy the stack again. 

You will need to hard-refresh your browser for it to pick up your new cert (some browsers are bad for this - use incognito mode to verify your cert)

Some routers will need a dns record added for accessing your site from inside your network. 

I recommend you start without oauth first and get a valid cert. Then when you re-deploy with oauth there are fewer variables to troubleshoot!
