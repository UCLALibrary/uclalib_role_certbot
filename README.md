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
- `certbot_domains` - defines the list of domains to be associated with the requested certificate
- `cerbot_certname` - defines the internal name certbot will use to reference/track the certificate
- `certbot_renewhook` - OPTIONAL - defines the command that should run after a successful certificate renewal
- `certbot_make_cert_request` - defines whether a certificate request should be made (`true`/`false`)
- `certbot_http_access_restricted` - defines whether the system running certbot will have restricted access to http (`true`/`false`)
- `certbot_certificate_path` - defines the filesystem path to the requested/issued certificate
- `certbot_request_command` - defines the command to use to initiate a certbot certificate request
- `certbot_cron_renew_user` - defines the user the cron job should run as
- `certbot_cron_renew_hour` - defines the hour parameter for when the cron job should run
- `certbot_cron_renew_minute` - defines the minute parameter for when the cron job should run
- `certbot_cron_renew_command` - defines the command to use to initiate a certbot certificate renewal

Dependencies
------------

None.

Example Playbook
----------------

```
---

- name: uclalib_certbot.yml
  become: yes
  become_method: sudo
  hosts: all

  roles:
    - { role: uclalib_role_certbot }
```
