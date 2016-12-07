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

```
tail -f /var/log/upstart/clientreach-app.log 
```
