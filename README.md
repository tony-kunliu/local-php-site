# local-php-site
Get Apache, MySQL, PHP and phpMyAdmin working on macOS Sierra
September 23, 2016 79 Comments
Get your Local Web Development Environment Up & Running on macOS Sierra

macos-sierra-apache

With Apples’ new macOS Sierra now in public beta, here is how to get the AMP stack up and running on the new macOS. This tutorial will go through the process on getting Apache, MySQL, PHP (or otherwise known as the ‘AMP’ stack) and phpMyAdmin running on the new mac OS Sierra.

This tutorial sets up the AMP stack in more of a traditional way using the loaded Apache and PHP and downloading MySQL and phpMyAdmin.

Setting Stuff Up

Web Sharing / Apache
Webroot – System and User level
.htaccess overrides and mod rewrites
PHP
MySQL
phpMyAdmin
Permissions
Apache/WebSharing

Web serving is possible via the inbuilt Apache app, it is installed ready to be fired up.

This needs to be done in the Terminal which is found at /Applications/Utilities/Terminal

For those not familiar with the Terminal, it really isn’t as intimidating as you may think, once launched you are faced with a command prompt waiting for your commands – just type/paste in a command and hit enter, some commands give you no response – it just means the command is done, other commands give you feedback.

Using the prefix of sudo is required for commands that have their applications protected in certain folders – when using sudo you will need to confirm with your admin password or iCloud password if set up that way…. lets get to it….

to start Apache web sharing

sudo apachectl start
to stop it

sudo apachectl stop
to restart it

sudo apachectl restart
To find the Apache version

httpd -v
The Apache version that comes in macOS Sierra is Apache/2.4.23

localhost-osx-apache
It Works!

After starting Apache – test to see if the webserver is working in the browser – http://localhost – you should see the “It Works!” text.

If you don’t get the localhost test, you can try troubleshooting Apache to see if there is anything wrong in its config file by running

apachectl configtest
This will give you an indication of what might be wrong.

Document Root

Document root is the location where the files are shared from the file system and is similar to the traditional names of ‘public_html‘ and ‘htdocs‘, OSX has historically had 2 web roots one at a system level and one at a user level – you can set both up or just run with one, the user level one allows multiple accounts to have their own web root whilst the system one is global for all users. It seems there is less effort from Apple in continuing with the user level one but it still can be set up with a couple of extra tweaks in configuration files. It is easier to use the user level one as you don’t have to keep on authenticating as an admin user.

System Level Web Root

– the default system document root is still found at –

http://localhost/

The files are shared in the filing system at –

/Library/WebServer/Documents/
User Level Root

The other web root directory which is missing by default is the ‘~/Sites’ folder in the User account. This takes a bit longer to set up but some users are very accustomed to using it.

You need to make a “Sites” folder at the root level of your account and then it will work. Once you make the Sites folder you will notice that it has a unique icon which is a throwback from a few versions older. Make that folder before you set up the user configuration file described next.

You have to make a few additional tweaks to get the ~/Sites folder back up and running.

user-level-webroot-sites-folder
Sites Folder

Add  a “username.conf” filed under:

/etc/apache2/users/
If you don’t already have one (very likely), then create one named by the short username of the account with the suffix .conf, its location and permissions/ownership is best tackled by using the Terminal, the text editor ‘nano‘ would be the best tool to deal with this.

Launch Terminal, (Applications/Utilities), and follow the commands below, first one gets you to the right spot, 2nd one cracks open the text editor on the command line (swap ‘username‘ with your account’s shortname, if you don’t know your account shortname type ‘whoami‘ the Terminal prompt):

cd /etc/apache2/users
sudo nano username.conf
Then add the content below swapping in your ‘username’ in the code below:

<Directory "/Users/username/Sites/">
AllowOverride All
Options Indexes MultiViews FollowSymLinks
Require all granted
</Directory>
Permissions on the file should be:

-rw-r--r--   1 root  wheel  298 Jun 28 16:47 username.conf
If not you need to change…

sudo chmod 644 username.conf
Open the main httpd.conf and allow some modules:

sudo nano /etc/apache2/httpd.conf
And make sure these modules are uncommented (the first 2 should already be on a clean install):

