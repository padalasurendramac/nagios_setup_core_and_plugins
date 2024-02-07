# nagios_setup_core_and_plugins
Nagios setup core and plugins 


## offical document page
click http://nagios.sourceforge.net/docs/nagioscore/4/en/quickstart.html

## Security-Enhanced Linux
This guide is based on SELinux being disabled or in permissive mode. SELinux is not enabled by default on Ubuntu. If you would like to see if it is installed run the following command:

sudo dpkg -l selinux*

## pre requirement 

===== Ubuntu 20.x =====

sudo apt-get update

sudo apt-get install -y autoconf gcc libc6 make wget unzip apache2 php libapache2-mod-php7.4 libgd-dev

sudo apt-get install openssl libssl-dev

## Downloading the Source

cd /tmp

wget -O nagioscore.tar.gz https://github.com/NagiosEnterprises/nagioscore/archive/nagios-4.4.14.tar.gz

tar xzf nagioscore.tar.gz


## Compile

cd /tmp/nagioscore-nagios-4.4.14/
sudo ./configure --with-httpd-conf=/etc/apache2/sites-enabled
sudo make all

## Create User And Group
This creates the nagios user and group. The www-data user is also added to the nagios group.

sudo make install-groups-users
sudo usermod -a -G nagios www-data

## Install Binaries

This step installs the binary files, CGIs, and HTML files.

sudo make install

## Install Service / Daemon
This installs the service or daemon files and also configures them to start on boot.

sudo make install-daemoninit

## Information on starting and stopping services will be explained further on.
This installs and configures the external command file.

sudo make install-commandmode

## Install Configuration Files
This installs the *SAMPLE* configuration files. These are required as Nagios needs some configuration files to allow it to start.

sudo make install-config

## Install Apache Config Files
This installs the Apache web server configuration files and configures Apache settings.

sudo make install-webconf
sudo a2enmod rewrite
sudo a2enmod cgi

## Configure Firewall
You need to allow port 80 inbound traffic on the local firewall so you can reach the Nagios Core web interface.


sudo ufw allow Apache
sudo ufw reload

## Create nagiosadmin User Account
You'll need to create an Apache user account to be able to log into Nagios.

The following command will create a user account called nagiosadmin and you will be prompted to provide a password for the account.


sudo htpasswd -c /usr/local/nagios/etc/htpasswd.users nagiosadmin

## Start Apache Web Server
===== Ubuntu 15.x / 16.x / 17.x / 18.x / 20.x =====

Need to restart it because it is already running.

sudo systemctl restart apache2.service

## Start Service / Daemon
This command starts Nagios Core.


===== Ubuntu 15.x / 16.x / 17.x / 18.x / 20.x =====

sudo systemctl start nagios.service

Test Nagios
Nagios is now running, to confirm this you need to log into the Nagios Web Interface.

Point your web browser to the ip address or FQDN of your Nagios Core server, for example:

http://10.25.5.143/nagios

http://core-013.domain.local/nagios


===========================================

## Documentation - Installing Nagios Plugins From Source

## Prerequisites
Make sure that you have the following packages installed.

sudo apt-get install -y autoconf gcc libc6 libmcrypt-dev make libssl-dev wget bc gawk dc build-essential snmp libnet-snmp-perl gettext


## Downloading The Source

cd /tmp
wget --no-check-certificate -O nagios-plugins.tar.gz https://github.com/nagios-plugins/nagios-plugins/archive/release-2.4.6.tar.gz
tar zxf nagios-plugins.tar.gz

## Compile + Install


cd /tmp/nagios-plugins-release-2.4.6/
sudo ./tools/setup
sudo ./configure
sudo make
sudo make install

## Test Plugins

Point your web browser to the ip address or FQDN of your Nagios Core server, for example:

http://10.25.5.143/nagios

http://core-013.domain.local/nagios

Go to a host or service object and "Re-schedule the next check" under the Commands menu. The error you previously saw should now disappear and the correct output will be shown on the screen.


## Service / Daemon Commands

Different Linux distributions have different methods of starting / stopping / restarting / status Nagios.


 

===== Ubuntu 15.x / 16.x / 17.x / 18.x / 20.x =====

sudo systemctl start nagios.service
sudo systemctl stop nagios.service
sudo systemctl restart nagios.service
sudo systemctl status nagios.service

