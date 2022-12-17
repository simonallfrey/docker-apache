# Example of running docker web app through Apache.

``` sh
sudo docker build -t hello-nginx .
sudo docker run --name hello-nginx-container -d -p 8181:80 hello-nginx
```

`/etc/apache2/sites-available/myserver.com.conf`
``` apacheconf
<VirtualHost *:80>
  ServerName myserver.com

  <Location />
    Order allow,deny
    Allow from all
    Require all granted
  </Location>

  ProxyPass /hello-nginx http://127.0.0.1:8181/
  ProxyPassReverse /hello-nginx http://127.0.0.1:8181/

  # and any other app you like running on e.g. 8081 with entrypoint /app2
  ProxyPass /app2/ http://127.0.0.1:8081/
  ProxyPassReverse /app2/ http://127.0.0.1:8081/
</VirtualHost>
```

``` sh
sudo a2ensite myserver.com
# make sure no https conf is forcing redirect
sudo a2dissite myserver.com-le-ssl
sudo systemctl reload apache2
```

Not sure what /run is. To investigate.
``` sh
$ mount|grep tmpfs
none on /sys/fs/cgroup type tmpfs (ro,nosuid,nodev,noexec)
none on /dev type devtmpfs (rw,nosuid,mode=755)
tmpfs on /dev/shm type tmpfs (rw,nosuid,nodev,noexec)
tmpfs on /run type tmpfs (rw,nosuid,nodev,size=419432k,mode=755)
tmpfs on /run/lock type tmpfs (rw,nosuid,nodev,noexec,relatime,size=5120k)
tmpfs on /run/user/1000 type tmpfs (rw,nosuid,nodev,relatime,size=419428k,mode=700,uid=1000,gid=1000)
```
