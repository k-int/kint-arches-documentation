# Upgrading an Arches version

## When no changes have been made to core arches files
1. We can simply git checkout to the new version in core arches e.g.
   ```
   git checkout 6.2.2
   ```

2. Then run `pip install -e .` to install all the newest pip dependencies.

3. Run `yarn` or `yarn install`

4. In the project directory change the package.json to point at the new version e.g.
   ```
   {
    "name": "project",
    "dependencies": {
        "arches": "archesproject/arches#stable/6.2.2"
       }
   }
   ```

5. Run `yarn` or `yarn install` in the project at the same level as the package.json.

6. Use the Arches releases directory to find the version (and those inbetween if making a bigger jump) to make additional changes to the settings.py and any other additions.

## When core arches file changes / model changes have been made 
When this is the case we cannot just git checkout because all our changes will be overwritten - in fact most times it will not let you git checkout until changes have been stashed.
In this position we must commit our changes locally and merge.

1. Assuming Arches is installed on a server we must generate a new ssh keypair (check first that one doesnt already exist in .ssh/)
   ```
   ssh-keygen
   ```
   Give the key a password for protecton.
   
2. Add the key to github

3. Create a repo in the kint organisation called arches_customer

4. Add the repo as a remote on the server by running `git remote add kint git@github.com:k-int/arches-CUSTOMER.git`

5. Optional: Can rename the old core Arches repo by `git remote rename origin coreArches`

6. Add and commit changes using `git add .` and `git commit -m "message"` 

7. Checkout to a new branch as a copy of the current but renamed
   ```
   git checkout -b arches_CUSTOMER_CURRENTVERSION
   ```
   
8. Push this branch to our new repo 
   ```
   git push kint arches_CUSTOMER_CURRENTVERSION
   ```
   
9. Locally clone this repo we have just made with its one branch of the current Arches version
   ```
   git clone URL/SSH URL
   ```

10. Checkout to a new branch again this time calling it the new version we want to upgrade to
    ```
    git checkout -b arches_CUSTOMER_NEWVERSION
    ```
    
11. Add coreArche as a remote to our local copy of the repo 
    ```
    git remote add coreArches https://github.com/archesproject/arches.git
    ```
    We can check our remotes using `git remote -vv`
    
12. Fetch the version you would like by running
    ```
    git fetch coreArches stable/version
    ```
   
13. Open VSCode in the cloned repo. Then, ensuring we are on the new version branch, run git merge
    ```
    git merge stable/version
    ```
    NOTE : sometimes a message displaying `merge: stable/6.2.2 - not something we can merge` will arise. Checkout to the fetched version and then checkout back to the branch we want to be on and then try again.

14. There should be no conflicts if the git histories are intact, however, if there are conflicts use the VSCode Source Control tab to see all conflicts and either accept incoming changes or keep current, or both.

