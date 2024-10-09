# Installing Arches and creating a new project 
This documentation is specifically for Arches version 7+.
Between 6 and 7, lots of features have been removed or changed such as:
- Elasticsearch upgraded from 7 to 8
- The introduction of webpack to manage front end bundles
- collectstatic command has been deprecated for build_production


## Steps
### 1. Create a new user to install arches under (if applicable)

   If installing arches locally, skip this step. 

   ```
   sudo adduser archesadmin
   sudo usermod -aG sudo archesadmin
   ```

### 2. Update all packages

   ```
   sudo apt-get update
   ```

### 3. Create a master directory to house everything. Within it, clone the arches repo (as this allows for easier development in the future).
   ```
   git clone https://github.com/archesproject/arches.git 
   ```

### 4. Checkout to the latest stable version
   ```
   cd arches 
   git checkout {VERSION}
   ```

### 5.  Create virtual python environment 

   Note that arches 7.0.0 uses python 3.10, but later versions, such as 7.5 and onwards, are compatible with 3.11.

   If you haven't previously installed the relevant venv for the version of python needed, run the following, replacing 3.x with the correct python version:

   ```
   sudo apt-get update
   sudo apt install python3.x-venv
   sudo apt-get install python3.x-dev
   ```
   To create the virtual environment for your project, within the master directory, run:

   ```
   python3.x -m venv env3.x
   ```

### 6. Activate the virtual enviroment

   ```
   source env3.x/bin/activate
   ```

### 7. Install software dependencies 
   ```
   sudo apt-get install gcc
   pip install wheel
   ```
   In core Arches, on the level of package.json run `yarn install`

   Dependencies such as postgres, elasticsearch, yarn, node etc can be installed using the following script, responding yes when prompted (except for Elastic Search):
   ```
   sudo bash ~/arches/arches/install/ubuntu_setup.sh
   ```
   It is typically bad practice to install ES via apt-get.  Instead use the following to download the relevant installation file:
   ```
   wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-8.4.3-linux-x86_64.tar.gz
   ```
   Unzip the tar file using `tar -xvf <the .tar.gz file>`

   In the unzipped ES directory, in config/elasticsearch.yml ensure that `http.port: 9200` is uncommented/added.
   
   If not already the case, set `xpack.security.enabled` to `true`

   Reset the password for the default "elastic" user:
   ```
   bin/elasticsearch-reset-password -u elastic
   ```

   Inside the unzipped ES directory, run ES in daemon mode using `bin/elasticsearch -d`
   See ES running by `ps aux | grep elastic` and kill using the pid and `kill -9 pid`

### 8. Install Arches

   Whilst connected to the python virtual environment, run:
   ```
   pip install -e .
   ```

### 9. Check postgres and es
   ```
   psql -U postgres
   \q 
   curl localhost:9200
   ```

### 10. Create a project

   Within the master directory run (replacing {project_name with the new project's desired name}):
   ```
   arches-project create {project_name}
   ```

### 11. Elasticsearch settings

   If the arches instance is http, then copy the following core arches line into the project settings.py and change to include http not https:
   ```
   ELASTICSEARCH_HOSTS = [{"scheme": "http", "host": "localhost", "port": ELASTICSEARCH_HTTP_PORT}]
   ```

   If it's not already defined, you may also need to add the following line: 

   ```
   ELASTICSEARCH_HTTP_PORT = 9200  # this should be in increments of 200, eg: 9400, 9600, 9800
   ```

   Add the ES password, set earlier, to the following line in settings.py, replacing {PASSWORD}
   ```
   ELASTICSEARCH_CONNECTION_OPTIONS = {"timeout": 30, "verify_certs": False, "basic_auth": ("elastic", "{PASSWORD}")}
   ```

### 12. Load package or set up database

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


### 13. Run project

   ```
   cd project_name
   python manage.py runserver
   ```

   Note that the software will not work without errors until webpack has been set up, as per the next step.

### 14. Fixing missing packages

Some packages may need to be added to the package.json manually. Examples include: 

   ```
   "datatables.net": "1.12.1",
   "jquery-validation": "1.20.0"
   ```

### 15. Setting up webpack
   If an error like the following occurs:
   ```
   Error reading /mnt/c/testing/v7arches/v7arches/v7arches/webpack/webpack-stats.json. Are you sure webpack has generated the file and the path is correct?
   ```
   This is because we need to correct the webpack settings to point at the correct place.

   At the same time as `python manage.py runserver` is running, run `yarn build development` or `yarn start` in a separate terminal, in the location of the project's package.json file.


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
   Then copy the mod_wsgi-express module-config output and paste it on top of the document above `<VirtualHost>`

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

5. Grant apache write settings =
```
cd
```
```
sudo chmod -R 664 /home/ubuntu/Projects/demo_project/demo_project/arches.log
sudo chgrp -R www-data /home/ubuntu/Projects/demo_project/demo_project/arches.log
```
```
sudo chmod -R 775 /home/ubuntu/Projects/demo_project/demo_project/uploadedfiles
sudo chgrp -R www-data /home/ubuntu/Projects/demo_project/demo_project/uploadedfiles
```
```
sudo chmod -R 775 /home/ubuntu/Projects/demo_project/demo_project
sudo chgrp -R www-data /home/ubuntu/Projects/demo_project/demo_project
```
```
sudo chmod -R 775 /home/ubuntu/Projects/demo_project/demo_project/static
sudo chgrp -R www-data /home/ubuntu/Projects/demo_project/demo_project/static
