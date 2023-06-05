Automysqlbackup
=========

Role to install the up-to-date version of [automysqlbackup fork](https://github.com/belgotux/automysqlbackup) of the [not maintained wipe_out's project on sourceforge](https://sourceforge.net/projects/automysqlbackup/).

Features
---------

- export databases in whitelist/blacklist format
- you can whitelist/backlist some tables
- A great retention management to keep x weekly, y daily and z monthly
- Adding feature to dump MySQL users as GRANT commands with `pt-show-grants`. The credentials file is different than the `.my.cnf` (this last doesn't work with `--defauls-file` but with `--config`)

Requirements
------------

Install the minimal rights on the target servers for automysqlbackup. Here an example to backup all the current and futur databases on the SAME server: 
```sql
CREATE USER 'automysqlbackup'@'localhost' IDENTIFIED BY  '<your_password_secret>';
GRANT SELECT, LOCK TABLES, SHOW VIEW, EVENT, TRIGGER,USAGE ON *.* TO 'automysqlbackup'@'localhost' ;
```

Install the minimal rights on target servers B,C,D,etc to have backup done on server A.
```sql
CREATE USER 'automysqlbackup'@'serverA' IDENTIFIED BY  '<your_password_secret>';
GRANT SELECT, LOCK TABLES, SHOW VIEW, EVENT, TRIGGER,USAGE ON *.* TO 'automysqlbackup'@'serverA' ;
```

Having mariadb or mysql client binary installed. By default mariadb is installed.

Role Variables
--------------

## Needed
### server configuration
- `automysqlbackup_repo` directory to put the git repository (default `/opt`)
- `automysqlbackup_client` sql client binary : can be mariadb-client or mysql-client (default `mariadb-client`)
- `automysqlbackup_bin` script directory (default `/usr/local/bin`)
- `automysqlbackup_conf` configurations directory (default `/etc/automysqlbackup`)
- `automysqlbackup_backupdir` backup root directory
### SQL client configuration
- `automysqlbackup_default_user` MySQL username to use for all servers by default (default `automysqlbackup`)
- `automysqlbackup_default_passwd` MySQL password to use for all servers by default
- `automysqlbackup_configurations` list of servers to backup. Take the value of `automysqlbackup_default_user`, `automysqlbackup_default_passwd`, etc if `user`, `passwd`, etc is not defined
```
automysqlbackup_configurations:
- name: server1
  server: xxx.yourdomain.tld
  port: 3306            # default use 3306
  user: test            # default use `automysqlbackup_default_user`
  passwd: testpwd       # default use `automysqlbackup_default_passwd`
  cron: "0 23 * * *"    # default use `automysqlbackup_default_cron`
  montly: 01            # default use `automysqlbackup_monthly`
  weekly: 5             # default use `automysqlbackup_weekly`
  daily_to_keep: 5      # default use `automysqlbackup_daily_to_keep`
  weekly_to_keep: 4     # default use `automysqlbackup_weekly_to_keep`
  monthly_to_keep: 3    # default use `automysqlbackup_monthly_to_keep`
- name: server2
  server: 192.168.x.x
```
- `automysqlbackup_default_db_exclude` list of database excluded if no db_names is defined on a server (dafault `('information_schema' 'performance_schema' 'sys' )`)
- `automysqlbackup_default_table_exclude` list of tables excluded in the format `('db_name.table' 'db_name.table2)'` (default `()`)
- `automysqlbackup_default_cron` set the default cron time, example `"0 23 * * *"`
- `automysqlbackup_create_user` create the `automysqlbackup_default_cron_user` system user. Can be `true` or `false` (default `true`)

## optionnal
### server configuration
- `automysqlbackup_multicore` using pigz for gzip or pbzip2 for bzip2 multitreading compression. Can be `'yes'` or `'no'` (default `'no'`)
- `automysqlbackup_multicore_threads` how many treads to use. Can be a number or `auto` to let pigz or pbzip2 autodetect (default `auto`)
- `automysqlbackup_default_cron_user` user to use to execute the cron to do the backup (default `automysqlbackup`)
- `automysqlbackup_default_cron_group` group to use for folder rights (default `users`)
- `automysqlbackup_default_cron_path` cron directory (default `/etc/cron.d`)

### SQL client configuration
- `automysqlbackup_monthly` Which day do you want monthly backups? (01 to 31) and 0 to disable monthly (default `01`)
- `automysqlbackup_weekly` Which day do you want weekly backups? (1 to 7 where 1 is Monday) and 0 to disable weekly (default `5`)
- `automysqlbackup_daily_to_keep` how many days daily backup to keep (default `6`)
- `automysqlbackup_weekly_to_keep` how many days weekly backup to keep (default `28`)
- `automysqlbackup_monthly_to_keep` how many days monthly backup to keep (default `180`)
- `automysqlbackup_no_tablespaces` You need PROCESS right to dump from MySQL 5.7.31 and 8.0.21, to avoid error this you can add --no-tablespaces. Can be `'yes'` or `'no'` (default `'yes'`)
- `automysqlbackup_dump_usessl` Use ssl encryption with mysqldump. Can be `'yes'` or `'no'` (default `'no'`)
- `automysqlbackup_dump_single_transaction` When using this option, you should keep in mind that only InnoDB tables are dumped in a consistent state. For example, any MyISAM or MEMORY tables dumped while using this option may still change state. Can be `'yes'` or `'no'` (default `'no'`)
- `automysqlbackup_dump_dbstatus` Backup status of table(s) in textfile. This is very helpful when restoring backups, since it gives an idea, what changed in the meantime. Can be `'yes'` or `'no'` (default `'yes'`)
- `automysqlbackup_dump_create_database` Include CREATE DATABASE in backup. Can be `'yes'` or `'no'` (default `'no'`)
- `automysqlbackup_dump_use_separate_dirs` Separate backup directory and file for each DB. Can be `'yes'` or `'no'` (default `'yes'`)
- `automysqlbackup_dump_compression` Choose Compression type `gzip` or `bzip2` (default `gzip`)
- `automysqlbackup_dump_latest` Store an additional copy (hardlink) of the latest backup to a standard location so it can be downloaded by third party scripts. Can be `'yes'` or `'no'` (default `'yes'`)
- `automysqlbackup_dump_latest_clean_filenames` Remove all date and time information from the filenames in the latest folder (default `no`)
- `automysqlbackup_mailcontent` What would you like to be mailed to you : `log` (send only log file), `files` (send log file and sql files as attachments), `stdout` (will simply output the log to the screen if run manually), `quiet` (Only send logs if an error occurs to the MAILADDR) (default `log`)
- `automysqlbackup_mail_address` Email Address to send mail to
- `automysqlbackup_dryrun` show what you are gonna do without actually doing it. `0` inactive, `1` active dry-run (default `0`)

### export GRANT users (for Debian only)
- `automysqlbackup_percona_toolkit_install` Install the `percona-toolkit` package (default `false`)
- `automysqlbackup_default_export_grant_users` : Export the MySQL users (default `false`)

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

    - hosts: servers
      roles:
         - role: belgotux.automysqlbackup
           vars:
              automysqlbackup_configurations:
              - name: server1
                user: test
                passwd: testpwd
                server: xxxx
                db_names: mysql
                export_grant_users: true
              - name: server2
                server: 192.168.x.x
              - name: server3
                server: 192.168.y.y
                db_exclude: "( 'test' )"
                table_exclude: "('db_name1.tablex' 'db_name2.tabley' 'db_name2.log')"

              automysqlbackup_backupdir: /tmp/backup
              automysqlbackup_mailcontent: quiet
              automysqlbackup_mail_address: root
              automysqlbackup_default_cron: 0 1 * * *

Troubleshooting
---------------
## Error with the system user
If you execute the script with another user than the `automysqlbackup_default_cron_user`, automysqlbackup can't read the cnf file with the mysql credentials, and you have this error :
```
# Checking for permissions to write to folders:
base folder /tmp ... exists ... ok.
backup folder /tmp/backup ... exists ... writable? mktemp: failed to create file via template '/tmp/backup/tmp.XXXXXX': Permission denied
no. Exiting.
Note: Parsed config file /etc/automysqlbackup/pc.conf.
Note: /etc/automysqlbackup/automysqlbackup.conf was not found - no global config file.
Error: /tmp/backup is not writable. Exiting.
/usr/local/bin/automysqlbackup: line 875: 6: Bad file descriptor
/usr/local/bin/automysqlbackup: line 876: 7: Bad file descriptor
Skipping normal output methods, since the program exited before any log files could be created.
```  

License
-------

[GPL v3](https://www.gnu.org/licenses/gpl-3.0.en.html)

Author Information
------------------

Belgotux
[MonLinux](https://www.monlinux.net)

