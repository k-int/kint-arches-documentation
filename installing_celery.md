# Install celery in an Arches project 

## Steps
1. Install a celery broker, in this case we are going to use RabbitMQ.

```
sudo apt-get install rabbitmq-server
```

2. Install Celery as a pip package. Ensure virtual environment is activated using `source ENV_NAME/bin/activate`

```
pip install celery
```

3. Navigate to your project settings.py file at `/my_proj/my_proj/settings.py` and ensure that the following lines are set as such.

```
CELERY_BROKER_URL = 'amqp://guest:guest@localhost'
CELERY_RESULT_BACKEND = 'django-db'  # Use 'django-cache' if you want to use your cache as your backend
```

You will probably want to create a new user and password and replace ‘guest:guest’ with the new users credentials.

4. Start RabbitMQ service

```
sudo service rabbitmq-server start
```

5. For development, celery can be run in a terminal window using

```
python manage.py celery start
```

However, for production, we need to set up celery as a service. We will do this using Supervisord.

## Running Celery as a service
1. Install Supervisor as a pip package.

```
pip install supervisor
```

2. In the core arches repo, in `arches/install/supervisor_celery_setup` there exist example files for supervisor, celeryd, and celerybeat named "my_proj_name-supervisor.conf", "my_proj_name-celeryd.conf" and "my_proj_name-celerybeat.conf", respectively.      
We are going to copy these files to resemble this structure:
![image](https://user-images.githubusercontent.com/105985664/196724278-5df73de9-284a-4ec0-91dc-093d61b9ed2d.png)

3. Enter a separate terminal window and enter `sudo su` then navigate to `/etc`.    
Run `mkdir supervisor` and cd into this new directory.

4. Copy the my_proj_name-supervisord.conf from core arches using

``` 
cp /arches/arches/install/supervisor_celery_setup/my_proj_name-supervisor.conf /etc/supervisor
```

5. Make a new directory within /etc/supervisor

```
mkdir conf.d
```

6. Copy the other two .conf files from core arches into this new directory

```
cp /arches/arches/install/supervisor_celery_setup/my_proj_name-celerybeat.conf /etc/supervisor/conf.d
cp /arches/arches/install/supervisor_celery_setup/my_proj_name-celeryd.conf /etc/supervisor/conf.d
```

7. Rename the three files from my_proj_name to the name of your Arches project.

```
mv my_proj_name-supervisor.conf arches_project-supervisor.conf
```

8. In each of the files make sure that:       
`directory=/absolute/path/to/my_proj` is changed to the path of your Arches project e.g. `/home/ubuntu/arches_project`      
`command=/path/to/virtualenv/` is changed to the path of your virtual environment e.g. `/home/ubuntu/env/` (use `which python3` to find)             
`user=%(ENV_USER)s` is changed to the user going to run the supervisor e.g. `user=ubuntu`       
`[app]` is changed to the name of ELASTICSEARCH_PREFIX in your project’s settings.py       
And in -supervisor.conf, make sure that:       
`files=./conf.d/my_proj_name-celeryd.conf` is changed to the name of your arches project.       

9. Currently the formatting of the -celeryd.conf is incorrect. Ensure these two lines of your file are formatted as such:

```
directory=/home/ubuntu/arches_project
command=/home/ubuntu/env/bin/celery -A arches_project worker --loglevel=INFO
```

10. Running supervisor now will cause errors as it cannot create the files due to current permissions. Before we can proceed, we need to change permissions of files/directories to the user entered to use supervisor.     
As root user, navigate to `/var/log` and `mkdir supervisor`     
Cd to supervisor and create the log file using `touch supervisord.log` and then change permissions to the user selected by `chown USER supervisord.log`

11. Still as root, navigate to `/var/log` and `mkdir celery`      
Once again cd to celery and use `touch worker.log` and `touch beat.log` then `chown USER worker.log` and `chown USER beat.log`

12. Navigate to `/var/run/` and touch `supervisord.pid`      
Then `chown USER supervisord.pid`
 
13. Exit as root user and return to the user you wish to run supervisor as.      

14. Run the following to start the supervisord which will start celery workers for your tasks.

```
supervisord -c /etc/supervisor/ARCHES_PROJECT-supervisord.conf
```

15. Run the following to stop your supervisord process.

```
unlink /tmp/supervisor.sock
```
