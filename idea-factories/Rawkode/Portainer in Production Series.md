[playlist](https://www.youtube.com/watch?v=VrtBN5pQMo4&list=PLz0t90fOInA7aRYTguuowv6qrNsMB-zxM)
[github](https://github.com/bronifty/portainer-in-production.git)

### TLS
1) launch ubuntu vm with access to ports 22 80 443 (aws & gcp)
2) map ip address to domain in cloudflare
3) install deps (snap certbot docker)
##### Certbot Method
1) install certbot, symbollically link it to path and run the standalone server
2) run portainer with the volumes mapped to the cert and key
##### Caddy Method
1) update Caddyfile email and hostname
2) remove existing portainer docker container
3) run portainer without the mapping on 8000 and 943
4) run caddy to reverse proxy from 80 and 443 to portainer on 9443

### Install Portainer on a VPS
#### AWS EC2 
- Free tier eligible - t2.micro 
	- defaults

#### GCP
- Free tier: -     
    1 non-preemptible `e2-micro` VM instance per month in one of the following US regions:
    -   Oregon: `us-west1`
    -   Iowa: `us-central1`
    -   South Carolina: `us-east1`
-   30 GB-months standard persistent disk


