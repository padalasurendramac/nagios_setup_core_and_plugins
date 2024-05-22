Agent configuring the Nagios 

step:- 1 first need to open 22 & all icmp & 5666 port for prod nagios server (34.203.151.231)

step:- 2 install nagios agent ( nrpe ) at client server 

OS:- 

       2a:- sudo apt install nagios-nrpe-server nagios-plugins
	   2b:- stop and disable nagios-nrpe-server service 
	   systemctl stop nagios-nrpe-server
	   systemctl disable nagios-nrpe-server
	   
	   2c:- create service file for nrpe agent like below
	   
		root@ip-10-0-2-107:~# cat /etc/systemd/system/nrpe.service
		[Unit]
		Description=Nagios Remote Plugin Executor
		After=network.target

		[Service]
		Type=forking
		ExecStart=/usr/sbin/nrpe -c /etc/nagios/nrpe.cfg -d
		Restart=on-failure

		[Install]
		WantedBy=multi-user.target
		root@ip-10-0-2-107:~#
		
		2d:- update prod nagios ip address at /etc/nagios/nrpe.cfg file like below 

		root@ip-10-1-0-26:~# cat /etc/nagios/nrpe.cfg | grep -i allowed_host
		allowed_hosts=127.0.0.1,::1,34.203.151.231
		root@ip-10-1-0-26:~# date
		Tue May 21 14:42:22 EDT 2024
		root@ip-10-1-0-26:~#

		2e :- start and enable the nrpe service 
		systemctl start nrpe 
		systemctl enable nrpe 


step:- 3 nagios server 

create cfg file at /usr/local/nagios/etc/server path like below ( note:- change the ip address and host name correspondingly) 

root@ip-10-0-0-29:~# cat /usr/local/nagios/etc/servers/vh-test-etl.cfg
define host {
    use                     linux-server ; Inherit default values from a template
    host_name               vh-test-etl
    alias                   ssh_checks
#    address                 127.0.0.1
    address                 52.73.57.49
    max_check_attempts      5
    check_period            24x7
    check_interval          1
    retry_interval          1
    check_command           check-host-alive
    notification_period     24x7
    notification_interval   60
    notification_options    d,r
    contact_groups          admins
    register                1
}
root@ip-10-0-0-29:~#

		
then updated the hostgroup file under path /usr/local/nagios/etc/objects/hostgroup.cfg like below for ec2 checks


root@ip-10-0-0-29:~# cat /usr/local/nagios/etc/objects/hostgroups.cfg | tail -7


define hostgroup{
        hostgroup_name ec2_checks
        alias          EC2_CHECKS
        members        vh-security-test-openvpn,vh-security-gitlab-runner,vh-test-etl
}
root@ip-10-0-0-29:~#

Note:- ec2 checks are defined at /usr/local/nagios/etc/objects/service.cfg file 


step:-4 run the nagios verify command 
/usr/local/nagios/bin/nagios -v /usr/local/nagios/etc/nagios.cfg

if there is no error then restart the nagios 

step:-5 systemctl restart nagios 
systemctl status nagios 


please verify the ec2 server at prod nagios console 
