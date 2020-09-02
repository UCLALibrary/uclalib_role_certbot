uclalib_role_certbot [![Build Status](https://travis-ci.com/UCLALibrary/uclalib_role_certbot.svg?branch=master)](https://travis-ci.com/UCLALibrary/uclalib_role_certbot)
=========

Ansible role for installing and configuring the [EFF Certbot client](https://certbot.eff.org/)

Requirements
------------

By default, certbot is configured to work with the Let's Encrypt service. However, the UCLA Library uses the Sectigo Certificate Management service.

This role provides the ability to:
- install certbot with a Sectigo ACME Account - it is expected you already have a registered valid ACME Account with Sectigo.
- install certbot with a Let's Encrypt Account - it is expected you already have a Let's Encrypt Account.

The EPEL repo should already be installed/enabled.
If you are on a RHEL system, the `rhel-server-optional-rpms` repo should already be enabled.

Certificate requests and renewals relay on the certbot standalone authenticator. This means certbot will provision a temporary web server on port 80 to verify the domain name association with the certificate.

Before initiating a certificate request, be sure there is no running process that is already bound to HTTP Port 80 on the host. This will cause the certificate request process to fail.

**NOTE**: If you are running this role within UCLA Library's infrastructure, reference our Confluence page - [Sectigo ACME Certbot Using Ansible](https://docs.library.ucla.edu/x/SCyUD)

Role Variables
--------------

Reference `defaults/main.yml` to review default values.

Variables associated with certbot installation and ACME account set-up:
- `certbot_install_pkgs` - defines the set of certbot packages necessary to complete the installation.
- `certbot_reg_type` - defines the registration type for the certbot client (acceptable value are: `sectigo` or `letsencrypt`).
- `certbot_baseconfig_path` - defines the path to certbot configuration and certificate files (default: `/etc/letsencrypt`).
- `certbot_acme_accnt_path` - defines the path to ACME account registration data.
- `acme_accnt_email` - defines the email address associated with the ACME account.
- `acme_accnt_url` - defines the URL associated with the ACME account provider (e.g. sectigo or letsencrypt).
- `acme_accnt_keyid` - defines the unique identifier for the ACME account.
- `acme_accnt_hmacid` - defines the HMAC key identifier for the ACME account.
- `acme_accnt_hash` - defines the hash value for the directory name that will store ACME account files.
- `acme_accnt_priv_key_json` - contains the json content for the ACME account's private key.

Variables associated with initiating a certificate request and set-up of renewal cron job:
- `certbot_certificates` - dictionary that defines the certificate names and associated domain names for each certificate - example:
    ```
    certbot_certificates:
      certname1:
        domains:
          - example1.library.ucla.edu
          - example2.library.ucla.edu
      certname2:
        domains:
          - example3.library.ucla.edu
          - example4.library.ucla.edu
      certname3:
        domains:
          - example5.library.ucla.edu
          - example6.library.ucla.edu
    ```
- `certbot_renewhook` - OPTIONAL - defines the command that should run after a successful certificate renewal
- `certbot_make_cert_request` - defines whether a certificate request should be made (`true`/`false`)
- `certbot_request_type` - defines the type of certificate request process to use (values are `standard` or `haproxy` - default is `standard`)
    * `standard` - certificates will be requested/packaged using the standard (default) method
    * `haproxy` - certificates will be requested/packaged for use by HAProxy
- `certbot_http_challenge_port` - defines the certbot HTTP challenge port (applicable only when using `certbot_request_type: haproxy`)
- `certbot_http_access_restricted` - defines whether the system running certbot will have restricted access to http (`true`/`false`)
- `certbot_cron_renew_user` - defines the user the cron job should run as
- `certbot_cron_renew_hour` - defines the hour parameter for when the cron job should run
- `certbot_cron_renew_minute` - defines the minute parameter for when the cron job should run
- `certbot_cron_renew_command` - defines the command to use to initiate a certbot certificate renewal


Minimum set of variables that need to be defined to use this role for setting up the ACME account and making a certificate request:
```
certbot_reg_type

acme_accnt_email
acme_accnt_keyid
acme_accnt_hmacid
acme_accnt_hash
acme_accnt_priv_key_json


certbot_make_cert_request
certbot_request_type
certbot_domains
cerbot_certname
```

Tags
----

Use the following tags to run specific role functions:

- `cert-setup` - causes the role to only set-up the ACME account on the host system
- `cert-req` - causes the role to only make a certificate request

Dependencies
------------

None.

Example Playbook
----------------
To run the role all by itself your playbook could look something like this:

```
---

- name: uclalib_certbot.yml
  become: yes
  become_method: sudo
  hosts: all

  roles:
    - { role: uclalib_role_certbot }
```

To run the role along-side other roles as a part of a larger playbook - for example one that installs a website running in Apache HTTPD:

```
---

- name: uclalib_webapp.yml
  become: yes
  become_method: sudo
  hosts: all

  roles:
    - { role: uclalib_role_certbot }
    - { role: uclalib_role_apache }
    - { role: uclalib_role_webapp }
```
Ensure the certbot role runs before the web server role - this will ensure port 80 is available for certbot to run its domain authentication checks. If something else is using port 80, certificate requests will fail.
