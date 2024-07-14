# Install-and-configure-WordPress-on-Ubuntu
### Note: Deployed this project as a learning exercise, leveraging official Ubuntu documentation and guides to ensure best practices and robust implementation.

## 1. Install Dependencies

sudo apt install apache2 \
                 ghostscript \
                 libapache2-mod-php \
                 mysql-server \
                 php \
                 php-bcmath \
                 php-curl \
                 php-imagick \
                 php-intl \
                 php-json \
                 php-mbstring \
                 php-mysql \
                 php-xml \
                 php-zip


## 2. Install WordPress

Create the installation directory and download the file from 
~~~
sudo mkdir -p /srv/www
sudo chown www-data: /srv/www
~~~
### note:
We will use the release from WordPress.org rather than the APT package in the Ubuntu Archive, because this is the preferred method from upstream WordPress.

~~~
curl https://wordpress.org/latest.tar.gz | sudo -u www-data tar zx -C /srv/www
~~~
### 3. Configure Apache for WordPress
Add following lines to  
 /etc/apache2/sites-available/wordpress.conf
 ~~~
 <VirtualHost *:80>
    DocumentRoot /srv/www/wordpress
    <Directory /srv/www/wordpress>
        Options FollowSymLinks
        AllowOverride Limit Options FileInfo
        DirectoryIndex index.php
        Require all granted
    </Directory>
    <Directory /srv/www/wordpress/wp-content>
        Options FollowSymLinks
        Require all granted
    </Directory>
</VirtualHost>
 ~~~

#### a. Enable the site with:
~~~
sudo a2ensite wordpress
~~~
#### b. Enable URL rewriting with:
~~~
sudo a2enmod rewrite
~~~
#### c.  Disable the default “It Works” site with:
~~~
sudo a2dissite 000-default
~~~
#### d. Finally, reload apache2 to apply all these changes:
~~~
Finally, reload apache2 to apply all these changes:
~~~
### 4. Configure database
~~~
$ sudo mysql -u root
~~~
#### a.  Creating a Database
~~~
mysql> CREATE DATABASE wordpress;
Query OK, 1 row affected (0.00 sec)
~~~
#### b. Creating a User
~~~
mysql> CREATE USER wordpress@localhost IDENTIFIED BY '<your-password>';
Query OK, 1 row affected (0.00 sec)
~~~
#### c. Granting Privileges
~~~
mysql> GRANT SELECT,INSERT,UPDATE,DELETE,CREATE,DROP,ALTER
    -> ON wordpress.*
    -> TO wordpress@localhost;
Query OK, 1 row affected (0.00 sec)
~~~
#### d. Flushing Privileges 
~~~
mysql> FLUSH PRIVILEGES;
Query OK, 1 row affected (0.00 sec)
~~~
##### This command reloads the privilege tables in MySQL, ensuring that any changes made to the privileges (like the ones you just granted) take effect immediately. This step is necessary whenever you modify user privileges.

#### e. Exiting MySQL Monitor
~~~
mysql> quit
Bye
~~~
### 5. Configure WordPress to connect to the database
Now, let’s configure WordPress to use this database. First,
#### a. copy the sample configuration file to wp-config.php
~~~
sudo -u www-data cp /srv/www/wordpress/wp-config-sample.php /srv/www/wordpress/wp-config.php
~~~
#### b. set the database credentials in the configuration file
~~~
sudo -u www-data sed -i 's/database_name_here/wordpress/' /srv/www/wordpress/wp-config.php
sudo -u www-data sed -i 's/username_here/wordpress/' /srv/www/wordpress/wp-config.php
sudo -u www-data sed -i 's/password_here/<your-password>/' /srv/www/wordpress/wp-config.php
~~~ 
#### Note:
Do not replace database_name_here or username_here in the commands below. Do replace <your-password> with your database password.

#### c. open the configuration file in nano and Update Security Keys and Salts
~~~
sudo -u www-data nano /srv/www/wordpress/wp-config.php
~~~
find the following lines and Delete them.
~~~
define( 'AUTH_KEY',         'put your unique phrase here' );
define( 'SECURE_AUTH_KEY',  'put your unique phrase here' );
define( 'LOGGED_IN_KEY',    'put your unique phrase here' );
define( 'NONCE_KEY',        'put your unique phrase here' );
define( 'AUTH_SALT',        'put your unique phrase here' );
define( 'SECURE_AUTH_SALT', 'put your unique phrase here' );
define( 'LOGGED_IN_SALT',   'put your unique phrase here' );
define( 'NONCE_SALT',       'put your unique phrase here' );
~~~
Go to https://api.wordpress.org/secret-key/1.1/salt/ to generate random keys and salts.
![alt text](<Screenshot 2024-06-20 170841.png>)
Copy the entire content from the webpage and paste it into the wp-config.php file where you deleted the previous lines and save the file.

#### Note:
This step is important to ensure that your site is not vulnerable to “known secrets” attacks.

### 6. Configure WordPress
#### a. Open http://localhost/ in your browser
You should see the WordPress installation page where it prompts you to set up your site.

Fill in the Required Details.

You can now login under http://localhost/wp-login.php. In the WordPress Dashboard, you will see bunch of icons and options.

By following these steps, you should be able to complete the WordPress installation and access it.