LoadModule authz_core_module libexec/apache2/mod_authz_core.so
LoadModule authz_host_module libexec/apache2/mod_authz_host.so
LoadModule userdir_module libexec/apache2/mod_userdir.so
LoadModule include_module libexec/apache2/mod_include.so
LoadModule rewrite_module libexec/apache2/mod_rewrite.so
 

Whilst you have this file open also to get php running uncomment. (Mentioned also in the PHP part of the article).

LoadModule php5_module libexec/apache2/libphp5.so
And also uncomment this configuration file also in httpd.conf – which allows user home directories.

Include /private/etc/apache2/extra/httpd-userdir.conf
Save all your changes (Control + O in nano)

Then open another Apache config file and uncomment another file:

sudo nano /etc/apache2/extra/httpd-userdir.conf
And uncomment:

Include /private/etc/apache2/users/*.conf
Save all your changes (Control + O in nano)

 

Restart Apache for the new file to be read:

sudo apachectl restart
Then this user level document root will be viewable at:

http://localhost/~username/

You should only see a directory tree like structure if the folder is empty.



Override .htaccess and allow URL Rewrites

If you are going to use the web serving document root at /Library/WebServer/Documents it is a good idea to allow any .htaccess files used to override the default settings – this can be accomplished by editing the httpd.conf file at line 217 and setting the AllowOverride to All and then restart Apache. This is already taken care of at the Sites level webroot by following the previous step.

sudo nano /etc/apache2/httpd.conf
 

osx-htaccess-override
osx-htaccess-override

Also whilst here allow URL rewrites so your permalinks look clean not ugly.

Uncomment in httpd.conf – should be uncommented on a clean install.

LoadModule rewrite_module libexec/apache2/mod_rewrite.so
 

PHP

PHP 5.6.24 is loaded in the build of macOS Sierra and needs to be turned on by uncommenting a line in the httpd.conf file.

sudo nano /etc/apache2/httpd.conf
Use “control” + “w” to search within nano and search for ‘php’ this will land you on the right line then uncomment the line (remove the #):

LoadModule php5_module libexec/apache2/libphp5.so
Write out and Save using the nano short cut keys at the bottom ‘control o’ and ‘control x’

Reload apache to kick in

sudo apachectl restart
To see and test PHP, create a file name it “phpinfo.php” and file it in your document root with the contents below, then view it in a browser.

 <?php phpinfo(); ?>
macos-sierra-php

MySQL

The macOS Sierra Public Beta’s didn’t play well with MySQL 5.7.x, but these issues are now resolved by using MySQL 5.7.16

MySQL doesn’t come pre-loaded with macOS Sierra and needs to be dowloaded from the MySQL site.

The latest version of MySQL 5.7.16 does work with the public release of macOS.

If you already have MySQL 5.7 and you have upgraded OS from El Capitan to Sierra I expect that to be ok, but will be interested if anyone comments on that.

Use the Mac OS X 10.11 (x86, 64-bit), DMG Archive version (works on macOS Sierra).

If you are upgrading from a previous OSX and have an older MySQL version you do not have to update it. One thing with MySQL upgrades always take a data dump of your database in case things go south and before you upgrade to macOS Sierra make sure your MySQL Server is not running.

When downloading you don’t have to sign up, look for » No thanks, just take me to the downloads!  – go straight to the download mirrors and download the software from a mirror which is closest to you.

Once downloaded open the .dmg and run the installer.

When it is finished installing you get a dialog box with a temporary mysql root password – that is a MySQL root password not a macOS admin password, copy and paste it so you can use it. But I have found that the temporary password is pretty much useless so we’ll need to change it straight away.

You are also told:

If you lose this password, please consult the section How to Reset the Root Password in the MySQL reference manual.

mysql-password-macos-sierra

Change the MySQL root password

Note that this is not the same as the root or admin password of macOS – this is a unique password for the mysql root user, use one and remember/jot down somewhere what it is.

Stop MySQL

sudo /usr/local/mysql/support-files/mysql.server stop
Start it in safe mode:

sudo mysqld_safe --skip-grant-tables
This will be an ongoing command until the process is finished so open another shell/terminal window, and log in without a password as root:

mysql -u root
FLUSH PRIVILEGES;
ALTER USER 'root'@'localhost' IDENTIFIED BY 'MyNewPass';
Change the lowercase ‘MyNewPass’ to what you want – and keep the single quotes.

\q
Start MySQL

sudo /usr/local/mysql/support-files/mysql.server start
Starting MySQL

You can then start the MySQL server from the System Preferences or via the command line.

macos-sierra-mysql

 

osx-yosemite-mysql

Command line start MySQL.

sudo /usr/local/mysql/support-files/mysql.server start
To find the MySQL version from the terminal, type at the prompt:

/usr/local/mysql/bin/mysql -v -uroot -p
This also puts you in to a shell interactive dialogue with mySQL, type \q to exit.

After installation, in order to use mysql commands without typing the full path to the commands you need to add the mysql directory to your shell path, (optional step) this is done in your “.bash_profile” file in your home directory, if you don’t have that file just create it using vi or nano:

cd ; nano .bash_profile
export PATH="/usr/local/mysql/bin:$PATH"
The first command brings you to your home directory and opens the .bash_profile file or creates a new one if it doesn’t exist, then add in the line above which adds the mysql binary path to commands that you can run. Exit the file with type “control + x” and when prompted save the change by typing “y”. Last thing to do here is to reload the shell for the above to work straight away.

source ~/.bash_profile
mysql -v
You will get the version number again, just type “q” to exit.

Fix the 2002 MySQL Socket error

Fix the looming 2002 socket error – which is linking where MySQL places the socket and where macOS thinks it should be, MySQL puts it in /tmp and macOS looks for it in /var/mysql the socket is a type of file that allows mysql client/server communication.

sudo mkdir /var/mysql
sudo ln -s /tmp/mysql.sock /var/mysql/mysql.sock
phpMyAdmin

First fix the 2002 socket error if you haven’t done so from the MySQL section-

sudo mkdir /var/mysql
sudo ln -s /tmp/mysql.sock /var/mysql/mysql.sock
Download phpMyAdmin, the zip English package will suit a lot of users, then unzip it and move the folder with its contents into the document root level renaming folder to ‘phpmyadmin’.

Make the config folder

mkdir ~/Sites/phpmyadmin/config
Change the permissions

chmod o+w ~/Sites/phpmyadmin/config
Run the set up in the browser

http://localhost/~username/phpmyadmin/setup/ or http://localhost/phpmyadmin/setup/



You need to create a new localhost mysql server connection, click new server.

 


Switch to the Authentication tab and set the local mysql root user and the password.
Add in the username “root” (maybe already populated, add in the password that you set up earlier for the MySQL root user set up, click on save and you are returned to the previous screen.
(This is not the macOS Admin or root password – it is the MySQL root user).


Make sure you click on save, then a config.inc.php is now in the /config directory of phpmyadmin directory, move this file to the root level of /phpmyadmin and then remove the now empty /config directory.

 

Now going to http://localhost/~username/phpmyadmin/ will now allow you to interact with your MySQL databases.



To upgrade phpmyadmin just download the latest version and copy the older ‘config.inc.php‘ from the existing directory into the new folder and replace – backup the older one just in case.

Permissions

To run a website with no permission issues it is best to set the web root and its contents to be writeable by all, since it’s a local development it shouldn’t be a security issue.

Lets say that you have a site in the User Sites folder at the following location ~/Sites/testsite you would set it to be writeable like so:

sudo chmod -R a+w ~/Sites/testsite
If you are concerned about security then instead of making it world writeable you can set the owner to be Apache _www but when working on files you would have to authenticate more as admin you are “not” the owner, you would do this like so:

sudo chown -R _www ~/Sites/testsite
This will set the contents recursively to be owned by the Apache user.

If you had the website stored at the System level Document root at say /Library/WebServer/Documents/testsite then it would have to be the latter:

sudo chown -R _www /Library/WebServer/Documents/testsite
Another easier way to do this if you have a one user workstation is to change the Apache web user from _www to your account.

That’s it! You now have the native AMP stack running on top of macOS Sierra.
