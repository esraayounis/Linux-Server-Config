# Udacity - Linux Server Configuration Project

> A baseline installation of a Linux server and prepare it to host your web applications.
secure the server from a number of attack vectors,
install and configure a database server, and deploy [Catalog](https://github.com/esraayounis/CatalogUdacityProject) web applications onto it.


### Technical Information About the Project

- **Server IP Address:** 157.230.128.59
- **SSH server access port:** 2200
- **SSH password of grader username:** grader
- **Application URL:** http://157.230.128.59.xip.io
- **Login as grader:** grader@157.230.128.59 -p 2200 -i ~/.ssh/item
- **passphrase for key '/c/Users/Esra/.ssh/item':** hello
- **SSH Key Content:**
```
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDwMiWDHN/7gLC+rguzA9lMjcK+XpdHNhxMgpR88pym91weKlHu1QS/u4Azrz9NN4Gh4/O5TA/+T0qAoPG/Tc41Vqx2Q392mFKYBv0PdT9/InOxzO8l1J7rDfQEOl+Yu1UjOJXR7MKz3qSuQwot9ev9LdIhUS9BKA8A4RVmfa+Et3u4yhdYk8zHkpa/nuaj8wuuHF8v/5IXENY1rl7uJ2n0D6Yq9M3SXF/TANANT6Vmqo7LfvulKCVm1FqKqae8O3G2VAnx4fA+002chhY3aWHP3md6NhrJUhHlbZD2G/2cd/3o9G03rN/ghDQ2bf3w43dF8un80S/Vxzcu6DYhqcFH Esra@EsOo
```



## Steps to Set up the Server

### 1. Creating the RSA Key Pair

On your local machine, you will first have to set up the public and private key pair. This key pair will be used to authenticate yourself while securely logging in to the server via SSH. The private key will be kept with you in your local machine, and the public key will be stored in the server.

To generate a key pair, run the following command:

   ```console
   $ ssh-keygen
   ```

When it asks to enter a passphrase, you may either leave it empty or enter some passphrase. A passphrase adds an additional layer of security to prevent unauthorized users from logging in.

The whole process would look like this:

```
Generating public/private rsa key pair.
Enter file in which to save the key (/c/Users/Esra/.ssh/id_rsa): /c/Users/Esra/.ssh/item
Created directory '/c/Users/Esra/.ssh'.
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /c/Users/Esra/.ssh/item.
Your public key has been saved in /c/Users/Esra/.ssh/item.pub.
The key fingerprint is:
SHA256:JnpV8vpIZqstoTm8aaDAqVmGSF/tGhtDfXAfL2fs/U8 subhadeep@subhadeep-VirtualBox
The key's randomart image is:
+---[RSA 2048]----+
|                 |
|       . . .     |
|      o + o +    |
| .   o o = o =   |
|+.o o o S . = .  |
|+o+. =.= .   . . |
|o= o.oB.=       E|
|+   *=.= +     ..|
|   .oo.o+ .     o|
+----[SHA256]-----+
```

You now have a public and private key that you can use to authenticate. The public key is called `item.pub` and the corresponding private key is called `item`. The key pair is stored inside the `~/.ssh/` directory.

### 2. Setting Up a DigitalOcean Droplet

1. Log in or create an account on [DigtalOcean](https://cloud.digitalocean.com/login).

2. Go to the Dashboard, and click **Create Droplet**.

3. Choose **Ubuntu 18.04 x64** image from the list of given images.

4. Choose a preferred size. In this project, I have chosen the **1GB/1 vCPU/25GB** configuration.

5. In the section **Add Your SSH Keys**, paste the content of your public key, `item.pub`:
```
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDwMiWDHN/7gLC+rguzA9lMjcK+XpdHNhxMgpR88pym91weKlHu1QS/u4Azrz9NN4Gh4/O5TA/+T0qAoPG/Tc41Vqx2Q392mFKYBv0PdT9/InOxzO8l1J7rDfQEOl+Yu1UjOJXR7MKz3qSuQwot9ev9LdIhUS9BKA8A4RVmfa+Et3u4yhdYk8zHkpa/nuaj8wuuHF8v/5IXENY1rl7uJ2n0D6Yq9M3SXF/TANANT6Vmqo7LfvulKCVm1FqKqae8O3G2VAnx4fA+002chhY3aWHP3md6NhrJUhHlbZD2G/2cd/3o9G03rN/ghDQ2bf3w43dF8un80S/Vxzcu6DYhqcFH Esra@EsOo
```

   This step will automatically create the file `~/.ssh/authorized_keys` with appropriate permissions and add your public key to it. It would also add the following rule in the `/etc/ssh/sshd_config` file automatically:

   ```
   PasswordAuthentication no
   ```

   This rule essentially disables password authentication on the `root` user, and rather enforces SSH logins only.

 6. Click **Create** to create the droplet. This will take some time to complete. After the droplet has been created successfully, a public IP address will be assigned. In this project, the public IPv4 address that I have been assigned is `157.230.128.59`.

### 3. Logging In as `root` via SSH and Updating the System

#### 3.1. Logging in as `root` via SSH

As the droplet has been successfully created, you can now log into the server as `root` user by running the following command in your host machine:

```
  $ ssh root@157.230.128.59
```


#### 3.2. Updating the System

Run the following command to update the virtual server:

```
 # sudo apt-get update  
 # sudo apt-get upgrade
```

This will update all the packages. If the available update is a kernel update, you might need to reboot the server by running the following command:

```
# reboot
```

### 4. Changing the SSH Port from 22 to 2200

1. Open the `/etc/ssh/sshd_config` file with `nano` or any other text editor of your choice:

   ```
   # nano /etc/ssh/sshd_config
   ```

2. Find the line `#Port 22` (would be located around line 13) and change it to `Port 2200`, and save the file.

3. Restart the SSH server to reflect those changes:
   ```
   # service ssh restart
   ```

4. To confirm whether the changes have come into effect or not, run:
   ```
   # exit
   ```

   This will take you back to your host machine. After you are back to your local machine, run:

   ```
   $ ssh root@157.230.128.59 -p 2200  -i ~/.ssh/item
   ```
   
   You should now be able to log in to the server as `root` on port 2200. The `-p` option explicitly tells at what port the SSH server operates on. It now no more operates on port number 22. 

### 5. Configure Timezone to Use UTC

To configure the timezone to use UTC, run the following command:

```
# sudo dpkg-reconfigure tzdata
```

It then shows you a list. Choose ``None of the Above`` and press enter. In the next step, choose ``UTC`` and press enter.


### 6. Setting Up the Firewall

Now we would configure the firewall to allow only incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123):

```
# sudo ufw allow 2200/tcp
# sudo ufw allow 80/tcp
# sudo ufw allow 123/udp
```

To enable the above firewall rules, run:

```
# sudo ufw enable
```

To confirm whether the above rules have been successfully applied or not, run:

```
# sudo ufw status
```

You should see something like this:

```
Status: active

To                         Action      From
--                         ------      ----
2200/tcp                   ALLOW       Anywhere
80/tcp                     ALLOW       Anywhere
123/udp                    ALLOW       Anywhere
2200/tcp (v6)              ALLOW       Anywhere (v6)
80/tcp (v6)                ALLOW       Anywhere (v6)
123/udp (v6)               ALLOW       Anywhere (v6)
```

### 7. Creating the User `grader` and Adding it to the `sudo` Group

#### 7.1. Creating the User `grader`

While being logged into the virtual server, run the following command and proceed:

```
  # sudo adduser grader
```

The output would look like this:

```
  Adding user `grader' ...
  Adding new group `grader' (1000) ...
  Adding new user `grader' (1000) with group `grader' ...
  Creating home directory `/home/grader' ...
  Copying files from `/etc/skel' ...
  Enter new UNIX password:
  Retype new UNIX password:
  passwd: password updated successfully
  Changing the user information for grader
  Enter the new value, or press ENTER for the default
	  Full Name []: Grader
	  Room Number []:
	  Work Phone []:
	  Home Phone []:
	  Other []:
  Is the information correct? [Y/n]
```

**Note**: Above, the UNIX password I have entered for the user `grader` is, `root`. 

#### 7.2. Adding `grader` to the Group `sudo`

Run the following command to add the user `grader` to the `sudo` group to grant it administrative access:

```
  # sudo nano /etc/sudoers.d/grader
```
and then add the following text

```
   grader ALL=(ALL:ALL) ALL
```


### 8. Adding SSH Access to the user `grader`

To allow SSH access to the user `grader`, first log into the account of the user `grader` from your virtual server:

```
# su - grader
```

You should see a prompt like this:

```console
grader@ubuntu-s-1vcpu-1gb-sfo2-01:~$

```

Now enter the following commands to allow SSH access to the user `grader`:

```
$ mkdir .ssh
$ chmod 700 .ssh
$ cd .ssh/
$ touch authorized_keys
$ chmod 644 authorized_keys
```

After you have run all the above commands, go back to your local machine and copy the content of the public key file ` cat ~/.ssh/item.pub`. Paste the public key to the server's `authorized_keys` file using `nano` or any other text editor, and save.

After that, run `exit`. You would now be back to your local machine. To confirm that it worked, run the following command in your local machine:

```console
subhadeep@VirtualBox:~$ ssh grader@157.230.128.59 -p 2200 -i ~/.ssh/item
```

You should now be able to log in as `grader` and would get a prompt to enter commands.

Next, run `exit` to go back to the host machine and proceed to the following step to disable `root` login.

### 9. Disabling Root Login

1. Run the following command on your local machine to log in as `root` in the server:
   ```
   $ ssh root@157.230.128.59 -p 2200 -i ~/.ssh/item
   ```

2. After you are logged in, open the file `/etc/ssh/sshd_config` with `nano`:
   ```
   # nano /etc/ssh/sshd_config
   ```

3. Find the line `PermitRootLogin yes` and change it to `PermitRootLogin no`.

4. Restart the SSH server:
   ```
   # service ssh restart
   ```

5. Terminate the connection:
   ```
   # exit
   ```

6. After you are back to the host machine, when you try to log in as `root`, you should get an error:

   ```console
   subhadeep@subhadeep-VirtualBox:~$ ssh root@157.230.128.59 -p 2200 -i ~/.ssh/item
   root@157.230.128.59: Permission denied (publickey).
   ```

### 10. Installing Apache Web Server

To install the Apache Web Server, run the following command after logging in as the `grader` user via SSH:

Install apache2, WSGI and Git using this command for python 2.7
```bash
$ sudo apt update
$ sudo apt-get install apache2 libapache2-mod-wsgi git
```

To confirm whether it successfully installed or not, enter the URL `http://157.230.128.59` in your Web browser:

If the installation has succeeded, you should see the following Webpage:

![Screenshot](https://res.cloudinary.com/sdey96/image/upload/v1527170572/Capture_seeiof.png)

### 11. Installing `pip`

The package `pip` or `pip3` will be required to install certain packages. 

If you are using Python 2, to install it, run:

```
$ sudo apt-get install python-pip
```

If you are using Python 3, to install it, run:

```
$ sudo apt install python3-pip
```


### 12. Installing and Configuring PostgreSQL

```bash
sudo apt-get install libpq-dev python-dev
sudo apt-get install postgresql postgresql-contrib
sudo su - postgres
psql
```

- Then create the Database and the schema.
```bash
CREATE USER catalog WITH PASSWORD 'grader';
CREATE DATABASE catalog WITH OWNER catalog;
\c catalog
REVOKE ALL ON SCHEMA public FROM public;
GRANT ALL ON SCHEMA public TO catalog;
\q
exit
```

### 13. Deploying The Catalog app

- First `cd /var/www/` .
- Make a new Directory `sudo mkdir catalog/` .
- Change the Owner `sudo chown grader:grader catalog/` .
- Clone the Project using this command
```bash
git clone https://github.com/esraayounis/CatalogUdacityProject.git catalog
```
- `cd catalog/` to move into the project .

**Note:** In `project.py` and `itemcatalog.py` `catalog_database.py` files you should change database engine to
```python
engine = create_engine('postgresql://catalog:grader@localhost/catalog')
```

#### 13.1. Set up Python environment

##### Configure app files

- Copy the client secret from your [Google Cloud APIs credentials dashboard](https://console.cloud.google.com/apis/credentials) into the *client_secrets.json* file on the server. Make sure the server's IP, and your domain name if you have one, have been added to the "Authorized JavaScript origins" and "Authorized redirect URIs."

  ```sh
  cd /var/www/catalog
  sudo touch g_client_secrets.json
  sudo nano g_client_secrets.json
  ```

- Modify the *application.py* file for the server:
  - Move `app.secret_key` out of the `if __name__ == '__main__'` block (which will not be used by the WSGI server), as recommended [here](https://stackoverflow.com/a/33898263).
  - Change paths to the *client_secrets.json* file, and any other external files, to absolute. Remember to modify the path in the Google Sign-In code.

  ```sh
  cd /var/www/catalog
  sudo nano project.py
  ```

  ```python
  # Obtain credentials from JSON file
  CLIENT_ID = json.loads(open('/var/www/catalog/g_client_secrets.json', 'r')
                        .read())['web']['client_id']
  CLIENT_SECRET = json.loads(open('/var/www/catalog/g_client_secrets.json', 'r')
                            .read())['web']['client_secret']
  redirect_uris = json.loads(open('/var/www/catalog/g_client_secrets.json', 'r')
                            .read())['web']['redirect_uris']
  app.secret_key = CLIENT_SECRET


  # Google Sign-In
  @app.route('/gconnect', methods=['POST'])
  def gconnect():
      """App route function for Google Sign-In."""
      # Confirm that client and server tokens match
      if request.args.get('state') != login_session['state']:
          response = make_response(json.dumps('Invalid state parameter.'), 401)
          response.headers['Content-Type'] = 'application/json'
          return response
      # Obtain authorization code
      code = request.data
      try:
          # Upgrade the authorization code into a credentials object
          oauth_flow = flow_from_clientsecrets('/var/www/catalog/g_client_secrets.json', scope='')
          oauth_flow.redirect_uri = 'postmessage'
          credentials = oauth_flow.step2_exchange(code)
      except FlowExchangeError:
          response = make_response(
              json.dumps('Failed to upgrade the authorization code.'), 401)
          response.headers['Content-Type'] = 'application/json'
          return response
      # (application code continues below)
  ```

#### 13.2. Creating wsgi.file 

- Create the app file uing `nano catalog.wsgi` in the directory `cd/var/www/catalog`.
- Then put this code into it
```python
import sys
sys.stdout = sys.stderr
activate_this = '/var/www/catalog/env/bin/activate_this.py'
with open(activate_this) as file_:
    exec(file_.read(), dict(__file__=activate_this))
sys.path.insert(0,"/var/www/catalog")
from project import app as application
```
**Note** replace the `project.py` file with your code application.

#### 13.3 Setup Virtual Environment and install dependencies

```bash
sudo apt-get install python-pip
sudo pip install virtualenv
virtualenv env
source env/bin/activate

sudo pip install --upgrade Flask SQLAlchemy httplib2 oauth2client requests psycopg2 psycopg2-binary
   ```
   

#### 14. Setting Up the VirtualHost Configuration

- Open this file `sudo nano /etc/apache2/sites-enabled/000-default.conf`
add the following code to it

```xml
# serve catalog app
<VirtualHost *:80>
  ServerName 157.230.128.59
  ServerAlias 157.230.128.59.xip.io
  ServerAdmin esraayounis61@gmail.com
  DocumentRoot /var/www/catalog
  WSGIDaemonProcess catalog user=grader group=grader
  WSGIScriptAlias / /var/www/catalog/catalog.wsgi

  <Directory /var/www/catalog>
    WSGIProcessGroup catalog
    WSGIApplicationGroup %{GLOBAL}
    Require all granted
  </Directory>

  ErrorLog ${APACHE_LOG_DIR}/error.log
  LogLevel warn
  CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```
- Restart the Apache2 Server
```bash
sudo service apache2 restart
```

- You are now good to go on the http://PUBLIC_IP/


## Debugging

If you are getting an _Internal Server Error_ or any other error(s), make sure to check out Apache's error log for debugging:

```
$ sudo cat /var/log/apache2/error.log
```

## References

[1] <https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-18-04>

[2] <http://terokarvinen.com/2016/deploy-flask-python3-on-apache2-ubuntu>

[3] <https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps>

[4] <https://modwsgi.readthedocs.io/en/develop/>
