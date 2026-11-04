---
title: CHPC Website
---

This is the documentation of the webserver serving CHPC website. The only special 
thing here is that some parts of the website are only visible with an LDAP login 
(on `ipa01.hpc.sns.it`) from user with a certain group, following this table:

|Path|LDAP Group|
|---|---|
|`/doc/staff`|`hpcstaff`|
|`/doc`|`hpcusers`|
|everything else|public|


## Architecture

```
Browser --HTTPS (public cert (self-signed by Caddy))--> Caddy (caddy01) --HTTPS (self-signed)--> HTTPD/Apache (chpcwebsite)
```

| Component | Role |
|---|---|
| Hugo | Generates the static site from a git repo |
| Apache httpd | Serves the files; enforces LDAP group access on some directories |
| FreeIPA | Identity source (users and groups) |
| Caddy | Public reverse proxy; terminates the public TLS connection |

Public name: `dev-website.chpc.sns.it`
Internal IPs: 
- chpcwebsite: 10.1.10.251
- caddy01: 192.168.210.247, 10.1.10.247

## Apache (AlmaLinux)

- Packages: `httpd`, `mod_ssl`, `mod_ldap` (provides `mod_authnz_ldap`).
- Listens on **443 only** which is the endpoint of Caddy communication
- The firewall allows 443 and 22 for SSH.
- Site root: `/var/www/chpc_website`, owned by `deploy:apache`, directories `750`, files `640`.
- SELinux: currently permissive. Before enforcing, check `ausearch -m avc -ts recent`. These are likely needed:
  - `httpd_sys_content_t` label on `/var/www/hugo` (`semanage fcontext` + `restorecon`)
  - `setsebool -P httpd_can_connect_ldap 1`

### Files

| File  | Purpose |
|---|---|
| `/etc/httpd/conf.d/chpc_website.conf` | Virtual host (HTTPS) |
| `/etc/httpd/conf.d/ldap-auth.inc` | Shared LDAP auth settings (`Include`d, not auto-loaded) |
| `/etc/httpd/conf.d/ldap-global.conf` | CA trust for LDAPS and LDAP cache settings |
| `/etc/pki/tls/certs/hugo.crt`, `/etc/pki/tls/private/hugo.key` | Self-signed certificate used between Caddy and Apache |

### Access model

- The whole site is public (`Require all granted`).
- Two subdirectories are restricted, each to one IPA group (`Require ldap-group ...`).
  - Not logged in: `401`
  - Valid user outside the group: `403`
- Users authenticate with Basic auth over HTTPS. Apache binds as the logging-in user (`AuthLDAPInitialBindAsUser`, `AuthLDAPSearchAsUser`, `AuthLDAPCompareAsUser`), because IPA does not allow anonymous reads of group membership.

### LDAP (FreeIPA) essentials

- Server: `ldaps://ipa01.hpc.sns.it`, base `dc=hpc,dc=sns,dc=it`
- Users: `uid=<name>,cn=users,cn=accounts,dc=hpc,dc=sns,dc=it`
- Groups: `cn=<group>,cn=groups,cn=accounts,dc=hpc,dc=sns,dc=it` (members are DNs: `AuthLDAPGroupAttributeIsDN on`)
- CA: `/etc/ipa/ca.crt`, set with `LDAPTrustedGlobalCert CA_BASE64 ...` (global config, restart Apache after a change)
- Group membership is checked on the direct `member` attribute. For nested groups use `AuthLDAPSubGroupDepth` or `Require ldap-attribute memberOf=...`.
- Command-line tests: use `ldapsearch -x` (without `-x` it tries Kerberos) and `LDAPTLS_CACERT=/etc/ipa/ca.crt`.

## Caddy

```caddy
dev-website.chpc.sns.it {
    reverse_proxy https://10.1.10.251:443 {
        transport http {
            tls_server_name dev-website.chpc.sns.it
            tls_trusted_ca_certs /etc/caddy/certs/host_dev-website.chpc.sns.it.crt
        }
    }
}
```
{{< callout type="warning">}}
On Caddy 2.8 or newer, `tls_trust_pool file { pem_file }` replaces `tls_trusted_ca_certs`.
{{< /callout >}}
- `/etc/caddy/certs/host_dev-website.chpc.sns.it.crt` must be the same certificate that Apache serves.
- `tls_server_name` must match a name in the certificate's SAN.
- The public certificate is managed by Caddy itself (or by whatever `tls` directive is configured).

## Self-signed certificate (Apache <-> Caddy)

- SAN must contain `DNS:dev-website.chpc.sns.it` and the Apache IP. A certificate without a SAN is rejected by Caddy.
- Validity: 825 days. Renew 365 days.
- **After every renewal:** copy the new `hugo.crt` to the Caddy host and **restart** Caddy.
- Check: `openssl x509 -in hugo.crt -noout -subject -ext subjectAltName`

## Deployment

1. Ansible updates the git repo as `deploy` and runs `hugo --minify` with `--destination /var/www/hugo --cleanDestinationDir`.
2. Ansible sets `deploy:apache`, `u=rwX,g=rX,o=` and the SELinux label on `/var/www/hugo`.
3. Hugo `baseURL` must be `https://dev-website.chpc.sns.it/`.

Content in the restricted sections can leak through Hugo-generated public pages (taxonomies, section lists, RSS, sitemap, search indexes). Use `_build: {list: never}` for private sections and check `public/` after a build.