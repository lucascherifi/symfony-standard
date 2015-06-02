Environnement de développement
==============================

## Prérequis

### 1) Installer les paquets nécessaires :

VirtualBox (si vous ne l'avez pas déjà)

    sudo apt-get install virtualbox
    
Vagrant (et support NFS)

    wget https://dl.bintray.com/mitchellh/vagrant/vagrant_1.7.2_x86_64.deb
    sudo dpkg -i vagrant_1.7.2_x86_64.deb
    sudo apt-get install nfs-common nfs-server
    
Ansible (non disponible sur Windows)

    sudo apt-add-repository ppa:ansible/ansible
    sudo apt-get update
    sudo apt-get install dkms ansible software-properties-common

### 2) Configuration nécessaire de votre machine :

# Une clef ~/.ssh/id_rsa.pub sur votre hôte partagée avec votre compte bitbucket.

    @see https://bitbucket.org/account/user/[VOTRE-IDENTIFIANT]/ssh-keys/

# Les repertoires ~/.composer et ~/.vagrant-apt-archives présents (même vides)

    mkdir ~/.composer; ~/.vagrant-apt-archives;

# Mettre à  jour le fichier `/etc/hosts` local en ajoutant :

    #============
    # SYMFONY
    #============

    192.168.33.2 local.symfony pma.local.symfony

# Renseignez votre token github : https://github.com/settings/applications, "Generate new token"
        
        cp ansible/vars/local.yml.dist ansible/vars/local.yml
        vi ansible/vars/local.yml
        
Cette étape obligatoire permettra d'accélérer l'étape du `composer install`
 

### 3) Installer la VM sur la machine
        
        vagrant up

### 4) Vous avez terminé !

pour vous connecter à la VM : 

    vagrant ssh

## Comprendre le fonctionnement

En lançant :

    vagrant up

le fichier `./VAGRANTFILE` est lu.

Dans ce fichier, il est précisé :

 * de télécharger et installer une VM VirtualBox `Debian Wheezy 64bits` incluant tous les packets nécessaires au développement (back end et front end) utilisant 4Go de votre mémoire RAM.
 * de lancer sur cette VM le playbook ansible situé dans `ansible/playbook.yml`.

 Ce playbook execute sur la VM les actions suivantes :

* Personnalisation du hostname
* Personnalisation du Motd
* Mise à jour du /etc/hosts de la machine invitée
* Configuration du vhost nginx de PhpMyAdmin
* Installation de la base MySQL et des users 
* Déplacement des répertoires à forte lecture/écriture sur la RAM
* Installation de composer puis des vendors
* Création du schéma de la base et ajout des fixtures
* Insallation des vendors "bundler"
* Mise en place du vhost Nginx
* ...

-
