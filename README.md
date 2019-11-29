# SystemD

Les processus du noyau sont listés entre parenthèses dans le résultat de ps -ef. Par exemple [kthreadd](PID 2).

vérifier que la version de systemdest> 241

systemctl --version
🌞s’assurer que systemdest PID1

Ajouts adduser admin pour créer un utilisateur & pour switch de user su admin

🌞 vérifier tous les autres processus système (** PAS les processus kernel)

  		- root           1       0  0 04:59 ?        00:00:02  usr/lib/systemd/systemd --switched-root --system --deserialize 30
  		- root         519       1  usr/lib/systemd/systemd-journald
  		- root         538       1  0 04:59 ?        00:00:00 /usr/lib/systemd/systemd-udevd
  		- root         853       1  0 04:59 ?        00:00:00 /usr/lib/systemd/systemd-logind
  		- root       26008       1  0 05:05 ?        00:00:00 /usr/lib/systemd/systemd --user
  		- root       27095   27032  0 05:57 pts/0    00:00:00 grep --color=auto systemd
décrire brièvement au moins 5 autres processus système
2. Gestion du temps
La gestion du temps est désormais gérée avec systemdavec la commande timedatectl.

timedatectl sans argument

-	   [admin@localhost /]# timedatectl
       Local time: ven. 2019-11-29 06:03:40 PST
   Universal time: ven. 2019-11-29 14:03:40 UTC
         RTC time: ven. 2019-11-29 14:03:40
        Time zone: America/Los_Angeles (PST, -0800)
System clock synchronized: yes
NTP service: active
RTC in local TZ: no

🌞 déterminer la différence entre heure locale, heure universelle et heure RTC

Heure local : L’heure de notre UTC ou on est situé

Heure Universelle : L’heure mondiale

Heure RTC : Real time clock heure du PC
C 'est le même principe que le système équipant les cartes mères depuis l’aube de l’informatique, lui permettant de garder en mémoire l’heure et la date, même lorsque celui-ci est éteint ou coupé de toutes sources de courant.

fuseaux horaires

timedatectl list-timezoneset `timedatectl set-timezone
[admin@localhost /]$ timedatectl set-Europe/Minsk
Unknown operation set-Europe/Minsk.
[admin@localhost /]$ timedatectl set-Europe/Minsk
Unknown operation set-Europe/Minsk.
[admin@localhost /]$ timedatectl Europe/Minsk
Unknown operation Europe/Minsk.
[admin@localhost /]$ timedatectl set-Europe/Minsk
Unknown operation set-Europe/Minsk.
[admin@localhost /]$ timedatectl set-timezone Europe/Minsk
==== AUTHENTICATING FOR org.freedesktop.timedate1.set-timezone ====
Authentication is required to set the system timezone.
Authenticating as: root
Password:
==== AUTHENTICATION COMPLETE ====
`pour définir les fuseaux horaires
- 🌞 changer de fuseau horaire pour un autre fuseau horaire européen

on peut activer ou désactiver l’utilisation de la synthèse NTP avec timedatectl set-ntp <BOOLEAN>
[admin@localhost /]$ timedatectl set-ntp 0 / 1
==== AUTHENTICATING FOR org.freedesktop.timedate1.set-ntp ====
Authentication is required to control whether network time synchronization shall be enabled.
Authenticating as: root
Password:
==== AUTHENTICATION COMPLETE ====

🌞 désactiver le service lié à la synchronisation du temps avec cette commande, et vérifier qu’il a été coupé
[admin@localhost /]$ sudo tcpdump -n “broadcast or multicast” | grep NTP
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on ens33, link-type EN10MB (Ethernet), capture size 262144 bytes
3. Gestion de noms
La gestion de nom est également gérée par systemdavec hostnamectl.
En réalité, hostnamectlpeut récupérer plus d’informations que le nom de domaine: hostnamectlsans arguments pour les voir.

