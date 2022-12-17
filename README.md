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
