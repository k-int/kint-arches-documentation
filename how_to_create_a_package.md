# Creating a package

## CSV
1. Connect to the remot machine using the command

    `ssh remote_name`

2. Create a mapping file using the Arches UI

    2.1. Go to Arches Designer in the UI

    2.2. Click `Manage...` and select `Create Mapping File` and download the file

    2.3. Unzip the file

3. Create a folder called `mapping_files` on the remote machine for easier navigation using 

    `mkdir mapping_files` 

4. Copy the unzipped directory using to the remote machine `mapping_files` directory with the following command in a new terminal:

    `scp -r /path/to/mapping remote_user@ip /path/to/mapping_files`

5. Create a new directory called `business_data` on the remote machine using the comman

    `mkdir business_data`

6. Get UUID of the resource model by clicking on the model in Arches Designer and copying it from url after `graph_designer/copy_this_string`

7. Run the following command

    `python manage.py packages -o export_business_data -d './path/to/business_data' -f 'csv' -c 'path/to/mapping_file' -'resource_model_UUID`

8. Once the business data is exported. Create a new dicrectory called `packages` using 

    `mkdir packages`

9. Then run the following command to create a package

    `python manage.py packages -o create_package -d '/path/to/packages/package_name'`

10. Once the package is created its time to move the files from the `business_data` folder you've create to the one created in the package :

    10.1. Copy `model_name.csv` from `business_data` to `packages/package_name/business_data` using the command 

    `cp /path/to/business_data/data_name/model_name.csv /path/to/packages/package_name/business_data`

    10.2. Move `model_name.relations` from `business_data` to `packages/package_name/business_data/relations` using the comman

    `cp /path/to/business_data/data_name/model_name.relations /path/to/packages/package_name/business_data/relations`
