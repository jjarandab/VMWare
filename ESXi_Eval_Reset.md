# ESXi Eval Reset

1. Create Script named "reset-eval.sh" on main datastore path

```shell
touch reset-eval.sh
vi reset-eval.sh
```

Content:

```
#!/bin/sh
## remove license
echo 'Removing License'
rm -r /etc/vmware/license.cfg
## get a new trial license
echo 'Copying new license'
cp /etc/vmware/.#license.cfg /etc/vmware/license.cfg
## restart services
echo 'Restarting VPXA'
/etc/init.d/vpxa restart
```

2. Grant execution access to script file and do a test run

```
chmod +x reset-eval.sh
./reset-eval.
```

3. Add crontab job (Task Scheduler)

```shell
/bin/echo "0 6 * * 1 /reset-eval.sh" >> /var/spool/cron/crontabs/root kill $(cat /var/run/crond.pid) crond
```

4. Add below code to '/etc/rc.local.d/local.sh'  (Run on Startup)

```shell
vi /etc/rc.local.d/local.sh
```

Content:

```
/bin/kill $(cat /var/run/crond.pid) /bin/echo "0 6 * * 1 /reset-eval.sh" >> /var/spool/cron/crontabs/root crond
```

Reference:

```
https://calvin.me/reset-esxi-evaluation-license
```