changer les noms de la machine avec hostnamectl --set-hostname
il est possible de changer trois noms avec --pretty, --staticet--transient
🌞expliquer la différence entre les trois types de noms.
--pretty : hostname is a free-form UTF8 host name for presentation to the user. - --static : traditional hostname stored in the /etc/hostname - --transient : dynamic host name maintained by the kernel
Lequel est-il utilisé pour les machines de prod?
On utilise static

on peut aussi modifier certains autres noms comme le type de déploiement: hostnamectl set-deployment <NAME>
This order is tres utile for a Inventorier un parc de machines, allumer les informations système, pouvoir aider à sa classification sont actuellement.

4. Gestion du réseau (et résolution de noms)
Pour gérer la pile de réseau, deux outils sont livrés avec systemd:

NetworkManager
souvent activé par défaut
aux Changements réagit du dynamiquement réseau (mise à jour de /etc/resolv.confen fonction des réseaux par exemple connectés)
bureau idéal pour un déploiement
expose une API dbus
systemd-networkd
permet une grande flexibilité de configuration
configuration de plusieurs interfaces simultanément
fonctionnalités avancées
utiliser une norme de syntaxe systemd
complètement intégré à systemd(gestion, journaux, etc.)
nuage idéal en déploiement
Gestionnaire de réseau
NetworkManager est l’utilitaire qui doit maintenant démarrer avec OS GNU / Linux équipé de systemd. Il est utilisé pour configurer les interfaces réseau d’une machine.

il pilote
il conserver et pilote le fichier /etc/resolv.confpar exemple
il existe des outils pour interagir avec les interfaces qu’il gère
à la suite Similaire iproute2( ip a, ip route show, ip neigh show, ip net add, etc.)
comme l’outil en ligne de commande nmcli
Utilisation basique en ligne de commande:

lister les interfaces et les informations liées
nmcli con show
nmcli con show <INTERFACE>
modifier la configuration réseau d’une interface
éditer le fichier de configuration d’une interface /etc/sysconfig/network-scripts/ifcfg-<INTERFACE_NAME>
rechargeur NetworkManager: sudo systemctl reload NetworkManager(relire les fichiers de configuration)
redémarrer l’interface sudo nmcli con up <INTERFACE_NAME>
🌞 afficher les informations DHCP récupérées par NetworkManager (sur une interface en DHCP)
root@localhost NetworkManager]# systemctl stop NetworkManager
[root@localhost NetworkManager]# systemctl disable NetworkManager
Removed /etc/systemd/system/multi-user.target.wants/NetworkManager.service.
Removed /etc/systemd/system/dbus-org.freedesktop.nm-dispatcher.service.
Removed /etc/systemd/system/network-online.target.wants/NetworkManager-wait-online.service.
Sinon une bonne interface curses des familles: avec la commande nmtui

Avec le gestionnaire de paquet dnf, vous can UTILISER dnf provides <COMMANDE>verser le nom du Pressothérapie à installer Paquet verser Avoir une commande Donnée

vec le gestionnaire de paquet dnf, vous can UTILISER dnf provides <COMMANDE>verser le nom du Pressothérapie à installer Paquet verser Avoir une commande Donnée

systemd-networkd
Il also possible la configuration des Qué interfaces et de la réseau de résolution NOMs enitèrement Geree par Soit systemd, à l’aide du démon systemd-networkd.

Dans le d’utilisation de CAS systemd-networkd, il EST de préférable NetworkManager Désactiver, d’EVITER les AFIN conflicts et l’ajout de SuperFlue Complexité:

🌞 stopper et désactiver le démarrage de NetworkManager
🌞 démarrer et activer le démarrage de systemd-networkd
Il est alors possible de configurer les interfaces réseau /etc/systemd/networkavec les fichiers .network.
C’est le rôle systemd-networkdde la surveillance de ces fichiers et de sa réponse au changement d’état des cartes réseaux (comme un redémarrage).

