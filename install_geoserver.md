# Deploying Geoserver with Apache Tomcat

Geoserver is required for Arches 5+ as the standard map tileserver was removed.

# Tomcat

## 1. Install tomcat
`sudo apt update`
`sudo apt install tomcat9 tomcat9-admin`

## 2. Enable service and allow traffic
`sudo systemctl enable tomcat9`
`sudo ufw allow from any to any port 8080 proto tcp`

## 3. Test by visiting port 8080
http://localhost:8080

# Geoserver 

## 1. Download the latest WAR file and move it onto the server
http://geoserver.org/release/stable/
WAR WeWeb Archive (war) for servlet containers.

## 2. Place the WAR file in /webapps directory in the tomcat service
`/var/lib/tomcat9/webapps`

## 3. Restart tomcat
`sudo service tomcat9 rsetart`

# Proxy with apache2 

## 1. Add to apache2 conf
```    
	ProxyRequests Off
    ProxyPreserveHost On

    ProxyPass /geoserver "http://localhost:8080/geoserver"
    ProxyPassReverse /geoserver "http://localhost:8080/geoserver"
```

## 2. enable rewrite
`sudo a2enmod rewrite`

## 3. restart
`sudo service apache2 restart`
