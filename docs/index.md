<h1 align="center">Getting Started with CD-MOJ</h1>
<h6 align="center">An introductory tutorial!</h6>

-----------------------------------------------------

## Requirements

 - Git >= 2.30.2-1
 - Apache >= 2.4.38-39

## Installation
To install cdmoj, run the followings commands from the command line:

* `git clone https://github.com/cd-moj/cdmoj.git` - Clone the project
* `cd cdmoj/` - Enter directory
* `bash install.sh /home/$USER/moj-serverside /home/$USER/moj-pagina` - Create project folders and install


## Settings

#### Configuring apache CGI bin

* `sudo nano /etc/apache2/apache2.conf` - Access the apache configuration file
* Add the following snippet to the end of the file:

````
    <Directory /home/$USER/moj-pagina/>
        Options +ExecCGI
        AddHandler cgi-script .cgi .sh
    </Directory>`
````

* `sudo ln -s /etc/apache2/mods-available/cgi.load /etc/apache2/mods-enabled/cgi.load` - Run the following command to let apache run cgi
* `sudo nano /etc/apache2/conf-available/serve-cgi-bin.conf` - Access the cgi-bin server file

Inside the file it will look something like this:

````
<IfModule mod_alias.c>
        <IfModule mod_cgi.c>
                Define ENABLE_USR_LIB_CGI_BIN
        </IfModule>

        <IfModule mod_cgid.c>
                Define ENABLE_USR_LIB_CGI_BIN
        </IfModule>

        <IfDefine ENABLE_USR_LIB_CGI_BIN>
                ScriptAlias /cgi-bin/ /usr/lib/cgi-bin/
                <Directory "/usr/lib/cgi-bin/">
                        AllowOverride None
                        Options +ExecCGI -MultiViews +SymLinksIfOwnerMatch
                        Require all granted
                </Directory>
        </IfDefine>
    </IfModule>
````

Change the following lines and save the file:

````
ScriptAlias /cgi-bin/  /home/$USER/moj-pagina/cgi-bin/
<Directory "/home/$USER/moj-pagina/cgi-bin/">
````

* `sudo nano /etc/apache2/sites-available/moj.conf` - Create a file for the moj site

This file should look like:

````
<VirtualHost *:80>
        ServerName moj.naquadah.com.br

        ServerAdmin webmaster@localhost
        DocumentRoot /home/$USER/moj-pagina/

        ErrorLog ${APACHE_LOG_DIR}/moj.naquadah.com.br-error.log
        CustomLog ${APACHE_LOG_DIR}/moj.naquadah.com.br-access.log combined

        Include conf-available/serve-cgi-bin.conf

        ScriptAlias /cgi-bin/ /home/$USER/moj-pagina/cgi-bin/
<Directory "/">
        Options Indexes FollowSymLinks MultiViews Includes
        Require all granted
</Directory>
</VirtualHost>
````
* `sudo a2dissite 000-default` - Disable default site

* `sudo a2ensite moj` - Enable the site

* `sudo systemctl reload apache2` - Restart apache

* `sudo nano /home/$USER/moj-serverside/etc/common.conf` - Edit the common.conf file which is inside the moj-serverside directory

This file should look like:

````
CACHEDIR=/tmp/
CONTESTSDIR=/home/$USER/moj/contests
SUBMISSIONDIR=/home/$USER/moj/submissions
BASEURL="http://localhost"
HTMLDIR=/home/$USER/moj-pagina
````

## Project layout

    mkdocs.yml    # The configuration file.
    docs/
        index.md  # The documentation homepage.
        ...       # Other markdown pages, images and other files.