La structure des fichiers /etc/systemd/network/*.networkest la suivante:

[Match]
Key=value

[Network]
Key=Value
La section [Match]permet de cibler une ou plusieurs interfaces (shell regex, ou liste avec des espaces) selon les critères tels que le nom, l’identifiant, l’adresse MAC, etc.

Par exemple, pour configurer une interface avec une IP statique:

[Match]
Key=enp0s8

[Network]
Address=192.168.1.110/24
DNS=1.1.1.1
Oh un fichier de configuration d’interface qui a été sous tous les OS GNU (qui sont équipés de systemd)🔥🔥🔥

La doc officielle Detaille ( Faites-y un tour ) l’Ensemble des clauses POSSIBLES ET REALISER Ë may des Amener les configurations fines extrémement ET poussées:

pare-feu de configuration, de serveurs DNS, de serveurs NTP, etc. par interface

utilisation des protocoles liés aux réseaux VLAN, VXLAN, IPVLAN, VRF, etc.

configuration de fonctionnalités réseau comme le collage / association, pont, MacVTap, etc.

création de tunnel ou interface virtuelle

détermination manuelle de voisins L2 (table ARP)

etc

🌞 éditer la configuration d’une carte réseau de la VM avec un fichier .network

A noter qu’un outil tel que nmtuiles configurations réalisées avec NetworkManager et avec systemd-networkdles pilotes sont tous les deux du même API.

systemd-resolved
L’activation de systemd-resolvedpermet une résolution des noms de domaines avec un serveur DNS sandbox local (sur certaines distributions c’est fait par défaut). Certains bénéfices de l’utilisation de systemd-resolvedsont:

configuration de DNS par interface

aucune requête sur les DNS potentiellement injoignables
= pas de fuite
= optimisation du temps de recherche
résolution robuste avec un serveur DNS sandbox local

support de fonctionnalités telles que DNSSEC, DNS sur TLS, mise en cache de DNS

🌞Activer la résolution des noms par systemd-resolveddémarré le service (maintenant et au démarrage)

vérifier que le service est lancé
🌞vérifier qu’un serveur DNS tourne localement et écoute sur un port d’interface localhost (avec sspar exemple)

Une de commande Effectuer de nom en résolution Utilisant le serveur DNS explicitement mis en lieu par systemd-resolved(avec dig)
effectuer une requête DNS avec systemd-resolve
on peut utiliser resolvectlpour avoir des infos sur le serveur local

systemd-resolvedde nombreuses fonctionnalités comme le cache, le support de DNSSEC ou encore de DNS sur TLS. Une fois qu’il est en place, tout passe par lui, le fichier/etc/resolv.conf est donc obsolète

🌞This content is not available to DNS using the DNS server, the good practice is to /etc/resolv.confbe to need to symbol symbol/run/systemd/resolve/stub-resolv.conf
🌞 Modifier la configuration de systemd-resolved
elle est dans /etc/systemd/resolved.conf
ajouter les serveurs de votre choix
vérifier la modification avec resolvectl
🌞 mise en place de DNS sur TLS
Renseignez-vous sur les avantages de DNS sur TLS
effectuer une configuration globale (dans /etc/systemd/resolved.conf)
compléter la clause DNSpour ajouter un serveur qui prend en charge le DNS sur TLS (on peut trouver des listes sur Internet)
utiliser la clause DNSOverTLSpour activer la fonctionnalité
valeur opportunisticpour tester les résolutions à travers TLS, et fallback sur une résolution DNS classique
valeur yespour forcer les résolutions à travers TLS
Avec prouver tcpdumpQue les Resolutions bien à are TLS Travers (les DNS Qui Serveurs DNS le plus supportent TLS sur le port de écoutent 853)
🌞 activer l’utilisation de DNSSEC
5. Gestion de sessions logind
logind est le nom du démon qui gère les sessions.

On peut le manipuler avec loginctl. Rien de fou ici, je vous laisse explorer un peu la ligne de commande.

6. Gestion d’unité basique (services)
La principale entité qui systemdgère sont les unités système ou unités système .
Les unités sont définies dans les fichiers texte et permettent de les manipuler différents éléments du système:

prestations de service
point de montage
carte réseau
autres. (plus tard)
Les chemins où sont définies les unités sont les suivantes, du moins prioritaire, au plus prioritaires:

/usr/lib/systemd/system : utilisé par la plupart des installations par défaut
faites un tour et regardez un peu ce qui est balade là-bas
/run/systemd/system : utilisé au runtime d’un service
/etc/systemd/system : dossier dédié à la modification par l’administrateur
Donc si on veut ajouter une nouvelle unité, c’est dans /etc/systemd/system.

Manipulation d’unité systemd

lister les unités systemdactives de la machine
systemctl list-units
ou seulement systemctl
on peut ajouter des options pour filtre par type
pour ne lister que les services: systemctl list-units -t service
Beaucoup de commandes systemdqui écrivent les choses en sortie sont automatiquement fournies avec un pager (pipé dans less). On peut ajouter --no-pagerpour se débarasser de ce comportement

pour obtenir plus de détails sur une unité de données
systemctl is-active <UNIT>
déterminer si l’unité est actuellement en cours de fonctionnement
systemctl is-enabled <UNIT>
Déterminez si l’unité est liée à une cible (généralement, on s’en sert pour activer les unités au démarrage)
systemctl status <UNIT>
affiche l’état complet d’une unité de données
like the path
systemctl cat <UNIT>
affiche les fichiers qui définissent l’unité
Les outils de l’écosystème GNU / Linux ont été modifiés pour être utilisés avec systemd

par exemple ps
on peut utiliser pspour trouver l’un associé à un processus donné:ps -e -o pid,cmd,unit
on peut également effectuer un systemctl status <PID>retournement
les logs are Fournis par journald , les stats system par les Mécanismes de groupes de contrôle (on y revient , plus tard)
🌞 trouver l’unité associée au processus chronyd
II. Boot et Logs
systemdest le PID 1 sur les distributions GNU / Linux qui l’utilisent. C’est à dire qu’il est le premier processus lancé.
Il est connu pour accélérer le démarrage des machines GNU / Linux, avec l’aide de deux principes (déjà existant auparavant):

parallélisation des tâches
événementiel
c’est à dire que certaines tâches ne s’achètent pas est un évènement est réalisé
par exemple, le démon bluetooth ne s’activera pas s’il y a une requête Bluetooth (sur le socket dédié)
This place privilégiée lui permet d’être présent dans les premiers instants du démarrage de l’OS et ainsi de récupérer les métriques intéressantes.

🌞 générer un graphe de la séquence de démarrage
systemd-analyze plot > graphe.svg
très utile pour le débug
déterminer le temps après mis sshd.serviceà démarrer
on peut aussi utiliser systemd-analyse blameen ligne de commande
systemdembarque un démon de journalisation journald. Il centralise tous les journaux des machines de la façon la plus exhaustive possible. Il est notamment possible que la génération du graphe soit récupérée avec systemd-analyze plotles logs au chargement du noyau.

Les journaux sont stockés dans un format binaire inexploitable à la main. Plutôt relou. L’avantage est que les journaux deviennent exploitables de façon très flexible avec des commandes dédiées:

$ sudo journalctl -xe

# Logs kernel (similaire à dmesg)
$ sudo journalctl -k 

# Logs d'une unité spécifique ou process spécifique
$ sudo journalctl -xe -u sshd.service
$ sudo journalctl -xe _PID=1
# Pour plus de filtres
$ man systemd.journal-fields 

# Logs en temps réel
$ journalctl -xe -f

# Logs avec des ordres dans le temps
$ sudo journalctl -xe -u sshd.service --since yesterday
$ sudo journalctl -xe -u sshd.service  --since "2019-11-28 01:00:00"

# JSON output
$ sudo journalctl -xe -u sshd.service  --since "2019-11-28 01:00:00" --output=json
L’avantage des logs binaires, c’est qu’on peut plier dans tous les sens. Utilitaires pour interrogation principale, mais encore pour exportateur d’outils de centralisation ou d’analyse de journaux (environnement Cloud avec graphiques, graphiques d’erreur avec Grafana ou analyse avec un SOC).

III. Mécanismes manipulés par systemd
1. cgroups
Le terme cgroup signifie un noyau de labellisation de processus et des ressources appliquées aux processus.

En clair, cela permet de rassembler les processus en groupe de processus. Pour ensuite appliquer les restrictions d’accès aux ressources des machines comme:

utilisation CPU
utilisation RAM
I / O disque
utilisation réseau
espaces de noms d’utilisation (plus tard)
L’utilisation généralisée des groupes de contrôle des systèmes GNU / Linux permet un plus grand contrôle sur les processus et une administration plus aisée.
D’autres fonctionnalités qui sont ensuite incluses dans la surveillance des processus par les groupes ou la priorisation de l’accès aux ressources de la machine.

Par exemple ACDE PERMET attribution de juin , plus beau des ressources qu’avec le Mécanismes Comme oom_score (pour l’utilisation de la RAM) ou Le nice_score (pour l’ utilisation du CPU l’). Cherchez-le sur Google si vous ne le savez pas.

Les groupes possèdent une structure arborescente, comme une arborescende fichiers. En savoir plus sur le processus des fichiers🔥🔥🔥

Les groupes sont totalement intégrés aux systèmes GNU / Linux et ont systemdété construits avec les groupes en tête.

un scope systemd is a un groupe qui contient des processus non-gérés parsystemd

un slice systemd est un groupe de processussystemd

systemd-cgls

affiche une structure arborescente des cgroups
systemd-cgtop

affiche un affichage comme topavec l’utilisation des ressources en temps réel, par cgroups
ps -e -o pid,cmd,cgroup

ajoute l’affichage des groupes à ps
cat /proc/<PID>/cgroup

Prenez le temps de vous balader avec peu de commandes, et d’explorer /sys/fs/cgrouppour voir les restrictions mises en place.

🌞 identifiant le groupe utilisé par votre session SSH
identifiant la RAM maximale à votre disposition (dans /sys/fs/cgroup)
🌞 modifier la RAM dédiée à votre session utilisateur
systemctl set-property <SLICE_NAME> MemoryMax=512M
vérifier le changement
toujours dans /sys/fs/cgroup
la commande d’ systemctl set-propertyexaminer des fichiers dans/etc/systemd/system.control/
🌞 vérifier la création du fichier
on peut supprimer ces fichiers pour annuler les changements
Pour voir les indigènes de restrictions Propriétés possibles (en plus de de MemoryMax : $ man systemd.resource-control)

2. dbus
dbus is a technologie of IPC: elle permet la communication entre les processus. Ses caractéristiques sont:

c’est un démon (on peut vérifier qu’il tourne avec un ps -ef | grep dbus
permet l’identification
les processus s’abonnent à un bus donné (user or system by default, on peut créer d’autres)
les processus peuvent
émettre des signaux
évènement général envoyé en diffusion qui permet à tout le monde de savoir que cet événement a eu lieu
exécuter des méthodes
les méthodes utilisées à un objet donné et à déterminer une action
on peut avoir des valeurs d’entrée et de sortie
les échanges ont un format binaire standardisé (perfs, analyser, etc.)
les processus sont nommés
forcément un ID unique
possibilité de définir un nom (lisible par l’homme c’est quand même cool)
Il existe des outils sympas comme d-feetexplorer les bus de dbus avec une interface graphique.

Les commandes pour manipuler les commandes sont dbus-monitoret dbus-sendenvoyer des signaux ou appeler des méthodes

dbus-monitor --system pour observer le bus système
vous pouvez facilement gérer des messages en faisant des actions avec systemctlpar exemple
testez-vous sur votre écran USB?
🌞 observateur, identifiant, et expliquer complètement un évènement choisi
🌞 envoyer un signal pour générer un évènement
Exemple: est-ce que NetworkManager est activé?

sudo dbus-send --system --print-reply \
	--dest=org.freedesktop.NetworkManager \
	/org/freedesktop/NetworkManager \
	org.freedesktop.DBus.Properties.Get \
        string:"org.freedesktop.NetworkManager"  \
        string:"NetworkingEnabled
3. Espaces de noms et cgroups
On peut lancer un processus dans un service temporaire avec systemd-runlui appliquer des restrictions. Utile pour sandboxer un programme, et éventuellement tracer des informations, ou enregistrer ses activités dans le journal système.

lancer un processus sandboxé, et tracé, avec systemd-run
un shell par exemple, pour faire des tests réseaux sudo systemd-run --wait -t /bin/bash
un service est créé: systemctl status <NAME>
un groupe est créé: systemd-cglspour le repérer, puis /sys/fs/cgrouppour voir les restrictions appliquées
🌞 identifiant le cgroup utilisé
🌞 ajouter des restrictions cgroups
sudo systemd-run -p MemoryMax=512M <PROCESS>
🌞 ajouter un traçage réseau
sudo systemd-run -p IPAccounting=true <PROCESS>
effectuer un ping
quitter le shell
observateur les données récoltées
🌞 ajouter des restrictions réseau
-p IPAddressAllow=192.168.56.0/24 + -p IPAddressDeny=any
Lancer un processus complètement sandboxé (conteneur?) Avec systemd-nspawn:

sudo systemd-nspawn --ephemeral --private-network -D / bash
vérifier que --private-networka fonctionné:ip a
🌞 expliquer cette ligne de commande
🌞 prouver qu’un espace de nom réseau différent est utilisé
pour voir les espaces de noms utilisés par un processus donné, on peut aller voir dans /proc
ls -al /proc/<PID>/ns name less with names
si le nombre vu ici est différent le nombre vu pour un autre processus alors qu’ils sont dans les espaces de noms différents
🌞 ajouter au moins une option pour isoler encore un peu plus le processus lancé
IV unités systemd en profondeur
Les unités sont au coeur du fonctionnement de systemd. Les services ne sont qu’un type d’unité systemd, il existe d’autres. Dans cette partie sur le manipulateur:

un service
minuteur
prise
chemin
montage automatique
Pour une liste complète: systemctl -t help

Il est possible de tomber sur l’état de tous les services lancés par systemdavec$ systemd-analyze dump

1. Exploration de services existants
# Liste des service existants
$ systemctl list-units --all
$ systemctl --all

# Filtrer avec le type
$ sudo systemctl -t service --all

# Obtenir des infos sur une unité
$ sudo systemctl status <UNIT>

# Lire le fichier lié à l'unité
$ sudo systemctl cat <UNIT>

# Ajouter de la configuration dans un fichier drop-in
$ sudo systemctl edit <UNIT>

# Modifier complètement l'unité
$ sudo systemctl edit --full <UNIT>
faites des tests et observez la structure des unités, toujours identiques:
[Unit]
Description=XXX
# Clauses de dépendances, ordres de démarrage

[<TYPE>]
pour un service, un souvent
[Unit]
Description=XXX
# Clauses de dépendances, ordres de démarrage

[Service]
Type=(forking|simple|exec)
ExecStart=<CMD>
ExecStop=<CMD>
Type

exec : l’unité attend à quitter
simple : l’unité attendue d’être démon, qui doit rester actif
forking on the one be be to a demon qui crée de nouveaux processus (comme un serveur Apache par exemple)
ExecStart commande qui permet de lancer le service

ExecStop : commande qui permet de stopper le service

dans les cas les plus simples, il a systemdété contrôlé par le groupe de surveillance (il détermine le numéro du père du processus)
🌞 observateur auditd.service

trouver le chemin auditd.service
expliquer le principe de la clause ExecStartPost
expliquer les 4 “Paramètres de sécurité” dans auditd.service
2. Création de service simple
🌞Créer un fichier dont /etc/systemd/systemle suffixe .service:

doit posséder une description
doit lancer un serveur web
doit ouvrir un pare-feu de port quand il est lancé, et qu’il est une fois que le service est stoppé
doit être limité en RAM
/etc/systemd/systemest le chemin dédié aux modifications ou ajouts d’un systemdadministrateur par l’administrateur

Beuacoup beaucoup d’autres options sont disponibles pour un service, comme définition de variables d’environnement par service, ou un utilisateur spécifique. Inspirez-vous des fichiers de service existants.

3. Sandboxing (sécurité lourde)
Utiliser la commande systemd-analyze security <SERVICE>sur votre service précémment créé. This order permet d’analyser l’unité et d’afficher un score de sécurité, en fonction d’un certain nombre de critères.
Les critères correspondant à des clauses que l’on peut ajouter dans le fichier .service.

La plupart se basent sur les mécanismes suivants:

cgroups
sur un déjà vu: regroupement de processus et restriction d’accès au système
espaces de noms
isolation des ressources système (OS, PAS les ressources matérielles)
arbre de processus
arborescence utilisateur
accès aux systèmes de fichiers (stockage)
nommage de la machine
capacités
droits particuliers attribués à des processus
par exemple
CAP_NET_BIND_SERVICE : écouter sur un port en dessous de 1024
CAP_CHOWN : permet de changer arbitrairement les propriétaires des fichiers
CAP_DAC_OVERRIDE : “DAC” pour “Contrôle d’accès discrétionnaire”, dans notre cas c’est les droits rwx.)
seccomp
permet de filtrer les appels système qu’émet un processus
les appels système sont des fonctions du noyau, qui permettent de lui demander de demander
il existe moins de 200 éléments sur le système d’exploitation, ce qui permet de faire tout ce qu’un système d’exploitation permet de lire (ouvrir / écrire un fichier, ouvrir un port, lancer des processus, etc.)
Essayez d’obtenir le meilleur score avec systemd-analyze security <SERVICE>, en comprenant ce que vous avez fait.

🌞 Expliquer au moins cinq clauses de sécurité ajoutée
4. Activation par événement
systemd is also as for Customer Service Management.

A. Activation via socket UNIX
Web-based-to-web-server-to-web-server-in-one-do-it-port

Principe de mise en place:

# Création d'un fichier .service
$ sudo vim /etc/systemd/system/bap.service

# Création d'un fichier .socket correspondant
$ sudo vim /etc/systemd/system/bap.socket

# Syntaxe du fichier
$ cat /etc/systemd/system/bap.socket
[Unit]
Description=Bap socket

[Socket]
ListenStream=0.0.0.0:80

ExecStartPre=/usr/bin/firewall-cmd --add-port 80/tcp
ExecStartPre=/usr/bin/firewall-cmd --add-port 80/tcp --permanent
ExecStopPost=/usr/bin/firewall-cmd --remove-port 80/tcp
ExecStopPost=/usr/bin/firewall-cmd --remove-port 80/tcp --permanent

[Install]
WantedBy=sockets.target
🌞 Docker démarre tout seul s’il est sollicité

avoir installateur docker
vérifier que le service dockerest éteint ( systemctl is-active docker)
création d’un fichier /etc/systemd/system/docker.socket
écoute sur le socket UNIX utilisé par docker
activer le socket systemdet prouver que le démon dockerse lance uniquement lorsque le socket est sollicité
B. Activation automatique d’un point de montage
Il est également possible d’activer et de terminer les points de montage. Pour cela, on va utiliser automount.

Mettre en place automatiquement un point de montage NFS

mettre en place une deuxième VM qui expose un dossier en NFS
vérifier le bon fonctionnement du point de montage sur la machine client
monter le partage NFS sur /data/nfs
sur le client, créer un fichier .automountdans/etc/systemd/system
[Unit]
Description=Mount NFS directory

Requires=network-online.target
After=network-online.service

[Automount]
Where=/data/backup

[Install]
WantedBy=multi-user.target
et un fichier .mount
[Unit]
Description=Remote cifs backup mount script
Requires=network-online.target
After=network-online.service

[Mount]
What=NFS_SERVER_IP:PATH
Where=/data/backup
Options=noauto,x-systemd.automount
Type=nfs
🌞 Prouver le bon fonctionnement de l’automobile

C. Minuterie systemd
Ils systemdont un fonctionnement similaire au précédent: ils permettent de planifier des tâches. Cela dit, les tâches lancées seront des services système, sur profit alors de la journalisation et de la surveillance natif du système.

systemctl list-timers permet de lister les timers en cours
systemctl status <TIMER NAME permet d’obtenir plus d’infos
systemctl cat <TIMER_NAME pour voir la construction d’un timer existant
Mise en place :

🌞 Créer un script simpliste qui archive un dossier sur le chemin créé dans le 2.: /data/nfs
🌞Créer un fichier .servicequi lance la sauvegarde
🌞Créer un fichier .timerqui est sauvegardé tous les jours à heure fixe
en la clause Utilisant OnCalendar(voir la doc officielle verser les facts possibles)
5. Ordonner et grouper des services
La gestion des unités systemdest organisée de façon séquentielle et hiérarchique. C’est à dire, par exemple, on peut ordonner à une unité de devenir qu’une autre unité ait été.

Pour afficher les dépendances d’une unité de données, on peut utiliser systemctl list-dependencies.

systemctl list-dependencies systemd-udevd.service
the démon udevdunder brique centrale of the os, elle est démarrée très tôt et n’a que peu de dépendances
Sur distinctera:

le fait de grouper des unités, avec target systemd
systemctl -t target pour lister les cibles actives
systemctl -t target --all pour aussi obtenir les cibles inactifs
l’appartenance targetto a self-end dans le fichier unit [Install]
permet de grouper logiquement des unités
un .targetqui regroupe nos applications web et base de données par exemple
remplace l’utilisation des runlevels
sur utiliser plus initmais la commandesystemctl isolate
systemctl isolate permet de lire le contenu d’une cible, de lancer les applications correspondantes, et de mettre fin à toutes les autres qui ne sont pas concernées par la cible
systemctl isolate shutdown.target a le même effet que init 0
gérer les relations entre les unités
ACDE se fait des clauses with Comme Require, Before, Afteretc.
ces clauses sont dans les fichiers de définitions d’unités systemd, dans la section[Unit]
Par exemple, l’unité docker.service

# /usr/lib/systemd/system/docker.service
[Unit]
Description=Docker Application Container Engine
Documentation=https://docs.docker.com
After=network-online.target docker.socket firewalld.service
Wants=network-online.target
Requires=docker.socket

[Service]
Type=notify
# the default is not to use systemd for cgroups because the delegate issues still
# exists and systemd currently does not support the cgroup feature set required
# for containers run by docker
ExecStart=/usr/bin/dockerd -H fd://
ExecReload=/bin/kill -s HUP $MAINPID
LimitNOFILE=1048576
# Having non-zero Limit*s causes performance problems due to accounting overhead
# in the kernel. We recommend using cgroups to do container-local accounting.
LimitNPROC=infinity
LimitCORE=infinity
# Uncomment TasksMax if your systemd version supports it.
# Only systemd 226 and above support this version.
#TasksMax=infinity
TimeoutStartSec=0
# set delegate yes so that systemd does not reset the cgroups of docker containers
Delegate=yes
# kill only the docker process, not all processes in the cgroup
KillMode=process
# restart the docker process if it exits prematurely
Restart=on-failure
StartLimitBurst=3
StartLimitInterval=60s

[Install]
WantedBy=multi-user.target
le service docker.serviceappartient autarget multi-user.target
l’équivalent du runlevel 5
le service docker.servicedoit se lancer après firewalld.serviceetnetwork.service
Mais aussi après la création du socket lié à la communication avec le démon docker docker.socket
on peut d’ailleurs visualiser la définition de ce socket avec les comandes systemctl status docker.socketousystemctl cat docker.socket
🌞créer un targetsystemd (inspirez-vous des targetanciens et de la doc)
ce qu’il faut targetdémarrer deux autres unités de votre choix
L’une des deux unités doit absolument démarrer après l’autre
Web qui démarrent après avoir été créé
base de données qui démarre avant le service W
