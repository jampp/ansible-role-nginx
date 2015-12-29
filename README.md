# Ansible role for Nginx

[![Build Status](https://travis-ci.org/torian/ansible-role-nginx.svg)](https://travis-ci.org/torian/ansible-role-nginx)

FIXME 

## Supported Platforms

  * Ubuntu trusty
  * Centos 6
  * Amazon Linux

## Defaults

FIXME

## Usage

You can checkout the plays in the [tests](tests) directory. But here, you 
have a quick pick:

```
---
- hosts: all
  sudo: yes

  vars:
    - nginx_vhosts:
        - server_name: foo.com
          server_aliases:
            - www.foo.bar
            - baz.foo.bar
          root: /var/www/foo.com/html
        
  roles:
    - { role: ansible-role-nginx }

```

Here, you have a more complete example:
```
---
- hosts: all
  sudo: yes

  vars:
    - nginx_no_log: false
    - nginx_vhosts:
        - server_name: foo.com
          logrotate_enabled: true
          server_aliases:
            - www.foo.bar
            - baz.foo.bar
          root: /var/www/foo.com/html
          log_fmt: main
        
        - server_name: sslfoo.com
          server_aliases:
            - www.sslfoo.bar
          root: /var/www/sslfoo.com/html
          ssl_enabled: true
          certs:
            key: "{{var_that_holds_the_key}}"
            crt: "{{var_that_holds_the_cert}}"
            ca:  "{{var_that_holds_the_ca}}"

          http_directives:
            - |
              return 301 https://www.sslfoo.com;
          
          https_directives:
            - |
            location / {
              proxy_cache a_cache;
              add_header X-Proxy-Cache $upstream_cache_status;
              include    proxy_params;
              proxy_pass http://some_upstream;
            }

    - nginx_cache_dirs:
        a_cache:
          prefix: /opt/cache/nginx

    - nginx_config:
        log_format: |
          log_format main '$remote_addr - $remote_user [$time_local] '
            '"$request" $status $body_bytes_sent '
            '"$http_referer" "$http_user_agent" '
            '"$http_x_forwarded_for" $request_time';

        caching: |
          proxy_cache_path
            {{nginx_cache_dirs.a_cache.prefix}} 
            levels=1:2 
            keys_zone=a_cache:100m 
            inactive=60m;
        
        upstreams: |
            upstream some_upstream {
              server 1.2.3.4:9000;
              keepalive 64;
            }


  roles:
    - { role: ansible-role-nginx }

```

## TODO

FIXME

