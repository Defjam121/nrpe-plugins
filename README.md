# Nagios Plugins

This is a collection of my custom Nagios/NRPE compatible plugins.

## check_snorby

This script queries the Snorby (<https://github.com/Snorby/snorby>) database for High severity events in a time interval. The time interval is defined by `-i` argument, the accepted arguments are integer values. 

Before executing the script you need to modify the database connection values defined inside the script.

```
./check_snorby.sh -i <minutes>
```

### Output
```
OK - 0 High severity events in the defined interval
CRITICAL - 1 or more High severity events in the defined interval
UNKNOWN - something hasn't been set properly
```

### Examples
```
$ ./check_snorby.sh -i 5
OK - 0 High severity events in the defined interval

$ ./check_snorby.sh -i 60000
CRITICAL - 14 High severity events in the defined interval

$ ./check_snorby.sh -i z
UNKNOWN - set the time interval to a integer value
```

### Configuration in Nagios

* Copy the script to your nagios plugin directory (usually `/usr/lib64/nagios/plugins/`)
* Create the NRPE command, adding the following line to `/etc/nagios/nrpe.cfg`
```
command[check_snorby]=/usr/lib64/nagios/plugins/check_snorby.sh -i $ARG1$
```
* Create the service check adding the following definition in a .cfg file on `/etc/nagios/conf.d/`
```
define service{
        use                             service-template
        name                            Snorby High events
        host_name                       hostname
        service_description             Snorby High events
        check_command                   check_nrpe!check_snorby!30
}
```

## check_container_cpu

This script checks the docker container internal CPU usage, it retrieves the values using docker stats.

```
sudo ./check_container_cpu.sh <container name> -w <warning threshold> -c <critical threshold>
```

### Output
```
OK       - if the CPU usage is under the warning and critical thresholds
WARNING  - if the CPU usage is equal or over the warning threshold and it is under the critical threshold
CRITICAL - if the CPU usage is equal or over the critical threshold
UNKNOWN  - if the container does not exist
```

### Examples
```
$ sudo ./check_container_cpu.sh container -w 80 -c 95
OK - CPU Usage = 7%

$ sudo ./check_container_cpu.sh container -w 80 -c 95
CRITICAL - CPU Usage = 97%

$ sudo ./check_container_cpu.sh notexist -w 80 -c 95
UNKNOWN - notexist does not exist.
```

### Configuration in Nagios

* Copy the script to your nagios plugin directory (usually `/usr/lib64/nagios/plugins/`)
* This plugin needs sudo, so edit the sudoers config file as follow
```
$ sudo visudo

#Defaults    requiretty

# Enable nagios to run restricted root checks
nrpe        ALL=(ALL) NOPASSWD: /usr/lib64/nagios/plugins/check_container_cpu.sh
```
* Create the NRPE command, adding the following line to `/etc/nagios/nrpe.cfg`
```
command[check_container_cpu_usage]=sudo /usr/lib64/nagios/plugins/check_container_cpu.sh $ARG1$ -w $ARG2$ -c $ARG3$
```
* Create the service check adding the following definition in a .cfg file on `/etc/nagios/conf.d/`
```
define service{
        use                             service-template
        name                            Container CPU
        host_name                       hostname
        service_description             Container CPU
        check_command                   check_nrpe!check_container_cpu_usage!container!80!95
}
```

## check_container_memory

This script checks the docker container internal Memory usage, it retrieves the values using docker stats.

```
sudo ./check_container_memory.sh <container name> -w <warning threshold> -c <critical threshold>
```

### Output
```
OK       - if the Memory usage is under the warning and critical thresholds
WARNING  - if the Memory usage is equal or over the warning threshold and it is under the critical threshold
CRITICAL - if the Memory usage is equal or over the critical threshold
UNKNOWN  - if the container does not exist
```

### Examples
```
$ sudo ./check_container_memory.sh container -w 80 -c 95
OK - MEMORY Usage = 4.82%

$ sudo ./check_container_memory.sh container -w 80 -c 95
CRITICAL - - MEMORY Usage = 96.79%

$ sudo ./check_container_memory.sh notexist -w 80 -c 95
UNKNOWN - notexist does not exist.
```

### Configuration in Nagios

* Copy the script to your nagios plugin directory (usually `/usr/lib64/nagios/plugins/`)
* This plugin needs sudo, so edit the sudoers config file as follow
```
$ sudo visudo

#Defaults    requiretty

# Enable nagios to run restricted root checks
nrpe        ALL=(ALL) NOPASSWD: /usr/lib64/nagios/plugins/check_container_memory.sh
```
* Create the NRPE command, adding the following line to `/etc/nagios/nrpe.cfg`
```
command[check_container_memory_usage]=sudo /usr/lib64/nagios/plugins/check_container_memory.sh $ARG1$ -w $ARG2$ -c $ARG3$
```
* Create the service check adding the following definition in a .cfg file on `/etc/nagios/conf.d/`
```
define service{
        use                             service-template
        name                            Container Memory
        host_name                       hostname
        service_description             Container Memory
        check_command                   check_nrpe!check_container_memory_usage!container!80!95
}
```

License
----


GNU GENERAL PUBLIC LICENSE Version 3