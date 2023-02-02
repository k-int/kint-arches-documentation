# Implementing Arches views for CIIM 

## Steps
1. Copy the file "ciimchanges.py" into the Arches project views directory (it is entirely possible there isn't a views folder in the project so in that case simply make one).       
This contains the core code for exporting all ConceptSchemes to XML and displaying resource changes from a given date.

2. Navigate to the project "urls.py" and add the following code, first a new import to the top of the file:
```
from .views.ciimchanges import ChangesView, ConceptsExportView
```
Secondly, add the following to urlpatterns:
```
url(r"^resource/changes", ChangesView.as_view(), name="ChangesView"),
url(r"^concept/export", ConceptsExportView.as_view(), name="ConceptsExportView"),
```
3. 
