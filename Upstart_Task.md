## Enable Ubuntu Upstart Task (Run NodeJS on reboot)

Create `/etc/init/node-app` file with `775` permissions.

File Contents:
```
description "My NodeJS Server"
author "Matt McDaniel"

# console log

env PORT=3000

start on runlevel [2345]
stop on runlevel [016]
respawn

setuid ubuntu
chdir /var/www/site-io
exec node server.js
```

To start the service, run `sudo service node-app start`.

### Accessing logs

http://upstart.ubuntu.com/cookbook/#console-log

```
tail -f /var/log/upstart/clientreach-app.log 
```

### Configuring log rotation

https://www.digitalocean.com/community/tutorials/how-to-manage-log-files-with-logrotate-on-ubuntu-12-10

`sudo nano /etc/logrotate.d/upstart`
