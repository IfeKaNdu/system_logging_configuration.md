### Gestion des processus en cours
Un processus est une instance d'un programme en cours d'exécution et peut également être une instance en cours d'exécution d'une commande. À partir du shell, vous pouvez lancer des processus, puis les suspendre, les arrêter ou les tuer. Un ID de processus unique pour un système actuel est utilisé pour l'identification d'un processus sur un système.
D'autres attributs sont associés à un processus avec le numéro d'identification du processus. Les comptes d'utilisateur et de groupe associés à un processus particulier déterminent les ressources système auxquelles le processus peut accéder.
**NB** Parfois, des processus en fuite peuvent nuire aux performances de votre système. Pour en savoir plus sur les processus, accédez au système de fichiers `` proc`` virtuel, où les commandes qui affichent des informations sur les processus en cours génèrent principalement des données brutes.
### La commande ps
La commande `ps` est la commande la plus ancienne et la plus courante pour répertorier les processus en cours d'exécution sur un système.Pour une écoute et un changement de processus plus orientés vers l'écran, la commande top peut être utilisée.
L'utilitaire ps peut être utilisé pour vérifier les processus en cours d'exécution, les ressources utilisées par les processus et l'utilisateur qui les exécute; par conséquent, ps fournit un instantané des processus en cours.
```bash
$ ps -u
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
egbuniwe 13024  0.0  0.2 116984  3716 pts/0    Ss   16:24   0:00 bash
egbuniwe 23850  0.0  0.1 155360  1876 pts/0    R+   19:17   0:00 ps u
```
L'option u concerne l'affichage des informations relatives à l'utilisateur du processus. Les colonnes Ss et R + situées sous la colonne STAT représentent l'état du processus en cours de mise en veille et en cours d'exécution. VSZ (taille du jeu virtuel) indique la taille du processus de l'image. (en kilo-octets), tandis que le RSS (taille du groupe résident) indique la taille du programme en mémoire. Le VSZ est la quantité de mémoire affectée à un processus, tandis que le RSS est la quantité réelle de mémoire utilisée par le processus. TIME représente le temps système cumulé (CPU) utilisé.
**NB** Le RSS représente la mémoire qui ne peut pas être échangée.

