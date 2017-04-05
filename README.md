[![Build Status](https://travis-ci.org/kleinstuff/ansible-centos-phpfpm.png)](https://travis-ci.org/kleinstuff/ansible-centos-phpfpm)


kleinstuff.centos-phpfpm
========================

Ansible role to install and configure php-fpm, I have defaults set up to make it work out-of-the-box with nginx

Requirements
------------

I recommend you to run my ansible-centos-base role before anything on CentOS7.

Role Variables
--------------

All variables are setup on defaults/main.yml.

### Not set by default, this can be used to enable selinux-boleans (you need libselinux-python for this):
    ansible_centos_phpfpm__selinux_booleans:
      - httpd_execmem

### User and group creation
    ansible_centos_phpfpm__user: nginx
    ansible_centos_phpfpm__group: nginx
    ansible_centos_phpfpm__group_uid: 991
    ansible_centos_phpfpm__user_uid: 994
    ansible_centos_phpfpm__user_home: /var/cache/nginx
    ansible_centos_phpfpm__user_shell: /sbin/nologin


### Default packages to install
    ansible_centos_phpfpm__packages:
      - php-cli
      - php-fpm

### php.ini template variables
    ansible_centos_phpfpm__short_open_tag: Off
    ansible_centos_phpfpm__disable_functions: show_source, system, shell_exec, backtick operator, passthru, exec, escapeshellarg, escapeshellcmd, proc_close, proc_get_status, proc_nice, proc_open, proc_terminate, popen, disable_functions
    ansible_centos_phpfpm__max_execution_time: 300
    ansible_centos_phpfpm__max_input_time: 300
    ansible_centos_phpfpm__memory_limit: 512M
    ansible_centos_phpfpm__error_reporting: E_ALL
    ansible_centos_phpfpm__display_errors: Off
    ansible_centos_phpfpm__display_startup_errors: Off
    ansible_centos_phpfpm__log_errors: On
    ansible_centos_phpfpm__post_max_size: 10M
    ansible_centos_phpfpm__file_uploads: On
    ansible_centos_phpfpm__upload_max_filesize: 4M
    ansible_centos_phpfpm__max_file_uploads: 10
    ansible_centos_phpfpm__allow_url_fopen: On
    ansible_centos_phpfpm__date_timezone: 'America/Sao_Paulo'

### php.ini extra parameters 
You can set some extra configurations to php.ini, like the ones wich comes on /etc/php.d/\*.ini right here, we have some "defaults" set on defaults/main, like this:<enter>

      - name: Session
        iniparams: |
            [Session]
            session.save_handler = files
            session.save_path = "/tmp"
            session.use_strict_mode = 0
            session.use_cookies = 1
            ;session.cookie_secure =
            session.use_only_cookies = 1
            session.name = PHPSESSID
            session.auto_start = 0
            session.cookie_lifetime = 0
            session.cookie_path = /
            session.cookie_domain =
            session.cookie_httponly =
            session.serialize_handler = php
            session.gc_probability = 1
            session.gc_divisor = 1000
            session.gc_maxlifetime = 1440
            session.referer_check =
            session.cache_limiter = nocache
            session.cache_expire = 180
            session.use_trans_sid = 0
            session.hash_function = 0
            session.hash_bits_per_character = 5
            url_rewriter.tags = "a=href,area=href,frame=src,input=src,form=fakeentry"
      - name: Assertion
        iniparams: |
            [Assertion]
            zend.assertions = -1

### php-fpm.conf template variables
    ansible_centos_phpfpm__process_max: 30
    ansible_centos_phpfpm__listen: "127.0.0.1:9000"
    ansible_centos_phpfpm__pm_max_children: 40
    ansible_centos_phpfpm__pm_start_servers: 10
    ansible_centos_phpfpm__pm_min_spare_servers: 10
    ansible_centos_phpfpm__pm_max_spare_servers: 30
    ansible_centos_phpfpm__pm_process_idle_timeout: 10
    ansible_centos_phpfpm__pm_max_requests: 300

### php-fpm.conf extra parameters (not set by default)
    #ansible_centos_phpfpm__phpfpmconf_params: |
    #   env[FOO] = BAR
    
### If you want to setup a different repo to download packages (like the good remi packages), you can do like this (not set by default):
    ansible_centos_phpfpm__extrarepo:
      - { name: 'REMIPHP71', desc: 'REMI PHP Packages', url: 'https://rpms.remirepo.net/enterprise/7/php71/x86_64/' }
      - { name: 'REMIPHPSafe', desc: 'REMI Safe Packages', url: 'https://rpms.remirepo.net/enterprise/7/safe/x86_64/' }


Dependencies
------------

This role has no deps.

Example Playbook
----------------

     - hosts: servers
       roles:
          - { role: kleinstuff.centos-phpfpm }

License
-------

GPLv3

TODO
-----
I think I should separate the pool configuration from the php-fpm.conf master file, but, for now it works.
