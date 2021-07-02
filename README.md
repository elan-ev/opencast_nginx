Ansible: Opencast Nginx Role
============================

This Ansible role installs and prepares Nginx as reverse proxy for Opencast.
If no certificate is present, this role deploys a dummy certificate which allows Nginx to start up.

Role Variables
--------------

- `opencast_storage_downloads_path`
    - Path to Opencast's downloads directory (default: `/srv/opencast/downloads/`)
- `opencast_cors_urls`
    - List of URLs to add CORS exceptions for (default: `[]`)


Example Playbook
----------------

Example of how to configure and use the role:

```yaml
- hosts: servers
  become: true
  roles:
    - role: lkiesow.opencast_nginx
```

This will leave you with an invalid dummy certificate.
You will need to replace it with a valid one before booting up Opencast.
The role will _not_ replace an existing certificate so you can safely use a `file` task to deploy it afterwards:

```yaml
- hosts: servers
  become: true
  tasks:
    - include_role:
        name: lkiesow.opencast_nginx

    - name: install tls certificate
      copy:
        src: tls-{{ item }}.pem
        dest: /etc/nginx/ssl/{{ inventory_hostname }}.{{ item }}
        owner: root
        group: root
        mode: '0400'
      notify: reload nginx
      loop:
        - key
        - crt
```
