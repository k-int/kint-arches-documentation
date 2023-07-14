# Installing Arches with the HER Package

## Installing the package
1. Assume the previous arches documentation has been followed up to step 9 where a project has been created.

2. Clone the arches-HER project 
```
git clone https://github.com/archesproject/arches-her.git
```

3. Copy the package to the same directory
```
cp -r arches-her/arches_her/pkg/ .
```

4. Setup the database for the project by navigating to the project root directory and running
```
python manage.py setup_db
```

5. Load the package
```
python manage.py packages -o load_package -s '../pkg/' -db
```

6. This package loads the ontologies, graphs, concepts etc but the plugins need to be copied as well as some JS/html components for the workflows.

## Copying required files
1. The report templates from the arches-her project are needed in our project. Copy the reports directories found in the following locations
   ```
   arches-her/arches_her/templates/views/components/reports
   arches-her/arches_her/media/js/views/components/reports
   ```
and place them in our project's /components/ dir.

2. Copy the following dir `arches-her/arches_her/media/js/utils` into our project's js/utils

3. The custom HER reports require their own CSS which also needs copying. These files are found in the directory `arches-her/arches_her/media/css` which can be copied into project/media

4. The consultation plugins are not included either which will need to be copied.      
   These consist of multiple components, the first is found at: 
   ```
   arches-her/arches_her/plugins
   ```
   This entire directory should be copied into the `project/project` location.
   Next, copy the JS and HTML components of the plugins found at locations:
   ```
   arches-her/arches_her/media/js/views/components/plugins
   arches-her/arches_her/templates/views/components/plugins
   ```
   
5. The workflows also need copying over to allow the plugins to function correctly. These consist of JS and HTML components which are found at the locations.
   ```
   arches-her/arches_her/media/js/views/components/workflows
   arches-her/arches_her/templates/views/components/workflows
   ```
   
6. Copy consultations-help from `media/js/views/consultations-help.js`

7. All plugins now need registering via the command line:
   ```
   python manage.py plugin register --source 'project/plugins/active-consultations.json'
   python manage.py plugin register --source 'project/plugins/application-area.json'
   python manage.py plugin register --source 'project/plugins/communcation-workflow.json'
   python manage.py plugin register --source 'project/plugins/consultation-workflow.json'
   python manage.py plugin register --source 'project/plugins/correspondence-workflow.json'
   python manage.py plugin register --source 'project/plugins/init-workflow.json'
   python manage.py plugin register --source 'project/plugins/site-visit.json'
   ```
   These can be confirmed by running `python manage.py plugin list`
   
8. If the correct version is used, all htm files should have `{% load static %}` at the top rather than `{% load staticfiles %}` - if not change to static.

9. Copy all python views from arches-HER from `arches-her/arches_her/views` into the project.

10. In the project urls.py found at `project/project/urls.py` copy the following imports to the top of the file:
    ```
    #Arches default views
    from arches.app.views.user import UserManagerView
    from arches.app.views.plugin import PluginView
    from django.views.generic import RedirectView
    
    #Arches HER views
    from .views.index import IndexView
    from .views.resource import ResourceDescriptors
    from .views.file_template import FileTemplateView
    from .views.active_consultations import ActiveConsultationsView
    
    uuid_regex = settings.UUID_REGEX
    ```
    Then add the following urls underneath `url(r'^', include('arches.urls')),` :
    ```
    url(r'^$', IndexView.as_view(), name='root'),
    url(r'^'+settings.APP_PATHNAME+'/$', IndexView.as_view(), name='consultations-root'),
    url(r'^index.htm', IndexView.as_view(), name='home'),
    url(r'^'+settings.APP_PATHNAME+'/index.htm', IndexView.as_view(), name='consultations-home'),
    url(r'^'+settings.APP_PATHNAME+'/', include('arches.urls')),
    url(r'^plugins/active-consultations$', RedirectView.as_view(url='/'+settings.APP_PATHNAME+'/plugins/active-consultations')),
    url(r'^resource/descriptors/(?P<resourceid>%s|())$' % uuid_regex, ResourceDescriptors.as_view(), name="resource_descriptors"),
    url(r'^'+settings.APP_PATHNAME+'/index.htm', IndexView.as_view(), name='home'),
    url(r'^', include('arches.urls')),
    url(r'^'+settings.APP_PATHNAME+'/user$', UserManagerView.as_view(), name="user_profile_manager"),
    url(r'^filetemplate', FileTemplateView.as_view(), name='filetemplate'),
    url(r'^'+settings.APP_PATHNAME+'/plugins/active-consultations', PluginView.as_view(), name='active-consultations'),
    url(r'^activeconsultations', ActiveConsultationsView.as_view(),
        name='activeconsultations'),
    url(r'^'+settings.APP_PATHNAME+'/plugins/consultation-workflow', PluginView.as_view(), name='consultation-workflow'),
    url(r'^'+settings.APP_PATHNAME+'/plugins/application-area', PluginView.as_view(), name='application-area'),
    url(r'^'+settings.APP_PATHNAME+'/plugins/site-visit', PluginView.as_view(), name='site-visit'),
    url(r'^'+settings.APP_PATHNAME+'/plugins/correspondence-workflow', PluginView.as_view(), name='correspondence-workflow'),
    url(r'^'+settings.APP_PATHNAME+'/plugins/communication-workflow', PluginView.as_view(), name='communication-workflow'),
    url(r'^'+settings.APP_PATHNAME+'/plugins/init-workflow', PluginView.as_view(), name='init-workflow'),
    ```
    
11. In settings.py of the project, add the following line to set the APP_PATHNAME for the urls involved in consultation and workflows:
    ```
    APP_PATHNAME = "PROJECT_HER"
    ```
    
12. In `project/media/js/views/components/plugins/init-workflow.js` change the following line:
    ```
    wf.url = "/arches-her" + arches.urls.plugin(wf.slug);
    ```
    From arches-her to your APP_PATHNAME.
    
13. In `views/index.py` change the import `from arches_her.settings import APP_TITLE` to `from project.settings import APP_TITLE`.
14. In `views/resource.py` change the import `from arches_her.views.active_consultations import build_resource_dict` to `from .active_consultations import build_resource_dict
`.