Pour que l'utilisateur actuel puisse parcourir tous les processus en cours d'exécution sur le système, exécutez la commande suivante:
```bash
$ ps ux | less 
```
Pour les processus en cours pour tous les utilisateurs exécutés
```bash
$ ps aux | less
```
La personnalisation de la commande ps afin d'afficher les colonnes d'informations sélectionnées et de trier les informations en fonction des colonnes peut être déclenchée. L'option `-e` de la commande ps répertorie tous les processus en cours d'exécution (-e) et le` -o` Cette option utilise les mots-clés pour indiquer les colonnes que vous souhaitez répertorier avec ps. Par défaut, la sortie est triée par numéro d'ID de processus, bien qu'avec l'option `--sort`, elle puisse être personnalisée pour trier en fonction d'une colonne spécifique.
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
La `sort = option`, permet de trier par une colonne spécifique .Pour voir les processus qui utilisent le plus de mémoire, trier par le champ rss
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
Pour voir les informations de processus d'un processus particulier, une variante de la commande ps peut être utilisée:
```bash
$ ps -eo pid,user,uid,group,gid,vsz,rss,comm --sort=-rss | grep bash
13024 egbuniwe  1000 egbuniwe  1000 116984  3716 bash
24188 egbuniwe  1000 egbuniwe  1000 116884  3612 bash
```
Un contexte de sécurité de processus est une méthode SELinux utilisée pour appliquer des règles de stratégie aux processus accédant à des objets (tels que des fichiers). Un contexte de sécurité pour un processus est constitué de quatre attributs similaires à ceux d'un utilisateur et d'un fichier. Il s'agit de l'utilisateur, du rôle, du type et du niveau.
Pour voir le contexte de sécurité d'un processus, vous devez utiliser l'option -Z sur la commande ps, par exemple:
```bash
$ ps -eZ | grep bash
unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 13024 pts/0 00:00:00 bash
unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 24188 pts/1 00:00:00 bash
```
La liste de contexte de processus affiche les éléments suivants:
1.user: le processus est mappé sur l'utilisateur unconfined_u de SELinux.
2.role: le processus est exécuté en tant que rôle unconfined_r.
3.type: le processus est en cours d'exécution dans le domaine unconfined_t.
4.le niveau—
+ sensibilité: le processus n'a que le niveau s0.
+ categories: le processus a accès à c0.c1023, qui correspond à toutes les catégories (c0
jusqu'à c1023).
### La commande top
La commande top, contrairement à la commande ps, affiche une mise à jour répétitive de l'ensemble des processus actifs ou de ceux sélectionnés. La durée d'utilisation du processeur est le paramètre par défaut utilisé dans le tri des processus pour l'utilitaire top, bien que le tri par d'autres colonnes puisse également être effectué. être utilisé pour tuer ou renice processus en conséquence.
La liste suivante comprend les actions que vous pouvez effectuer avec top pour afficher des informations dans différents
façons et modifier les processus en cours:
1. Appuyez sur h pour afficher les options d’aide, puis appuyez sur n’importe quelle touche pour revenir à l’affichage supérieur.
2. Appuyez sur M pour trier par utilisation de la mémoire au lieu de CPU, puis appuyez sur P pour revenir à
tri par CPU.
3.Appuyez sur le chiffre 1 pour afficher l’utilisation du processeur de tous vos processeurs, si vous en avez plusieurs.
4. Appuyez sur R pour effectuer un tri inverse de votre sortie
5. Appuyez sur u et entrez un nom d'utilisateur pour afficher les processus uniquement pour un utilisateur particulier.
Pour tuer ou renommer un processus dont le top est en cours d'exécution, procédez comme suit.
+ **Tuer un processus**: Identifiez d'abord l'ID de processus du processus que vous voulez tuer et appuyez sur k.Tapez l'ID de processus du programme à tuer, puis tapez 15 pour mettre fin proprement ou 9 pour simplement arrêter le processus immédiatement.
+ **Renicing d'un processus**: avec l'identifiant de processus du programme que vous souhaitez renommer, appuyez sur r.Lorsque le PID à renoncer: le message apparaît, tapez l'identifiant du processus que vous souhaitez renier.Lorsque vous êtes invité à Renice PID à valeur: entrez un nombre compris entre -19 et 20.
**NB** Plus la valeur de courtoisie est basse, plus le processus dispose d'un accès aux processeurs.
##### Vérification des problèmes de mémoire avec top
Si vous rencontrez soudainement un problème de performances en exécutant de nombreuses applications, il est important d'utiliser top ou ps pour vérifier la consommation de mémoire du système.
Afin de vérifier la consommation de mémoire, la commande top peut être utilisée avec M, c’est-à-dire en tapant `top` puis un` M` majuscule:
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
Le M en majuscule indique à top de trier par utilisation de la mémoire au lieu du temps de consommation du processeur par défaut, en utilisant la colonne RES qui correspond à la quantité de mémoire non mappable réellement utilisée..La colonne VIRT indique uniquement la quantité de mémoire qui a été utilisée. La colonne SHR indique la mémoire qui pourrait éventuellement être partagée par d'autres applications (telles que les bibliothèques). La colonne% MEM indique le pourcentage de mémoire totale utilisée par chaque application.

### La commande journalctl
Les systèmes Linux qui utilisent la fonction systemd pour collecter des messages provenant du noyau et des services exécutés sur le système disposent d’une méthode de collecte et d’affichage des messages, appelée journal systemd. Afin de visualiser le journal systemd, la commande `journalctl` peut être utilisée pour afficher les messages de journal de différentes manières.
Le processus de démarrage, le noyau et tous les services gérés par systemd dirigent leurs messages d'état et d'erreur vers le journal systemd.
Pour afficher les messages associés à une instance de démarrage particulière, utilisez l'option -b avec l'une des instances de démarrage. Pour répertorier les ID de démarrage à chaque démarrage du système, utilisez les options `-list-boots`.
Pour vérifier uniquement les messages de démarrage, vous pouvez répertorier les ID de démarrage pour tous les démarrages du système, puis démarrer l'instance de démarrage particulière qui vous intéresse. Pour afficher les ID de démarrage, puis les messages de démarrage pour un ID de démarrage sélectionné, utilisez les commandes suivantes:

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
Pour faire défiler tous les messages du noyau au fur et à mesure qu'ils arrivent, ajoutez les options -f et -k à la commande journalctl.
```bash
# journalctl -k -f
-- Logs begin at Mon 2019-08-26 22:41:15 GMT. --
Aug 27 19:58:49 localhost.localdomain kernel: wls1: send auth to 4c:fa:ca:db:43:f0 (try 1/3)
Aug 27 19:58:49 localhost.localdomain kernel: wls1: authenticated
Aug 27 19:58:49 localhost.localdomain kernel: wls1: associate with 4c:fa:ca:db:43:f0 (try 1/3)
Aug 27 19:58:49 localhost.localdomain kernel: wls1: RX AssocResp from 4c:fa:ca:db:43:f0 (capab=0x1401 status=0 aid=2)
---
```
L'option journalctl de _SYSTEMD_UNIT consiste à afficher des messages pour des services spécifiques, par exemple le service vsftpd ou tout autre fichier d'unité systemd (tel que d'autres services ou montages). Vous pouvez également suivre les messages lorsqu'ils arrivent en utilisant l'option -f. Pour afficher tous les messages, utilisez l'option -a.
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
La commande Journalctl affiche localement les messages de journal collectés dans le journal systemd. Elle affiche les messages du journal de journal directement à partir du journal systemd, au lieu de les afficher à partir de fichiers du répertoire /var/log.

Pour afficher les messages associés à un service particulier, utilisez l’option -u suivie du nom du service.
```bash
# journalctl -u named.service
# journalctl -u NetworkManager.service
```
Pour plus d'informations sur les nombreuses options disponibles avec la commande journacll et le journal systemd, consultez la section `man journalctl` et `man systemd-journald.service`

### La fonction rsyslogd
Pour les systèmes Linux qui n'utilisent pas l'utilitaire systemd, l'utilitaire principal pour la consignation des messages d'erreur et de débogage est le démon rsyslogd.
La fonction rsyslogd rassemble les messages de journal et les dirige vers des fichiers journaux ou des hôtes de journal distants. Les informations contenues dans le fichier /etc/rsyslog.conf déterminent la manière dont les messages de journalisation sont collectés par le démon rsyslogd et les fichiers dans lesquels ils sont stockés. Les messages qui leur sont généralement envoyés se trouvent dans le répertoire /var/log. Bien que, pour plus de sécurité, les messages puissent également être dirigés vers des hôtes de journalisation distants ou un serveur centralisé, fournissant ainsi un emplacement unique pour afficher et gérer la journalisation d'un groupe de systèmes.
Certains fichiers journaux courants du répertoire /var/log incluent:

| Nom du journal système | Nom du fichier | La description
| ------------------------------ | ------------- | ----------------------- |
| Mauvais identifiants de connexion | btmp | Enregistre les mauvaises tentatives de connexion |
| Journal de démarrage | boot.log | Contient des messages indiquant quels services du système ont démarré et se sont arrêtés avec succès et quels (le cas échéant) le démarrage ou l'arrêt du programme ont échoué.
| Journal du noyau | dmsg | Enregistre les messages imprimés par le noyau au démarrage du système.
| Journal du gestionnaire d'affichage GNOME | /var/log/gdm/:0.log | Contient les messages liés à l'écran de connexion (gestionnaire d'affichage GNOME).
| LastLog | lastlog | Enregistre la dernière fois qu'un compte s'est connecté au système.
| Connexion / déconnexion | wtmp | contient un historique des connexions et des déconnexions sur le système.
| Journal de sécurité | sécurisé | Enregistre la date, l'heure et la durée des tentatives de connexion et des sessions.
| Journal système | messages | Fournit un fichier journal à usage général dans lequel de nombreux programmes enregistrent des messages.
| Journal YUM | yum.log | Affiche les messages liés aux packages logiciels RPM.

Le service rsyslog permet au système local de servir de serveur de journalisation système en rassemblant les messages de journalisation délivrés à partir de divers composants du système. Il peut également agir en tant que serveur de journalisation distant en regroupant les messages de journalisation envoyés par un autre serveur de journalisation. Les messages de journalisation sont rassemblés dans le journal systemd, qui peut être récupéré et redirigé par le service rsyslog ou affiché localement par la commande journalctl.

En termes simples, avec le service rsyslog (démon rsyslogd), il est possible de rassembler des informations critiques et des conditions d'erreur dans des fichiers journaux concernant de nombreux services différents.

Il existe différents progiciels utilisables avec rsyslog dans la gestion des messages de journal. Par exemple, `logrotate` sauvegarde les fichiers de journal dans des archives compressées lorsque les journaux atteignent une certaine taille ou dépassent une durée définie depuis la sauvegarde précédente. tandis que la fonction `logwatch` analyse les fichiers journaux chaque nuit et envoie les informations critiques recueillies à partir de ces fichiers numérisés à un courrier électronique de votre choix.
Contrairement à syslog, Rsyslog permet d’ajouter des modules pour gérer et diriger plus précisément les messages de journalisation. La plupart des messages de journalisation sont dirigés vers des fichiers du répertoire /var/ log.Bien que les messages puissent toujours être redirigés vers un périphérique un hôte de journal distant (tel que @ loghost.example.com). Le signe at (@) indique que le nom qui suit est le nom de l'hôte-journal.

##### Regarder les journaux avec logwatch
Installez la fonction logwatch à l'aide de la commande suivante:
```bash
# yum install logwatch
```
En réalité, logwatch collecte des messages problématiques une fois par nuit et les envoie par courrier électronique au courrier électronique prévu de l'administrateur.
Le service logwatch s'exécute à partir d'un travail cron (01logwatch) placé dans /etc/cron.daily. Le fichier /etc/logwatch/conf/logwatch.conf contient les paramètres locaux. Les options par défaut utilisées pour rassembler les messages de journal sont définies dans le fichier /usr/share/logwatch/default.conf/logwatch.conf.

Modifiez le paramètre MailTo dans le fichier /etc/logwatch/conf/logwatch.conf:
```bash
MailTo = egbuniwe@odii.cic
```
Le fichier `/usr/share/logwatch/default.conf/logwatch.conf` contient la description des autres paramètres à modifier (tels que le niveau de détail ou la plage de temps de chaque rapport). La description mentionnée dans le fichier /usr/share/logwatch * est ajoutée et implémentée dans le fichier `/etc/logwatch/conf/logwatch.conf`

Les messages sont envoyés chaque nuit à la boîte aux lettres de l'utilisateur racine et, avec la commande `mail`, l'utilisateur root peut afficher la boîte aux lettres de l'utilisateur racine en tant que root.
```bash
# mail
```

Le type d'informations que l'on voit dans le courrier inclut les erreurs du noyau, les packages installés, les échecs d'authentification et les services défaillants. L'espace disque est également signalé.

                              Références

+ Negus, N.  2015: Linux Bible(9th edition). Indianapolis, Indiana: John Wiley & Sons Inc., pp. 138-140,187-189,200,317, 322-323,334-339,583-584.




