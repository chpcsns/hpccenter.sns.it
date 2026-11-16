---
title: Caddy SSL
---

`caddy01` is the reverse proxy for HPC websites. It should proxy every website from `10.0.0.0/8` to `192.168.0.0/16`.

Currently it serves:
* grafana.chpc.sns.it
* dev-website.chpc.sns.it
* jupyterhub.chpc.sns.it

## SSL
To have SSL connections from the 192.168.0.0/24 to caddy, we used [Harica](https://cm.harica.gr) certificates that are signed 
from a reconized CA. We decided to have a single certificate for the wildcard domain `*.chpc.sns.it`.

The certificate is issued accessing the Harica website with SNS login, and follow the [instructions](https://tcs-docs.aai.garr.it/emissioneCertificati/#certificati-server-ssl-e-ssl-igtf). At the end of the procedure the private key (encrypted with a password - Mattia has it) can be downloaded. After
the certificate is issued (you have to ask to ICT ppl) you can also download the certificate. 

Once you have both the key and the certificate you have to:
1. place certificate on `caddy01` (we used `/etc/caddy/certs/harica_cert.pem`)
2. place encrypted private key on `caddy01` (we used `/etc/caddy/certs/harica_privatekey.pem`)
3. decrypt private key: `openssl pkey -in /etc/caddy/certs/harica_privatekey.pem -out /etc/caddy/certs/harica_privatekey.dec.pem`
4. set correct ownership and permission for both files (`root:caddy 640`)
5. add the line `tls /etc/caddy/certs/harica_cert.pem /etc/caddy/certs/harica_privatekey.dec.pem` to caddy conf and restart it.


