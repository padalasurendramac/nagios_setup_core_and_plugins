
##Os ubuntu


##step:- 1 enable the ssl mode 


sudo a2enmod ssl

##step:- 2 enable default ssl conf file first 

sudo a2ensite default-ssl.conf

Note:- after that update the following ssl file path

SSLCertificateFile      /etc/ssl/certificate.pem


SSLCertificateKeyFile /etc/ssl/private.key

##step:- 3 reload the apache2  service 

##Redirect http to https 

##update the following property at 000-default.conf

Redirect permanent / https://our.server.org/ 

note:- between the virtualhost for 80


