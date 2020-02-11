uclalib_role_certbot
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
