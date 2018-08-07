# devgateway.ha-letsencrypt-webserver

Set up a Pacemaker cluster with Nginx and Letsencrypt automatic renewal. Letsencrypt data are stored
in GlusterFS to replicate them between nodes. Pacemaker manages `letsencrypt` service which enables
or disables `update-letsencrypt` timer and its respective service. The latter runs a job of updating
Letsencrypt certificates using Certbot. This role uses Route53 plugin of Certbot.

## Required Variables

### `hlws_aws_key_id`

The access key ID of the account to manage Route53 host records. See the [Certbot Route53 plugin
documentation](https://certbot-dns-route53.readthedocs.io/en/latest/) for required permissions.

### `hlws_aws_secret`

The secret to this access key.

### `hlws_domains`

A list of domains to request wildcard certificates for. Do not include the leading asterisk.

### `hlws_letsencrypt_email`

An email associated with the domains at Letsencrypt.

### `hlws_virtual_ip`

The virtual IP address assigned to the current Nginx resource.

### `pacemaker_password`

The password to `pacemaker` user, who manages the cluster.

## Optional Variables

### `hlws_dhparam`

Path to randomized DH parameter.

Default: ``` /etc/pki/tls/misc/dhparam.pem ```

### `hlws_dhparam_bits`

Length of DH parameter in bits.

Default: 2048

### `hlws_gluster_brick`

Path to the brick in GlusterFS that will store Letsencrypt data.

Default: ``` /srv/brick/letsencrypt ```

### `hlws_gluster_volume`

Name of the GlusterFS volume for Letsencrypt data.

Default: ``` letsencrypt ```

### `hlws_letsencrypt_dir`

Directory where Certbot configs and certificates are stored.

Default: ``` /etc/letsencrypt ```

### `hlws_letsencrypt_log`

Logging path for Certbot.

Default: ``` /var/log/letsencrypt/letsencrypt.log ```

### `hlws_letsencrypt_staging`

Whether to request a staging certificate from Letsencrypt.

Default: *False*

### `hlws_nginx_reload_script`

Install path for the script to reload Nginx configuration.

Default: ``` /usr/local/sbin/reload-nginx.sh ```

### `hlws_ngx_defaults`

Default settings for [Nginx role](https://github.com/devgateway/ansible-role-nginx), see
`defaults/main.yml` for details.

### `hlws_ngx_settings`

Additional Nginx role settings as a dict, to be combined with the defaults (overriding them).

Default: `{}`

### `hlws_systemd_dir`

Path to custom Systemd units.

Default: ``` /etc/systemd/system ```

### `hlws_update_unit`

Name of the one-shot Systemd service and its timer.

Default: ``` update-letsencrypt ```

## Playbook Example

    ---
    - hosts:
        - alpha
        - bravo
      tasks:
        - name: Install HA Letsencrypt webserver
          include_role:
            name: devgateway.ha-letsencrypt-webserver
          vars:
            hlws_aws_key_id: AKIFGHAINYONTOUGJOC9
            hlws_aws_secret: nunokcevkiWamAvKopEitlyotIvbimDiheWroodd
            hlws_letsencrypt_email: hostmaster@example.org
            hlws_domains:
              - example.org
              - example.net
            hlws_letsencrypt_staging: true
            pacemaker_password: hunter2
            hlws_virtual_ip: 192.168.0.10
            hlws_ngx_settings:
              http:
                server_names_hash_max_size: 2048


## License

GPLv3+

## Author Information

Copyright 2018, Development Gateway

