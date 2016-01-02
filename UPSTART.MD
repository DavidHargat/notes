# Upstart Notes

## Background

```
Traditionally when linux boots there is an 'init' program
to help get everything set up (/sbin/init).

init is synchronous, it linearly handles one task after the other
until it's finished.

'Upstart' is one asynchronous replacement for init. Upstart also
has the concept of 'events' which is useful for handling tasks that
occur after the initial boot (such as initializing a device driver
when something is plugged in).
```

## Upstart Concepts

```
- Jobs
	think Processes

- Events
	signals which can be used to trigger
	a job or other events

- Emitting Events
	events are usually caused by a process
	or job, though you can emit them manually
	initctl emit <event>
```

## Writing a Job Configuration File


Move to the init directory.


`cd /etc/init`


Create a .conf file (may need root access (sudo)).


`touch testjob.conf`


Now we can start!


Jobs start with 2 basic stanzas:


```
description "Test job with Upstart"
author "Dave"
```


Next we specify when we want this job to run:


```
start on runlevel [2345]
```

Looks bit unintuitive, but all this means is "start after all the general
linux configuration is done".


Literally interpretted as "start this task when the runlevel is equal to
2, 3, 4, or 5".


`runlevel` is the value that represents the current system configuration.


Fun fact: you can type `runlevel` in your terminal and get the current value.


Next, we'll write the actual thing our testjob is going to execute:


We're going to append the date to a log file when this job runs.


```
exec echo Test Job ran at  `date` >> /var/log/testjob.log
```


Save and close the file.


Before we test this script, we can debug/check it wih:


```
init-checkconf /etc/init/testjob.conf
```


Now we can test our Upstart Job!


`sudo service testjob start`


and check our log file:


`cat /var/log/testjob.log`


### Useful Commands

`stop on shutdown`

### Using 'stanzas' to define pre and post behavior

```
description "Test"
author      "Your Name"

start on filesystem or runlevel [2345]
stop on shutdown

script

    exec echo Test Job ran at  `date` >> /var/log/testjob.log

end script

pre-start script

    echo "`date` Starting" >> /var/log/testjob.log

end script

pre-stop script

    echo "`date` Ending " >> /var/log/testjob.log

end script
```
