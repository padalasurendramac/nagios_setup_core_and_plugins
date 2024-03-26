Nagios client installation on latest RHEL instance 




step:-1 selinux disable 

getenforce
setenforce 0
sestatus
(OR)
vi /etc/selinux/config
SELINUX=disabled


then updated below changes in nrpe.cfg file 

sed -i 's/^allowed_hosts=127.*/allowed_hosts=127.0.0.1,172.30.2.46/g' /etc/nagios/nrpe.cfg
sed -i 's/^dont_blame_nrpe=.*/dont_blame_nrpe=1/g' /etc/nagios/nrpe.cfg

grep allowed_hosts /etc/nagios/nrpe.cfg
grep dont_blame_nrpe=1 /etc/nagios/nrpe.cfg




step:-2. yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm

step:-3. yum install nrpe nagios-plugins-users nagios-plugins-load nagios-plugins-swap nagios-plugins-disk nagios-plugins-procs

step:-4. create common_check.cfg & custom_checks.cfg under /etc/nrpe.d/

note:- updated the keycloak ip address in this below check 
command[keycloak_process]=/usr/lib64/nagios/plugins/check_port.pl -p 8080 -h < keycloak ip address> -c 1.5 -w 1.0 -v

[root@ip-172-10-4-223 ~]# cat /etc/nrpe.d/common_check.cfg
command[check_users]=/usr/lib64/nagios/plugins/check_users -w 10 -c 15
command[check_sys_load]=/usr/lib64/nagios/plugins/check_load -w 15,10,5 -c 30,25,20
command[check_root_partition]=/usr/lib64/nagios/plugins/check_disk -w 20% -c 10% -p /
command[check_core-engine_partition]=/usr/lib64/nagios/plugins/check_disk -w 20% -c 10% -p /core-engine
command[check_zombie_procs]=/usr/lib64/nagios/plugins/check_procs -w 5 -c 10 -s Z
command[check_total_procs]=/usr/lib64/nagios/plugins/check_procs -w 250 -c 300
command[check_memory_usage]=/usr/lib64/nagios/plugins/check_memory -fC -w 12 -c 10
command[check_ngnix_service]=/usr/lib64/nagios/plugins/check_procs -c 1:20 -C nginx
command[check_ssh]=/usr/lib64/nagios/plugins/check_ssh localhost
command[check_dev_partition]=/usr/lib64/nagios/plugins/check_disk -w 20% -c 10% -p /dev
[root@ip-172-10-4-223 ~]#

[root@ip-172-10-4-223 ~]# cat /etc/nrpe.d/custom_checks.cfg
command[check_http_port]=/usr/lib64/nagios/plugins/check_port.pl -p 80 -h 172.50.2.14 -c 1.5 -w 1.0 -v
command[check_https_port]=/usr/lib64/nagios/plugins/check_port.pl -p 443 -h 127.0.0.1 -c 1.5 -w 1.0 -v
command[check_widgetplugin_port]=/usr/lib64/nagios/plugins/check_port.pl -p 3000 -h 172.50.2.14 -c 1.5 -w 1.0 -v
command[check_widgethandler_port]=/usr/lib64/nagios/plugins/check_port.pl -p 8080 -h localhost -c 1.5 -w 1.0 -v
command[check_qa_bot_port]=/usr/lib64/nagios/plugins/check_port.pl -p 4008 -h localhost -c 1.5 -w 1.0 -v
command[check_qabot_port]=/usr/lib64/nagios/plugins/check_port.pl -p 4010 -h localhost -c 1.5 -w 1.0 -v
command[check_intentdirectory_port]=/usr/lib64/nagios/plugins/check_port.pl -p 8081 -h localhost -c 1.5 -w 1.0 -v
command[check_coreengine_port]=/usr/lib64/nagios/plugins/check_port.pl -p 8082 -h localhost -c 1.5 -w 1.0 -v
command[check_solr_port]=/usr/lib64/nagios/plugins/check_port.pl -p 8983 -h localhost -c 1.5 -w 1.0 -v
command[check_rabbitmq_service]=/usr/lib64/nagios/plugins/check_procs -c 1:5 -C beam.smp
command[check_sms_platform_handler]=/usr/lib64/nagios/plugins/check_port.pl -p 3000 -h localhost -c 1.5 -w 1.0 -v
command[check_widget_server]=/usr/lib64/nagios/plugins/check_port.pl -p 3008 -h localhost -c 1.5 -w 1.0 -v
command[check_widget_platform_handler]=/usr/lib64/nagios/plugins/check_port.pl -p 8080 -h localhost -c 1.5 -w 1.0 -v
command[check_uptime]=/usr/lib64/nagios/plugins/check_uptime.pl -f -w 5 -c 10
command[check_iostat]=/usr/lib64/nagios/plugins/check_iostat -d nvme0n1 -w 5000000,10000000,10000000,8 -c 6000000,20000000,20000000,10
command[check_cpu_usage]=/usr/lib64/nagios/plugins/check_cpu.sh -w 80 -c 90
command[keycloak_process]=/usr/lib64/nagios/plugins/check_port.pl -p 8080 -h <ip address> -c 1.5 -w 1.0 -v
command[check_keycloak-monitor_script_process]=/usr/lib64/nagios/plugins/check_procs -c 1:20 -C keycloak-monitor.sh




step:- 4 copy these following checks to /usr/lib64/nagios/plugins/ path then give execute permission these files 

check_uptime.pl
check_iostat
check_cpu.sh
check_port.pl
check_memory
check_ssh


step:- 5 install these dependency packages in instance
note:- check_iostat ( dependency ) 
yum install sysstat

yum install bc

note:- *.pl ( dependency ) 
yum install perl
step:-6 start nrpe service and enable also 

systemctl start nrpe
systemctl status nrpe
systemctl enable nrpe

