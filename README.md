Automysqlbackup
=========

Role to install the up-to-date version of [automysqlbackup fork](https://github.com/belgotux/automysqlbackup) of the [not maintained wipe_out's project on sourceforge](https://sourceforge.net/projects/automysqlbackup/)

Requirements
------------

Having mariadb or mysql client binary installed. By default mariadb is installed

Role Variables
--------------

## Needed
### server configuration
- `automysqlbackup_repo` direcoty to put the git repository (default `/opt`)
- `automysqlbackup_client` client binary : can be mariadb-client or mysql-client (default `mariadb-client`)
- `automysqlbackup_bin` client directory (default `/usr/local/bin`)
- `automysqlbackup_conf` configuration directory (default `/etc/automysqlbackup`)
- `automysqlbackup_backupdir` backup root directory
### client configuration
- `automysqlbackup_default_user` username to use for all servers by default (default `automysqlbackup`)
- `automysqlbackup_default_passwd` password to use for all servers by default
- `automysqlbackup_configurations` list of servers to backup. Take the value of `automysqlbackup_default_user` and `automysqlbackup_default_passwd` if `user` and `passwd` is not defined
```
automysqlbackup_configurations:
- name: server1
  server: xxx.yourdomain.tld
  port: 3306
  user: test
  passwd: testpwd
  cron: "0 23 * * *"
- name: server2
  server: 192.168.x.x
```
- `automysqlbackup_default_db_exclude` list of database excluded if no db_names is defined on a server (dafault "`('information_schema' 'performance_schema' 'sys' )`")
- `automysqlbackup_default_table_exclude` list of tables excluded in the format db_name.table_name (default "`('mysql.event')`")
- `automysqlbackup_default_cron` set the default cron time, example `"0 23 * * *"`
- `automysqlbackup_create_user` create the `automysqlbackup_default_cron_user` system user. Can be `true` or `false` (default `true`)

## optionnal
### server configuration
- `automysqlbackup_multicore` using pigz for gzip or pbzip2 for bzip2 multitreading compression. Can be `'yes'` or `'no'` (default `'no'`)
- `automysqlbackup_multicore_threads` how many treads to use. Can be a number or `auto` to let pigz or pbzip2 autodetect (default `auto`)
- `automysqlbackup_monthly` Which day do you want monthly backups? (01 to 31) and 0 to disable monthly (default `01`)
- `automysqlbackup_weekly` Which day do you want weekly backups? (1 to 7 where 1 is Monday) and 0 to disable weekly (default `5`)
- `automysqlbackup_daily_to_keep` how many daily backup to keep (default `6`)
- `automysqlbackup_weekly_to_keep` how many weekly backup to keep (default `28`)
- `automysqlbackup_monthly_to_keep` how many monthly backup to keep (default `180`)
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
- `automysqlbackup_default_cron_user` user to use to execute the cron to do the backup (default `automysqlbackup`)
- `automysqlbackup_default_cron_group` group to use for folder rights (default `users`)
- `automysqlbackup_default_cron_path` cron directory (default `/etc/cron.d`)

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
              - name: server2
                server: 192.168.x.x
              - name: server3
                server: 192.168.y.y
                db_exclude: "( 'test' )"

              automysqlbackup_backupdir: /tmp/backup
              automysqlbackup_mailcontent: quiet
              automysqlbackup_mail_address: root

License
-------

[GPL v3](https://www.gnu.org/licenses/gpl-3.0.en.html)

Author Information
------------------

Belgotux
[MonLinux](https://www.monlinux.net)

