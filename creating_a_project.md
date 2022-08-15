# Create a new project 

## Steps
1. Create a new user to install arches under 

```
sudo adduser archesadmin
sudo usermod -aG sudo archesadmin
```

2. Update all packages

```
sudo apt-get update
```

3. Clone the arches repo (as this allows for easier development in the future) and checkout the latest stable version
```
git clone https://github.com/archesproject/arches.git
cd arches 
git checkout 6.1.0
cd
```

4.  Create virtual python environment 
```
sudo apt-get update
sudo apt install python3.8-venv
sudo apt-get install python3-dev
python3 -m venv env
```

5. Activate the virtual enviroment

```
source env/bin/activate
```

6. Install software dependencies 
```
sudo apt-get install gcc
pip install wheel
yes | sudo bash ~/arches/arches/install/ubuntu_setup.sh
yarn install
```

7. Install Arches
```
cd arches
pip install -e .
```

8. Check postgres and es
```
psql -U postgres
\q
curl localhost:9200
```

9. Create a project
```
cd
arches-project create project_name
```
so that `/home/archesadmin/project_name`

## Load package

Arches packages set up a database.
If not loading a package, type the following to setup a database. `python manage.py setup_db`

1. Clone package
```
git clone 'package_name' 
```
HER - Package : https://github.com/archesproject/arches-her

2. Load package
```
python manage.py packages -o load_package -s 'directory/to/package/' -db
```


## Run project

```
cd project_name
python manage.py runserver
```

# Serving the project with Apache

1. install software
```
sudo apt-get install apache2
sudo apt install apache2-dev python3-dev
pip install mod_wsgi
mod_wsgi-express module-config
```
2. Copy `mod_wsgi-express module-config` output

example: 
```
LoadModule wsgi_module "/home/archesadmin/env/lib/python3.8/site-packages/mod_wsgi/server/mod_wsgi-py38.cpython-38-x86_64-linux-gnu.so"
WSGIPythonHome "/home/archesadmin/env"
```

3. Create an apache config file for arches

```
sudo nano /etc/apache2/sites-available/arches-default.conf 
```
Then copy the mod_wsgi-express module-config output and pase it on top o fhte document above `<VirtualHost>`

Populate the document with the following

_Ensure alias is changed to static_

```
# If you have mod_wsgi installed in your python virtual environment, paste the text generated
# by 'mod_wsgi-express module-config' here, *before* the VirtualHost is defined.

<VirtualHost *:80>

    WSGIDaemonProcess arches python-path=/home/archesadmin/project_name
    WSGIScriptAlias / /home/archesadmin/project_name/project_name/wsgi.py process-group=arches

    # Necessary to support Arches Collector
    WSGIPassAuthorization on

    ## Uncomment the ServerName directive and fill it with your domain
    ## or subdomain if/when you have your DNS records configured.
    # ServerName heritage-inventory.org

    <Directory /home/archesadmin/project_name>
        Require all granted
    </Directory>

    # This section tells Apache where to find static files. This example uses
    # STATIC_URL = '/media/' and STATIC_ROOT = os.path.join(APP_ROOT, 'static')
    # NOTE: omit this section if you are using S3 to serve static files.
    Alias /static/ /home/archesadmin/project_name/project_name/static/
    <Directory /home/archesadmin/project_name/project_name/static/>
        Require all granted
    </Directory>

    # This section tells Apache where to find uploaded files. This example uses
    # MEDIA_URL = '/files/' and MEDIA_ROOT = os.path.join(APP_ROOT)
    # NOTE: omit this section if you are using S3 for uploaded media
    Alias /files/uploadedfiles/ /home/archesadmin/project_name/project_name/uploadedfiles/
    <Directory /home/archesadmin/project_name/project_name/uploadedfiles/>
        Require all granted
    </Directory>

    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/html

    # Available loglevels: trace8, ..., trace1, debug, info, notice, warn,
    # error, crit, alert, emerg.
    # It is also possible to configure the loglevel for particular
    # modules, e.g.
    #LogLevel info ssl:warn
    # Recommend changing these file names if you have multiple arches
    # installations on the same server.
    ErrorLog /var/log/apache2/error-arches.log
    CustomLog /var/log/apache2/access-arches.log combined

</VirtualHost>
```

4. Enable the new config created in apache
```
sudo a2dissite 000-default
sudo a2ensite arches-default
sudo service apache2 reload
```

3. Create static files directory 
```
mkdir project_name/project_name/static
```

4. Change `settings.py` to following 
```
STATIC_ROOT = os.path.join(APP_ROOT, 'static')
STATIC_URL = '/static/'
```
and comment out `#STATIC_ROOT = '/var/www/media'`


finally run
```
python manage.py collectstatic
```

4. Grant apache write settings =
```
cd
```
```
sudo chmod 664 /home/ubuntu/Projects/demo_project/demo_project/arches.log
sudo chgrp www-data /home/ubuntu/Projects/demo_project/demo_project/arches.log
```
```
sudo chmod 775 /home/ubuntu/Projects/demo_project/demo_project/uploadedfiles
sudo chgrp www-data /home/ubuntu/Projects/demo_project/demo_project/uploadedfiles
```
```
sudo chmod 775 /home/ubuntu/Projects/demo_project/demo_project
sudo chgrp www-data /home/ubuntu/Projects/demo_project/demo_project
```
```
sudo chmod 775 /home/ubuntu/Projects/demo_project/demo_project/static
sudo chgrp www-data /home/ubuntu/Projects/demo_project/demo_project/static
```
