# Creating a package

## CSV
1. Connect to the remot machine using the command

    `ssh remote_name`

2. Create a mapping file using the Arches UI

    2.1. Go to Arches Designer in the UI

    2.2. Click `Manage...` and select `Create Mapping File` and download the file

    2.3. Unzip the file

    2.3. Once the file is extracted, go into the `file_name.mapping` file, and search and replace all `"export":true` to `true`

3. Create a folder called `mapping_files` on the remote machine for easier navigation using 

    `mkdir mapping_files` 

4. Copy the unzipped mapping files to the remote machine `mapping_files` directory with the following command in a new terminal:

    `scp -r /path/to/mapping remote_user@ip /path/to/mapping_files`

5. Create a new directory called `business_data` on the remote machine using the comman

    `mkdir business_data`

6. Get UUID of the resource model by clicking on the model in Arches Designer and copying it from url after `graph_designer/copy_this_string`

7. Run the following command for each mapping file

    `python manage.py packages -o export_business_data -d './path/to/business_data' -f 'csv' -c 'path/to/mapping_file' -'resource_model_UUID`

8. Once the business data is exported. Create a new dicrectory called `package` using 

    `mkdir packages`

9. Then run the following command to create a package

    `python manage.py packages -o create_package -d '/path/to/packages/package_name'`

10. Once the package is created its time to move the files from the `business_data` folder you've create to the one created in the package :

    10.1. Remove all files in `bussiness_data` containing the word `group` using this command once in the directory

    ```
    find -type f -name '*group*' -delete
    ```

    10.2. Copy  all `model_name.csv` from `business_data` to `packages/business_data` using the command 

    ```
    cp /path/to/business_data/data_name/*.csv /path/to/packages/business_data
    ```

    10.3. Copy all`model_name.relations` from `business_data` to `packages/business_data/relations` using the command

    ```
    cp /path/to/business_data/data_name/*.relations /path/to/packages/business_data/relations
    ```

    10.4. Copy each `mapping_file.mapping` to `packages/business_data` 
    using the command 

    ```
    cp /path/to/mapping_files/model_name/file.mapping /path/to/packages/package_name/business_data
    ```

    10.5 Rename `business_data.csv` file in `packages/business_data` to the same name as your corresponding mapping files `mapping_file.mapping` to end up with `mapping_file_name.csv` using the command

    ```
    mv business_data.csv mapping_file_name.csv 
    ``` 
    
    10.6 Copy all files from `uploadedfiles` directory to `packages/business_data/files` using the command
    
    ```
    cp /path/to/uploadedfiles /path/to/packages/package_name/business_data/files
    ```

### Once business data is moved, it's time to download reference data

11. Go to Reference Data Manager in Arches UI click  `Tools` and export each thesauri one by one

    11.2. Once downloaded move the thesauri to the remote machines `packages/package_name/reference_data/concepts` using the command

    ```
    scp -r /path/to/thesauri remote_user@ip /path/to/packages/package_name/reference_data/concepts
    ```

12. Go to Refernece Data Manger in Arches UI click `Tools` and export all collections

    12.1. Once downloaded move the collections to the remote machines `packages/package_name/reference_data/collections` using the command

    ```
    scp -r /path/to/collections remote_user@ip /path/to/packges/package_name/reference_data/collections
    ```

13. In `packages` create a new folder called `ontlogies` using `mkdir ontolgoies` and within that folder create another directory called `cidoc_crm` and finally move `cidoc` files in there donwloaded from [https://arches.readthedocs.io/en/stable/ontologies-in-arches/?highlight=cidoc#loading-an-ontology](https://arches.readthedocs.io/en/stable/ontologies-in-arches/?highlight=cidoc#loading-an-ontology)
to `ontologies/cidoc_crm/`
