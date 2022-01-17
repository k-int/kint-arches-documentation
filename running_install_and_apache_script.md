# How to use `install_and_apache.sh` script to install Arches.

 1. Create a new user called `archesadmin` using the following command:
  ```
  sudo adduser archesadmin
  ```
  
  2. Sudo the `archesadmin` user using the following command:
  ```
  sudo usermod -aG sudo archesadmin
  ```
  
  3. Switch to the `archesadmin` user using the following command:
  ```
  su - archesadmin
  ```
  enter `archesadmin` password
  
  4.  Switch to `root` using the fowllowing command:
  ```
  sudo su
  ```
  enter `archesadmin` password
  
  5. Clone `install_and_apache.sh` repo using the following:
  ```
  git clone https://github.com/reubenosborne1/arches-scripts.git
  ```
  
  6.  Step into `archesadmin` and configure install script varbiles  
   
   6.1 Change `project_name` at the top of the `install_and_apache.sh` file to `projectName_project` example by using `nano`:
   ```
   nano arches-scrips/install_and_apache.sh
   project_name="projectName_project"
   ```
    
   6.2 Change my host to broadcast IP of the server to match ssh IP using while in `nano`:
   ```
   my_host="ssh.ip.add.ress"
   ```
    
   6.3 Else install `net-tools` by running:
   ```
   sudo apt install net-tools
   ```
    
   6.4 Run the following command:
   ```
   ifconfig 
   ```
      
   6.5 Copy the `inet` ip then `nano` into `install_and_apache.sh` and finally replace
   ```
   my_host="inet.add.ress"
   ```
      
   6.6 Change `arches_version` to desired branch version e.g.: `origin/stable/5.2.x` in `nano`:
   ```
   arches_version="origin/stable/5.2.x"
   ```
     
7. Run the script using the following:
  ```
  bash arches-scripts/install_and_apache.sh
  ```
    
