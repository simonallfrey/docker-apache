# Example of running docker web app through Apache.

As our example web app we use nginx (doen't need a database like phoenix or wordpress)

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
credit to https://stackoverflow.com/questions/46099348/how-to-use-apache-to-redirect-to-docker-container

``` sh
sudo a2ensite myserver.com
# make sure no https conf is forcing redirect
sudo a2dissite myserver.com-le-ssl
sudo systemctl reload apache2
```
Now head over to http://myserver.com/hello-nginx


``` sh
sudo docker ps
sudo docker stop hello-nginx-container
sudo docker info
sudo docker images
```
If this last gives you some `REPOSITORY:TAG <none>:<none>` follow this advice:

``` sh
sudo docker rmi $(sudo docker images -f "dangling=true" -q)
```
or maybe be more insistent if claims are made of usage by non existent container.

``` sh
sudo docker rmi -f $(sudo docker images -f "dangling=true" -q)
```

credit: https://projectatomic.io/blog/2015/07/what-are-docker-none-none-images/
