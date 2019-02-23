# B1 Réseau 2018 - TP5

# Sommaire
* [I. Préparation du lab](#i-préparation-du-lab)
* [II. Lancement et configuration du lab](#ii-lancement-et-configuration-du-lab)
* [III. DHCP](#iii-dhcp)

# I. Préparation du lab

## 1. Préparation VMs

[x] **1. Création d'un nouveau host-only**
  * peu importe l'adresse on s'en servira juste pour faire du [SSH](../../cours/lexique.md#ssh--secure-shell)
  * **activez le DHCP** comme ça on aura pas besoin de saisir les IPs

[x] **2. Création des VMs**
* On va juste cloner trois VMs depuis le patron du TP précédent :
  * `server1.tp5.b1` sera dans `net1` et portera l'IP `10.5.1.10/24`
  * `client1.tp5.b1` sera dans `net2` et portera l'IP `10.5.2.10/24`
  * `client2.tp5.b1` sera dans `net2` et portera l'IP `10.5.2.11/24`
* Ajoutez aux trois VMs une interface host-only **en deuxième carte** dans le host-only précédemment créé

[x] **3. Vous clonez juste les VMs, vous ne les allumez pas.**  
* Ensuite RDV dans GNS3 : Edit > Preferences > VirtualBox VMs et vous ajoutez les trois VMs. 

[x] **4. Config réseau des dans GNS3** 
* Sur les 3 VMs 
  * Clic-droit > Configure > Network
  * mettre 2 cartes réseau

[x] ## 2. Préparation Routeurs Cisco
Importez l'ISO du routeur et mettez-en deux dans GNS3 : 
* `router1.tp5.b1` est dans :
  * `net1` et portera l'IP `10.5.1.254/24`
  * `net12`
* `router2.tp5.b1` est dans :
  * `net2` et portera l'IP `10.5.2.254/24`
  * `net12`

**Vous devrez déterminer vous-même un réseau et un masque pour `net12` et le justifier**. Il n'y aura que deux routeurs dans ce réseau.

## 3. Préparation Switches
Rien à faire ici, on va utiliser les Ethernet Switches de GNS3 comme de bêtes multiprises. Un switch n'a pas d'IP. 

## 4. Topologie et tableau récapitulatif

**Topologie :**
```
                                             client1
                                            /
Server1 --net1-- R1 --net12-- R2 --net2-- Sw
                                            \
                                             client2
```

**Réseaux :**

* `net1` : `10.5.1.0/24`
* `net2` : `10.5.2.0/24`
* `net12` : **votre choix** (à justifier)

**Machines :**

Machine | `net1` | `net2` | `net12`
--- | --- | --- | ---
`client1.tp5.b1` | X | `10.5.2.10` | X
`client2.tp5.b1` | X | `10.5.2.11` | X
`router1.tp5.b1` | `10.5.1.254` | X | *Votre choix*
`router2.tp5.b1` | X | `10.5.2.254` | *Votre choix*
`server1.tp5.b1` | `10.5.1.10` | X | X

# II. Lancement et configuration du lab

### Checklist IP VMs 

On parle de `client1.tp5.b1`, `client2.tp5.b1` et `server1.tp5.b1` :
* [X] Désactiver SELinux
  * déja fait dans le patron
* [X] Installation de certains paquets réseau
  * déja fait dans le patron
* [X] Désactivation de la carte NAT
  * déja fait dans le patron
* [ ] [Définition des IPs statiques](../../cours/procedures.md#définir-une-ip-statique)
* [ ] La connexion SSH doit être fonctionnelle
  * une fois fait, vous avez vos trois fenêtres SSH ouvertes, une dans chaque machine
* [ ] [Définition du nom de domaine](../../cours/procedures.md#changer-son-nom-de-domaine)

### Checklist IP Routeurs 

On parle de `router1.tp5.b1` et `router2.tp5.b1` :
* [ ] [Définition des IPs statiques](../../cours/procedures-cisco.md#définir-une-ip-statique)
* [ ] [Définition du nom de domaine](../../cours/procedures-cisco.md#changer-son-nom-de-domaine)

### Checklist routes 

On parle de toutes les machines :
* [ ] `router1.tp5.b1`  
  * directement connecté à `net1` et `net12`  
  * [route à ajouter](../../cours/procedures-cisco.md#ajouter-une-route-statique) : `net2`  
* [ ] `router2.tp5.b1`
  * directement connecté à `net2` et `net12`  
  * [route à ajouter](../../cours/procedures-cisco.md#ajouter-une-route-statique) : `net1`  
* [ ] `server1.tp5.b1`  
  * directement connecté à `net1`  
  * [route à ajouter](../../cours/procedures.md#ajouter-une-route-statique) : `net2`
  * [fichiers `hosts`](../../cours/procedures.md#editer-le-fichier-hosts) à remplir : `client1.tp5.b1`, `client2.tp5.b1`
* [ ] `client1.tp5.b1`
  * directement connecté à `net2`  
  * [route à ajouter](../../cours/procedures.md#ajouter-une-route-statique) : `net1`
  * [fichiers `hosts`](../../cours/procedures.md#editer-le-fichier-hosts) à remplir : `server1.tp5.b1`, `client2.tp5.b1`
* [ ] `client2.tp5.b1`
  * directement connecté à `net2`  
  * [route à ajouter](../../cours/procedures.md#ajouter-une-route-statique) : `net1`
  * [fichiers `hosts`](../../cours/procedures.md#editer-le-fichier-hosts) à remplir : `server1.tp5.b1`, `client1.tp5.b1`

# III. DHCP

## 1. Mise en place du serveur DHCP

[x] **1. [Renommer la machine](../../cours/procedures.md#changer-son-nom-de-domaine)**

[x] **2. Installer le serveur DHCP** en faisant un peu de crasse :

[x] **3. Rallumer la VM dans GNS**

[x] **4. Configuration du serveur DHCP**

[x] **5. Démarrer le serveur DHCP**

[x] **6. Faire un test**

## 2. Explorer un peu DHCP