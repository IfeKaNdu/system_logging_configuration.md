### Managing running processes
A process is an instance of a running program and can also be a running instance of a commmand. From the shell, you can launch processes then pause,stop or kill them.Process ID which is unique for a current system is used in the identification of a process on a system.
Other attributes are associated with a process along with the process ID number. The user and group accounts associated with a particular process determine the system resources that can be accessed by the process.
**NB** Sometimes, runaway processes may be killing your system's performance .To find out about the processes, access the virtual `/proc` file system which is where the commands that display information about running processes get mostly there raw data from. 
### The ps command
The `ps` command is the oldest and most common command for listing processes currently running on a system.For a more screen-oriented listening and changing of a process, the top command can be used.
The ps utility can be used to check running processes, the resources used by the processes and the user running them hence the ps reports a snapshot of the current processes.
```bash
$ ps -u
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
egbuniwe 13024  0.0  0.2 116984  3716 pts/0    Ss   16:24   0:00 bash
egbuniwe 23850  0.0  0.1 155360  1876 pts/0    R+   19:17   0:00 ps u
```
The u option is for the process user information to be shown.The Ss and the R+ under the STAT column represents the state of the process which is currently sleeping and running processes respectively.VSZ (virtual set size) shows the size of the image process (in kilobytes), while the RSS (resident set size) shows the size of the program in memory.The VSZ is the amount of memory assigned to a process while the RSS is the actual amount of memory being used by the process. TIME represents the cumulative system(CPU) time used.
**NB** The RSS represent the memory that cannot be swapped.

For the current user to page through all the processes running on the system for the current user , run the command:
```bash
$ ps ux | less 
```
For the processes running for all the users run
```bash
$ ps aux | less
```
The customization of the ps command inorder to display selected columns of information and to sort the information according to the columns can be triggered .The `-e` option to the ps command lists every running process(-e) and the `-o` option uses the keywords to indicate the columns you want to list with ps. By default ,the output is sorted by process ID number although with the option `--sort`, it can be customized to sort based on a specific column.
```bash
$ ps -eo pid,user,uid,group,gid,vsz,rss,comm | less
PID   USER       UID GROUP      GID    VSZ   RSS COMMAND
1     root         0 root         0 193840  6120 systemd
2     root         0 root         0      0     0 kthreadd
3     root         0 root         0      0     0 ksoftirqd/0
5     root         0 root         0      0     0 kworker/0:0H
7     root         0 root         0      0     0 migration/0
---
```
The `sort=option`, enables one to sort by a specific column .To see the processes which are using the most memory, sort by the rss field
```bash
$ ps -eo pid,user,uid,group,gid,vsz,rss,comm --sort=-comm | less
---
PID USER       UID GROUP      GID    VSZ   RSS COMMAND
12031 egbuniwe  1000 egbuniwe  1000 3439004 143360 gnome-shell
21446 egbuniwe  1000 egbuniwe  1000 1694016 143204 tracker-extract
21850 egbuniwe  1000 egbuniwe  1000 1106740 107060 evince
12383 egbuniwe  1000 egbuniwe  1000 629420 87076 tracker-store
 2226 mysql       27 mysql       27 980728 79640 mysqld
 2241 root         0 root         0 407012 70616 X
---
```
To see the process information for a particular process, a variant of the  ps command can be used :
```bash
$ ps -eo pid,user,uid,group,gid,vsz,rss,comm --sort=-rss | grep bash
13024 egbuniwe  1000 egbuniwe  1000 116984  3716 bash
24188 egbuniwe  1000 egbuniwe  1000 116884  3612 bash
```
A process security context is a SELinux method used to enforce policy rules for processes accessing objects (such as files). A security context for a process consists of four attributes similar to those of a user and a file.They are user,role,type and level. 
To see the security context for a process,you need to use the -Z option on the ps command for example:
```bash
$ ps -eZ | grep bash
unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 13024 pts/0 00:00:00 bash
unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 24188 pts/1 00:00:00 bash
```
The process context list shows the following:
1.user—Process is mapped to the SELinux unconfined_u user.
2.role—Process is running as the unconfined_r role.
3.type—Process is running in the unconfined_t domain.
4.level—
+ sensitivity—Process has only level s0.
+ categories—Process has access to c0.c1023, which is all categories (c0
	through to c1023).
