## 1. First steps

			-   `systemctl --version`  = 243
-   ![sun_with_face](https://github.githubassets.com/images/icons/emoji/unicode/1f31e.png)  s'assurer que  `systemd`  est PID1 :
		 -   `pidof systemd`  ->  `902 1`
-   ![sun_with_face](https://github.githubassets.com/images/icons/emoji/unicode/1f31e.png)  check tous les autres processus système (**NOT**  kernel processes) :
	   -   `ps ax | less`  ->

## 2. Gestion du temps

		-   `timedatectl` Juste cette commande suffit

   ![sun_with_face](https://github.githubassets.com/images/icons/emoji/unicode/1f31e.png)  déterminer la différence entre Local Time, Universal Time et RTC time :
    -   Local Time : l'heure local (propre à la région )
    -   Universal Time : l'heure mondial 
    -   RTC time : l'heure propre à ( et dans) la machine , et bien plus précise,
        -   l'avantage d'utiliser RTC time est pour la précision, il est possible de déclencher des tâches à la nanoseconde précise, comme cela pourrait être utile en bourse ou autre.
-   timezones
    -   ![sun_with_face](https://github.githubassets.com/images/icons/emoji/unicode/1f31e.png)  changer de timezone pour un autre fuseau horaire européen :
        -   `timedatectl set-timezone Europe/London`
    -   ![sun_with_face](https://github.githubassets.com/images/icons/emoji/unicode/1f31e.png)  désactiver le service lié à la synchronisation du temps avec cette commande, et vérifier à la main qu'il a été coupé
        -   `timedatectl set-ntp 0`
        -   `sudo tcpdump -n "broadcast or multicast" | grep NTP`
            -   aucun traffic , ntp est donc désactivé

## 3. Gestion de noms

-   `hostnamectl set-hostname oct`
-   il est possible de changer trois noms avec  `--pretty`,  `--static`  et  `--transient`
-   ![sun_with_face](https://github.githubassets.com/images/icons/emoji/unicode/1f31e.png)  expliquer la différence entre les trois types de noms. Lequel est à utiliser pour des machines de prod ?
    -   `--pretty`  : hostname is a free-form UTF8 host name for presentation to the user.
    -   `--static`  : traditional hostname stored in the /etc/hostname
    -   `--transient`  : dynamic host name maintained by the kernel
        -   Pour une utilisation sur des machines de prod , il faut utiliser le  `--static`

## 4. Gestion du réseau (et résolution de noms)


-   ![sun_with_face](https://github.githubassets.com/images/icons/emoji/unicode/1f31e.png)  afficher les informations DHCP récupérées par NetworkManager (sur une interface en DHCP)
    
-   ![sun_with_face](https://github.githubassets.com/images/icons/emoji/unicode/1f31e.png)  stopper et désactiver le démarrage de  `NetworkManager`
    
    -   `sudo systemctl stop NetworkManager`
    -   `sudo systemctl disable NetworkManager`
-   ![sun_with_face](https://github.githubassets.com/images/icons/emoji/unicode/1f31e.png)  démarrer et activer le démarrage de  `systemd-networkd`
    
    -   `sudo systemctl start systemd-networkd`
    -   `sudo systemctl enable systemd-networkd`
-   ![sun_with_face](https://github.githubassets.com/images/icons/emoji/unicode/1f31e.png)  éditer la configuration d'une carte réseau de la VM avec un fichier  `.network`
    

### systemd-resolved`

-   ![sun_with_face](https://github.githubassets.com/images/icons/emoji/unicode/1f31e.png)  activer la résolution de noms par  `systemd-resolved`  en démarrant le service (maintenant et au boot)
    
    -   `sudo systemctl start systemd-resolved`
    -   `sudo systemctl enable systemd-resolved`
    -   `sudo systemctl status systemd-resolved`
-   ![sun_with_face](https://github.githubassets.com/images/icons/emoji/unicode/1f31e.png)  vérifier qu'un serveur DNS tourne localement et écoute sur un port de l'interfce localhost (avec  `ss`  par exemple)
    
    -   `ss -4 state listening`
        -   nous pouvons voir notre DNS qui écoute
-   ![sun_with_face](https://github.githubassets.com/images/icons/emoji/unicode/1f31e.png)  Afin d'activer de façon permanente ce serveur DNS, la bonne pratique est de remplacer  `/etc/resolv.conf`  par un lien symbolique pointant vers  `/run/systemd/resolve/stub-resolv.conf`
    
    -   `ln –s /run/systemd/resolve/stub-resolv.conf /etc/resolv.conf`

-  ![sun_with_face](https://github.githubassets.com/images/icons/emoji/unicode/1f31e.png)  Modifier la configuration de  `systemd-resolved`  - elle est dans  `/etc/systemd/resolved.conf`  - ajouter les serveurs de votre choix  - vérifier la modification avec  `resolvectl`  -  ![sun_with_face](https://github.githubassets.com/images/icons/emoji/unicode/1f31e.png)  mise en place de DNS over TLS  - renseignez-vous sur les avantages de DNS over TLS  - effectuer une configuration globale (dans  `/etc/systemd/resolved.conf`)  - compléter la clause  `DNS`  pour ajouter un serveur qui supporte le DNS over TLS (on peut en trouver des listes sur internet)  - utiliser la clause  `DNSOverTLS`  pour activer la fonctionnalité  - valeur  `opportunistic`  pour tester les résolutions à travers TLS, et fallback sur une résolution DNS classique en cas d'erreur  - valeur  `yes`  pour forcer les résolutions à travers TLS  - prouver avec  `tcpdump`  que les résolutions sont bien à travers TLS (les serveurs DNS qui supportent le DNS over TLS écoutent sur le port 853)  - vous pouvez aussi ajouter DNSSEC, en passant la valeur à True dans le fichier de configuration de  `systemd-resolved`

## 5-gestion-de-sess

## 5. Gestion de sessions  `logind`

## 6. Gestion d'unité basique (services)

-   ![sun_with_face](https://github.githubassets.com/images/icons/emoji/unicode/1f31e.png)  trouver l'unité associée au processus  `chronyd`  -`└─system.slice`  `├─httpd.service`  `├─chronyd.service`  `│ └─613 /usr/sbin/chronyd -u chrony`

# II. Boot et Logs

-  ![sun_with_face](https://github.githubassets.com/images/icons/emoji/unicode/1f31e.png)  générer un graphe de la séquence de boot  ~~ -  `systemd-analyze plot > graphe.svg`~~ ~~ - très utile pour du débug~~ ~~ - déterminer le temps qu'a mis  `sshd.service`  à démarrer~~ ~~- on peut aussi utiliser  `systemd-analyse blame`  en ligne de commande ~~

# III. Mécanismes manipulés par systemd

## [](https://github.com/octaveOJK/Tp_Open_S#1-cgroups)[](https://github.com/octaveOJK/Tp_Open_S#1-cgroups)1. cgroups

-   ![sun_with_face](https://github.githubassets.com/images/icons/emoji/unicode/1f31e.png)  identifier le cgroup utilisé par votre session SSH
    
    -   identifier la RAM maximale à votre disposition (dans  `/sys/fs/cgroup`)
    -   `systemd-cgls`  puis on cherche notre session ssh -`└─system.slice`
        -   `├─sshd.service`
        -   `│ └─1068 /usr/sbin/sshd -D`
-   ![sun_with_face](https://github.githubassets.com/images/icons/emoji/unicode/1f31e.png)  modifier la RAM dédiée à votre session utilisateur
    
    -   `systemctl set-property <SLICE_NAME> MemoryMax=512M`
    -   vérifier le changement
        -   toujours dans  `/sys/fs/cgroup`
-   la commande  `systemctl set-property`  génère des fichiers dans  `/etc/systemd/system.control/`
    
    -   ![sun_with_face](https://github.githubassets.com/images/icons/emoji/unicode/1f31e.png)  vérifier la création du fichier
    -   on peut supprimer ces fichiers pour annuler les changements

## 2. dbus

-   ![sun_with_face](https://github.githubassets.com/images/icons/emoji/unicode/1f31e.png)  observer, identifier, et expliquer complètement un évènement choisi
-   ![sun_with_face](https://github.githubassets.com/images/icons/emoji/unicode/1f31e.png)  envoyer un signal pour générer un évènement

## 3. Namespaces et cgroups

-   lancer un processus sandboxé, et tracé, avec  `systemd-run`
    -   un shell par exemple, pour faire des tests réseaux  `sudo systemd-run --wait -t /bin/bash`
    -   un service est créé :  `systemctl status <NAME>`
    -   un cgroup est créé :  `systemd-cgls`  pour le repérer, puis  `/sys/fs/cgroup`  pour voir les restrictions appliquées
    -   ![sun_with_face](https://github.githubassets.com/images/icons/emoji/unicode/1f31e.png)  identifier le cgroup utilisé
    -   ![sun_with_face](https://github.githubassets.com/images/icons/emoji/unicode/1f31e.png)  ajouter des restrictions cgroups
        -   `sudo systemd-run -p MemoryMax=512M <PROCESS>`
    -   ![sun_with_face](https://github.githubassets.com/images/icons/emoji/unicode/1f31e.png)  ajouter un traçage réseau
        -   `sudo systemd-run -p IPAccounting=true <PROCESS>`
            -   effectuer un ping
            -   quitter le shell
            -   observer les données récoltées
    -   ![sun_with_face](https://github.githubassets.com/images/icons/emoji/unicode/1f31e.png)  ajouter des restrictions réseau
        -   `-p IPAddressAllow=192.168.56.0/24`  +  `-p IPAddressDeny=any`

----------

Lancer un processus complètement sandboxé (conteneur ?) avec  `systemd-nspawn`  :

-   `sudo systemd-nspawn --ephemeral --private-network -D / bash`
    -   vérifier que  `--private-network`  a fonctionné :  `ip a`
    -   ![sun_with_face](https://github.githubassets.com/images/icons/emoji/unicode/1f31e.png)  expliquer cette ligne de commande
    -   ![sun_with_face](https://github.githubassets.com/images/icons/emoji/unicode/1f31e.png)  prouver qu'un namespace réseau différent est utilisé
        -   pour voir les namespaces utilisés par un processus donné, on peut aller voir dans  `/proc`
        -   `ls -al /proc/<PID>/ns`  : montre les liens vers les namespaces utilisés (identifiés par des nombres)
        -   si le nombre vu ici est différent du nombre vu pour un autre processus alors ils sont dans des namespaces différents
    -   ![sun_with_face](https://github.githubassets.com/images/icons/emoji/unicode/1f31e.png)  ajouter au moins une option pour isoler encore un peu plus le processus lancé

## IV. systemd units in-depth

##1. Exploration de services existants

-   ![sun_with_face](https://github.githubassets.com/images/icons/emoji/unicode/1f31e.png)  observer l'unité  `auditd.service`
    
    -   trouver le path où est définit le fichier  `auditd.service`
    -   expliquer le principe de la clause  `ExecStartPost`
    -   expliquer les 4 "Security Settings" dans  `auditd.service`

## 2. Création de service simple

![sun_with_face](https://github.githubassets.com/images/icons/emoji/unicode/1f31e.png)  Créer un fichier dans  `/etc/systemd/system`  qui comporte le suffixe  `.service`  :

-   doit posséder une description
-   doit lancer un serveur web
-   doit ouvrir un port firewall quand il est lancé, et le fermer une fois que le service est stoppé
-   doit être limité en RAM

## 3. Sandboxing (heavy security)


`systemd.unit=`, `rd.systemd.unit=`[](https://www.freedesktop.org/software/systemd/man/systemd.html#systemd.unit= "Lien permanent vers ce terme")

Remplace l'unité à activer au démarrage. La valeur par défaut est `default.target`. Cela peut être utilisé pour démarrer temporairement dans une unité de démarrage différente, par exemple `rescue.target`ou `emergency.service`. Voir [systemd.special (7)](https://www.freedesktop.org/software/systemd/man/systemd.special.html#) pour plus de détails sur ces unités. L'option préfixée par " `rd.`" est uniquement prise en compte dans le disque RAM initial (initrd), tandis que celle qui n'est pas préfixée uniquement dans le système principal.

`systemd.dump_core`[](https://www.freedesktop.org/software/systemd/man/systemd.html#systemd.dump_core "Lien permanent vers ce terme")

Prend un argument booléen ou active l'option si elle est spécifiée sans argument. Si activé, le gestionnaire systemd (PID 1) vide le noyau en cas de plantage. Sinon, aucun core dump n'est créé. Les valeurs par défaut sont activées.

	- systemd.show_status

Prend un argument booléen ou la constante `auto`. Peut également être spécifié sans argument, avec le même effet qu'un booléen positif. Si activé, le gestionnaire systemd (PID 1) affiche des mises à jour succinctes sur l’état du service sur la console lors du démarrage


	- systemd.crash_reboot

Prend un argument booléen ou active l'option si elle est spécifiée sans argument. Si cette option est activée, le gestionnaire de système (PID 1) redémarre automatiquement la machine après une panne, après un délai de 10 secondes. Sinon, le système se bloquera indéfiniment. Par défaut, cette option est désactivée afin d'éviter une boucle de redémarrage. Si combiné avec `systemd.crash_shell`, le système est redémarré après la fermeture du shell.

		systemd.show_status

Prend un argument booléen ou la constante `auto`. Peut également être spécifié sans argument, avec le même effet qu'un booléen positif.




-   ![sun_with_face](https://github.githubassets.com/images/icons/emoji/unicode/1f31e.png)  Expliquer au moins 5 cinq clauses de sécurité ajoutées

## 4. Event-based activation

### A. Activation via socket UNIX![sun_with_face](https://github.githubassets.com/images/icons/emoji/unicode/1f31e.png)  Faire en sorte que Docker démarre tout seul s'il est sollicité

-   avoir installer  `docker`
-   vérifier que le service  `docker`  est éteint (`systemctl is-active docker`)
-   création d'un fichier  `/etc/systemd/system/docker.socket`
-   faire en sorte que le socket écoute sur le socket UNIX utilisé par docker
-   activer le socket  `systemd`  et prouver que le démon  `docker`  se lance uniquement lorsque le socket est sollicité

### B. Activation automatique d'un point de montage

```

🌞 **Prouver le bon fonctionnement de l'automount**

### [](#c-timer-systemd)C. Timer `systemd`

Les timers `systemd` ont un fonctionnement similaire au démon cron : ils permettent de planifier des tâches. Cela dit, les tâches lancées seront des services systemd, on profite alors du logging et monitoring natif du système.

-   `systemctl list-timers` permet de lister les timers en cours
-   `systemctl status <TIMER NAME` permet d'obtenir plus d'infos
-   `systemctl cat <TIMER_NAME` pour voir la construction d'un timer existant

Mise en place :

-   🌞 Créer un script simpliste qui archive un dossier sur le path créé dans le 2. : `/data/nfs`
-   🌞 Créer un fichier `.service` qui lance la backup
-   🌞 Créer un fichier `.timer` qui programme la backup tous les jours à heure fixe
    -   en utilisant la clause `OnCalendar` (voir [la doc officielle](https://www.freedesktop.org/software/syste
```
