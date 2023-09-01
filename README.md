# orangepizero3-kiosk-image
Créer un client kiosk avec un orange pi zero 3 


Pour créer une borne de consultation du solde, il faut un pc arm ou orange pi zero 3 qui démarre avec linux en mode kiosk.
Le démarrage arrive directement dans le navigateur sur la page prévu pour la consultation du solde, les message du démarrage seront désactivé en production et le pointeur de souris disparaître en moins de 1 seconde.
Nous désactiverons aussi les console virtuel et certains raccourci firefox
On crée la carte sd avec l’image Debian cli de orangepi zero 3, l’os est l’image   
qui vient de cette source https://github.com/leeboby/armbian-images
L’image de debian 12 1gb serveur, on la grave sur la carte sd une sandisk;)

on démarre le orange pi zero 3, on créer un mot de passe pour root et un user.

(par défault user=root et pwd=deglea et un user=deglea pwd=deglea, ou a votre choix) 


Installer xorg lightdm :

apt update
apt upgrade
apt install xorg lightdm firefox-esr

Autologin Lightdm :

Pour que lightdm passe en autologin, il faut modifier le fichier
/etc/lightdm/lightdm.conf

Trouver la section [Seat:*] et modifier les paramètres suivant et retirer le #

(il faut être dans la section [Seat:*] !!!!!

#autologin-user= deglea
#autologin-user-timeout=0


Installer openbox gestionnaire de fenêtre léger :


apt install openbox


Pour démarrer une application xwindow au démarrage, il faut créer dans le répertoire .conf de utilisateur ici deglea un fichier autostart et donner les droits à cette utilisateur, il est propriétaire du fichier.
Le répertoire et le fichier autostart doivent être la propriété du l’utilisateur,le même utilisateur qui aura fait l’autologin de Lightdm

Créer le répertoire openbox si il n’existe pas

cd /home/deglea/.config

mkdir openbox #crée le répertoire

chown deglea:deglea openbox #change le user et le groupe 

cd openbox

touch autostart #crée le fichier autostart

chown deglea:deglea autostart #change le user et groupe

on devrait avoir .home/deglea/.config/openbox/autostart, ce fichier sera à orangepi et du groupe deglea si pas changer avec chow.

Dans le fichier, il sera indiqué la commande pour lancer l’application dans notre cas Firefox.

firefox-esr –kiosk http://5.196.66.41/login


Désactivé les messages boot :

Pour désactiver les message lors du démarrage, il faut éditer les fichiers 

nano /boot/armbianEnv.txt

A la ligne « console=both » il faut remplacer le both par serial.

Sur d’autre carte il fait mettre « tty1 »


Et ensuite un reboot

Désactivé le pointeur souris :

apt install unclutter # unclutter permet d’éffacer le pointeur souris 

On a joute dans le fichier /home/deglea/.config/openbox/autostart

On obtient :

unclutter -idle 0.01 &
firefox --kiosk http://5.196.66.41/login


Désactiver des touches et des raccourcis du clavier : (beta)

Pour désactiver raccourcis du clavier, il faut utiliser xmodmap.
Dans le répertoire personnel de l’utilisateur il faut créer un fichier .Xmodmap dans lequel on inscrit le code clavier correspondant à la combinaison de touche 
Il est possible de bloquer les combinaison de touche ALT+CTRL+F1 pour ne pas avoir la possibilité d’accéder à la console virtuel ainsi que tous les autres raccourcis système et même ceux de firefox 

Exemple : ALT+P correspond à 33.

touch .Xmodmap

nano .Xmodmap


Dans le fichier on ajoute les raccourcis

keycode <valeur de la clé Alt+F4> = NoSymbol


(La command xev permet de voir toute les activités clavier et souris)


Ensuite il faut lancer la commande :


xmodmap ~/.Xmodmap (dans le fichier autostart de openbox à testé)





Exemple de fichier .Xmodmap :

Dans cette exemple, les raccourcis claviers système et certains raccourcis firefox-esr en kiosk empêcheront l’utilisateur de sortir de l’application web si l’ on branche un clavier.  

keycode 23 = NoSymbol
keycode 29 = NoSymbol
keycode 33 = NoSymbol
keycode 37 = NoSymbol
keycode 38 = NoSymbol
keycode 57 = NoSymbol
keycode 64 = NoSymbol
keycode 65 = NoSymbol
keycode 67 = NoSymbol
keycode 68 = NoSymbol
keycode 69 = NoSymbol
keycode 70 = NoSymbol
keycode 71 = NoSymbol
keycode 72 = NoSymbol
keycode 73 = NoSymbol
keycode 74 = NoSymbol
keycode 75 = NoSymbol
keycode 76 = NoSymbol
keycode 77 = NoSymbol
keycode 95 = NoSymbol
keycode 96 = NoSymbol
keycode 142 = NoSymbol
keycode 166 = NoSymbol
keycode 167 = NoSymbol
keycode 180 = NoSymbol
keycode 182 = NoSymbol
keycode 213 = NoSymbol
keycode 214 = NoSymbol


Désactivé les console virtuel  CTRL+ALT+F..: (beta)

Pour désactivé le console virtuel, dans certains cas utile pour ne pas accéder à la console virtuel.

Editer le fichier /etc/systemd/logind.conf et modifier le nombre de console, dans ce cas il en reste que une.
 
[Login]
NAutoVTs=1