### The top command
The top command unlike the ps command displays a repetitive update of the entire or selected active processes.CPU consumption time is the default parameter used in the sorting of processes for the top utility although sorting by other columns can be done.The top command can also be used to kill or renice processes accordingly.
The following list includes actions you can do with top to display information in different
ways and modify running processes:
1.Press h to see help options, and then press any key to return to the top display.
2.Press M to sort by memory usage instead of CPU, and then press P to return to
sorting by CPU.
3.Press the number 1 to toggle showing CPU usage of all your CPUs, if you have more than one CPU on your system.
4.Press R to reverse sort your output.
5.Press u and enter a username to display processes only for a particular user. 
To kill or renice a process with the top running, the following should be done.
+**Killing a process**: First identify the process ID of the process you want to kill and press k.Type the process ID of the program to kill then type 15 to terminate cleanly or 9 to just kill the process immediately.
+**Renicing a process**: With the process ID of the program you want to renice noted, press r.When the PID to renice:message appears,type the process ID of the process you wish to renice.When prompted to Renice PID to value:type in a number from -19 to 20.
**NB** The lower the nice value ,the morer access to the CPUs the process has. 

##### Checking for memory problems with top
If suddenly you start having a performance problem in the process of running lots of applications, it's important to use top or ps to check for the memory consumption on the system.
For the purpose of checking for memory consumption, top command can be used with M that is by typing `top` and then a capital `M` :
```bash
$ top
top - 05:47:03 up 13:29,  3 users,  load average: 0.95, 1.34, 0.92
Tasks: 215 total,   1 running, 213 sleeping,   1 stopped,   0 zombie
%Cpu(s):  3.7 us,  2.0 sy,  0.0 ni, 93.3 id,  1.0 wa,  0.0 hi,  0.0 si,  0.0 st
KiB Mem :  1809148 total,   278536 free,   678284 used,   852328 buff/cache
KiB Swap:  2096124 total,  1449568 free,   646556 used.   738640 avail Mem 

  PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND                              
13176 egbuniwe  20   0 1568208 144032  21580 S   0.0  8.0   1:46.19 nautilus                             
12031 egbuniwe  20   0 3457292  92716  27420 S   5.0  5.1  11:32.59 gnome-shell                          
21850 egbuniwe  20   0 1150160  89768  19832 S   0.0  5.0   4:29.67 evince                               
 1629 egbuniwe  20   0 1781396  65832  30904 S   0.0  3.6   0:06.03 totem                                
 2241 root      20   0  411564  49532  32724 S   3.0  2.7  12:17.15 X                                    
16049 egbuniwe  20   0  841336  27712   8680 S   0.0  1.5   6:26.04 gedit                                
21446 egbuniwe  39  19 1694020  21740   2252 S   0.0  1.2  11:51.80 tracker-extract                      
13002 egbuniwe  20   0  751288  16216   6812 S   1.3  0.9   1:00.41 gnome-terminal- 
---
```
The capital M tells top to sort by memory usage instead of the default CPU consumption time and it does that using the RES column which is the amount of nonswappable memory that is actually being used..The VIRT column shows only the amount of memory that has been promised to the application.The SHR column shows memory that could potentially be shared by other applications(such as libraries).The column %MEM shows the percentage of total memory consumed by each application.

### The journalctl command 
Linux systems that use the systemd facility to gather messages coming from the kernel and services running on the system have a method of gathering and displaying messages called the systemd journal. Inorder to view the systemd journal, the `journalctl` command can be used to display log messages in various ways.
The boot process, the kernel and all systemd-managed services direct their status and error messages to the systemd journal.
To view messages associated with a particular boot instance, use the -b option with one of the boot instance while to list the boot IDs for each time the system was booted use the `-list-boots` options.
To check only boot messages, you can list the boot IDs for all system boots and then boot the particular boot instance that interests you.To display boot IDs and then boot messages for a selected boot ID,use the following commands:

```bash
# journalctl --list-boots 
0 47687fa591ed4609bab5a6224443d5d1 Mon 2019-08-26 21:17:57 GMT—Mon 2019-08-26 21:28:25 GMT
```
```bash
# journalctl -b 47687fa591ed4609bab5a6224443d5d1
-- Logs begin at Mon 2019-08-26 21:17:57 GMT, end at Mon 2019-08-26 21:31:07 GMT. --
Aug 26 21:17:57 localhost.localdomain systemd-journal[94]: Runtime journal is using 8.0M (max allowe
Aug 26 21:17:57 localhost.localdomain kernel: microcode: microcode updated early to revision 0xa0b, 
Aug 26 21:17:57 localhost.localdomain kernel: Initializing cgroup subsys cpuset
Aug 26 21:17:57 localhost.localdomain kernel: Initializing cgroup subsys cpu
Aug 26 21:17:57 localhost.localdomain kernel: Initializing cgroup subsys cpuacct
Aug 26 21:17:57 localhost.localdomain kernel: Linux version 3.10.0-957.27.2.el7.x86_64 (mockbuild@kb
Aug 26 21:17:57 localhost.localdomain kernel: Command line: BOOT_IMAGE=/vmlinuz-3.10.0-957.27.2.el7.
Aug 26 21:17:57 localhost.localdomain kernel: Disabled fast string operations
Aug 26 21:17:57 localhost.localdomain kernel: e820: BIOS-provided physical RAM map:
Aug 26 21:17:57 localhost.localdomain kernel: BIOS-e820: [mem 0x0000000000000000-0x000000000009fbff]
---
```
To page through all the kernel messages as they come in, add the -f and the -k options to the journalctl command.
```bash
# journalctl -k -f
-- Logs begin at Mon 2019-08-26 22:41:15 GMT. --
Aug 27 19:58:49 localhost.localdomain kernel: wls1: send auth to 4c:fa:ca:db:43:f0 (try 1/3)
Aug 27 19:58:49 localhost.localdomain kernel: wls1: authenticated
Aug 27 19:58:49 localhost.localdomain kernel: wls1: associate with 4c:fa:ca:db:43:f0 (try 1/3)
Aug 27 19:58:49 localhost.localdomain kernel: wls1: RX AssocResp from 4c:fa:ca:db:43:f0 (capab=0x1401 status=0 aid=2)
---
```
The journalctl option of _SYSTEMD_UNIT is to show messages for specific services for instance the vsftpd service or any other systemd unit file (such as other services or mounts). You can also follow messages as they come in by using the -f option while to show all the messages ,use the -a option.
```bash
# journalctl _SYSTEMD_UNIT=sshd.service
-- Logs begin at Mon 2019-08-26 21:17:57 GMT, end at Mon 2019-08-26 21:46:11 GMT. --
Aug 26 21:19:42 localhost.localdomain sshd[1690]: Server listening on 0.0.0.0 port 22.
Aug 26 21:19:42 localhost.localdomain sshd[1690]: Server listening on :: port 22.
```
```bash
# journalctl -fa | head
Logs begin at Mon 2019-08-26 21:17:57 GMT. --
Aug 26 22:25:24 localhost.localdomain tracker-extract[20132]: gstpad.c:5079:store_sticky_event:<avdec_wmav2-13:src> Sticky event misordering, got 'segment' before 'caps'
Aug 26 22:25:24 localhost.localdomain tracker-extract[20132]: gstpad.c:5079:store_sticky_event:<'':decodepad60> Sticky event misordering, got 'segment' before 'caps'
Aug 26 22:25:24 localhost.localdomain tracker-extract[20132]: gstpad.c:5079:store_sticky_event:<src_0:proxypad185> Sticky event misordering, got 'segment' before 'caps'
Aug 26 22:25:24 localhost.localdomain tracker-extract[20132]: gstpad.c:5079:store_sticky_event:<avdec_wmav2-14:src> Sticky event misordering, got 'segment' before 'caps'
---
```
Journalctl command displays locally log messages that are gathered in the systemd journal.It displays the journal log messages directly from the systemd journal,instead of viewing them from files in the /var/log directory.

To view messages associated with a particular service ,use the -u option followed by the service name.
```bash
# journalctl -u named.service
# journalctl -u NetworkManager.service
```
For more information on the many options that are available with the journacll command and the systemd journal ,visit the `man journalctl` and `man systemd-journald.service`

### The rsyslogd facility 
For Linux systems that don't use the systemd facility,the main utility for logging error and debugging messages is the rsyslogd daemon.
The rsyslogd facility gather log messages and direct them to log files or remote log hosts. The information in the /etc/rsyslog.conf file determines how logging messages are gathered by the rsyslogd daemon and what files they are stored in. Files that are usually in the /var/log directory have messages directed to them. Although for additional security, messages can also be directed to remote log hosts or centralized server, hence providing a single location to view and manage logging for a group of systems.
Some common log files in the /var/log Directory include:

|  System Log Name       	|Filename      		| Description
| ------------------------------| -------------		| -----------------------|
| Bad Logins Log         	| btmp         		| Logs bad login attempts|
| Boot Log               	| boot.log     		| Contains messages indicating                                                   which system services have                                                     started and shut down                                                         successfully and which(if any)                                                 have failed to start or stop.
| Kernel Log             	| dmsg         		| Records messages printed by the kernel when the system boots.
|GNOME Display Manager Log  	| /var/log/gdm/:0.log	| Holds messages related to the login screen (GNOME display manager).
|LastLog			|lastlog		| Records the last time an account logs in to the system.
|Login/out Log 			|wtmp			| contains a history of logins and logouts on the system.
|Security Log  			|secure			| Records the date ,time, and duration of login attemps and sessions.
|System Log			|messages 		| Provides a general-purpose log file where many programs record messages.
|YUM Log			|yum.log		| Shows messages related to RPM software packages.

The rsyslog service allows the local system to serve as a System Logging Server by gathering log messages delivered from a variety of components on the system.It can also act as a remote logging server by gathering logging messages sent from other logging server.Although presently, log messages are gathered in the systemd journal , which can be picked up and redirected by the rsyslog service of displayed locally by the journalctl command.

simply put, with the rsyslog service (rsyslogd daemon) one can gather critical information and error conditions into log files about many different services.

There are different software packages which are available to work with rsyslog in the management of log messages.The `logrotate` for instance backs up log files into compressed archives when the logs reach a certain size or pass a set amount of time since the previous backup while the `logwatch` feature scans the log files each night and sends critical information gathered from those scanned files to an e-mail of your choice.

Rsyslog unlike syslog allows one to add modules to more specifically manage and direct log messages.Most log messages are directed to files in the /var/log directory.Although one can still direct messages to a device ( such as /dev/console) or a remote log host (such as @loghost.example.com). The at sign (@) indicates that the name that follows is the name of the loghost.

##### Watching logs with logwatch
Install the logwatch facility with the following command:
```bash
# yum install logwatch
```
What actually logwatch does is gather messages once each night that look problematic and send it via e-mail message to the administrator's intended email.
The logwatch service runs from a cron job(01logwatch) placed in /etc/cron.daily. The /etc/logwatch/conf/logwatch.conf file holds local setting.The default options used to gather log messages are set in the /usr/share/logwatch/default.conf/logwatch.conf file.

Change the MailTo setting in the /etc/logwatch/conf/logwatch.conf file:
```bash
MailTo = egbuniwe@odii.cic
```
The `/usr/share/logwatch/default.conf/logwatch.conf` file contains the description of other settings to change (such as detail level or the time range for each report). The description mentioned in the /usr/share/logwatch* file are added and implemented to the `/etc/logwatch/conf/logwatch.conf`

Mail messages are sent each night to the root user's mailbox and with the `mail` command, the root user can view the root user's mailbox as root
```bash
# mail
```

The kind of information one sees from the mail includes kernel errors, installed packages ,authentication failures,and malfunctioning services.Also among the thing reported is the disk space usage.

								References 
							
+ Negus, N.  2015: Linux Bible(9th edition). Indianapolis, Indiana: John Wiley & Sons Inc., pp. 138-140,187-189,200,317, 322-323,334-339,583-584.




