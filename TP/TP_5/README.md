# B1 Réseau 2018 - TP5

# Sommaire
* [I. Préparation du lab](#i-préparation-du-lab)
* [II. Lancement et configuration du lab](#ii-lancement-et-configuration-du-lab)
* [III. DHCP](#iii-dhcp)

# I. Préparation du lab

## 1. Préparation VMs

* [x] **1. Création d'un nouveau host-only**
  * peu importe l'adresse on s'en servira juste pour faire du [SSH](../../cours/lexique.md#ssh--secure-shell)
  * **activez le DHCP** comme ça on aura pas besoin de saisir les IPs

* [x] **2. Création des VMs**
* On va juste cloner trois VMs depuis le patron du TP précédent :
  * `server1.tp5.b1` sera dans `net1` et portera l'IP `10.5.1.10/24`
  * `client1.tp5.b1` sera dans `net2` et portera l'IP `10.5.2.10/24`
  * `client2.tp5.b1` sera dans `net2` et portera l'IP `10.5.2.11/24`
* Ajoutez aux trois VMs une interface host-only **en deuxième carte** dans le host-only précédemment créé

* [x] **3. Vous clonez juste les VMs, vous ne les allumez pas.**  
* Ensuite RDV dans GNS3 : Edit > Preferences > VirtualBox VMs et vous ajoutez les trois VMs. 

* [x] **4. Config réseau des dans GNS3** 
* Sur les 3 VMs 
  * Clic-droit > Configure > Network
  * mettre 2 cartes réseau

* [x] ## 2. Préparation Routeurs Cisco
Importez l'ISO du routeur et mettez-en deux dans GNS3 : 
* `router1.tp5.b1` est dans :
  * `net1` et portera l'IP `10.5.1.254/24`
  * `net12`
* `router2.tp5.b1` est dans :
  * `net2` et portera l'IP `10.5.2.254/24`
  * `net12`

**Vous devrez déterminer vous-même un réseau et un masque pour `net12` et le justifier**. Il n'y aura que deux routeurs dans ce réseau.

## 3. Préparation Switches
Utilisation des switchs DNS3 comme multiprise rien a faire.

## 4. Topologie et tableau récapitulatif

**Topologie :**
```
                                             client1
                                            /
Server1 --net1-- R1 --net12-- R2 --net2-- Sw
                                            \
                                             client2
```

**Machines :**

Machine | `net1` | `net2` | `net12`
--- | --- | --- | ---
`client1.tp5.b1` | X | `10.5.2.10` | X
`client2.tp5.b1` | X | `10.5.2.11` | X
`router1.tp5.b1` | `10.5.1.254` | X | `10.5.10.1`
`router2.tp5.b1` | X | `10.5.2.254` | `10.5.10.2`
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

Pour définir les IPs statiques, les noms de domaines et avoir une connexion ssh fonctionnelle nous allons suivre les procédures vues dans les TP précédents. 

### Checklist IP Routeurs 

On parle de `router1.tp5.b1` et `router2.tp5.b1` :
* [x] [Définition des IPs statiques](../../cours/procedures-cisco.md#définir-une-ip-statique)

**1. Repérer le nom de l'interface dont on veut changer l'IP**
```
# show ip interface brief
OU
# show ip int br
```
**2. Passer en mode configuration d'interface**
```
# conf t
(config)# interface ethernet <NUMERO>
```
**3. Définir une IP**
```
(config-if)# ip address <IP> <MASK>
Exemple :
(config-if)# ip address 10.5.1.254 255.255.255.0
```
**4. Allumer l'interface**
```
(config-if)# no shut
```
**5. Vérifier l'IP**
```
(config-if)# exit
(config)# exit
# show ip int br
```
---

* [x] [Définition du nom de domaine](../../cours/procedures-cisco.md#changer-son-nom-de-domaine)

**1. Passer en mode configuration**
```
# conf t
```

**2. Changer le hostname**
```
(config)# hostname <HOSTNAME>
```

### Checklist routes 

On parle de toutes les machines :
* [x] `router1.tp5.b1`  
  * directement connecté à `net1` et `net12`  
  * [route à ajouter](../../cours/procedures-cisco.md#ajouter-une-route-statique) : `net2`  

`router1`:

```cisco
(config-if)# ip address 10.5.10.1 255.255.255.0
```

```cisco
(config-if)# hostname router1.tp5.b1
```


* [x] `router2.tp5.b1`
  * directement connecté à `net2` et `net12`  
  * [route à ajouter](../../cours/procedures-cisco.md#ajouter-une-route-statique) : `net1`  

`router2`:

```cisco
(config-if)# ip address 10.5.10.2 255.255.255.0
```

```cisco
(config-if)# hostname router2.tp5.b1
``` 

* [x] `server1.tp5.b1`  
  * directement connecté à `net1`  
  * [route à ajouter](../../cours/procedures.md#ajouter-une-route-statique) : `net2`
  * [fichiers `hosts`](../../cours/procedures.md#editer-le-fichier-hosts) à remplir : `client1.tp5.b1`, `client2.tp5.b1`

     ```bash
    [root@server1 ~]# ip route add 10.2.0.0/24 via 10.2.0.254 dev eth0
    ```
     ```bash
    [root@server1 ~]# nano /etc/sysconfig/network-scripts/route-eth0

    10.2.0.0/24 via 10.2.0.254 dev eth0
    ```

* [x] `client1.tp5.b1 && client2.tp5.b1`
  * directement connecté à `net2`  
  * [route à ajouter](../../cours/procedures.md#ajouter-une-route-statique) : `net1`
  * [fichiers `hosts`](../../cours/procedures.md#editer-le-fichier-hosts) à remplir : `server1.tp5.b1`, `client2.tp5.b1`

    Pour les clients j'ai tester en les pingant.




# III. DHCP

## 1. Mise en place du serveur DHCP

* [x] **1. [Renommer la machine](../../cours/procedures.md#changer-son-nom-de-domaine)**

```
echo 'dhcp-net2.tp5.b1' | sudo tee /etc/hostname
```

* [x] **2. Installer le serveur DHCP** en faisant un peu de crasse :

* [x] **3. Rallumer la VM dans GNS**

* [x] **4. Configuration du serveur DHCP**

* [x] **5. Démarrer le serveur DHCP**

* [x] **6. Faire un test**

```
dhclient -v eth0
Internet Systems Consortium DHCP Client 4.2.5
Copyright 2004-2013 Internet Systems Consortium.
All rights reserved.
For info, please visit https://www.isc.org/software/dhcp/

Listening on LPF/eth0/00:50:00:00:04:00
Sending on   LPF/eth0/00:50:00:00:04:00
Sending on   Socket/fallback
DHCPDISCOVER on eth0 to 255.255.255.255 port 67 interval 3 (xid=0x6ad2d7)
DHCPREQUEST on eth0 to 255.255.255.255 port 67 (xid=0x6ad2d7)
DHCPOFFER from 10.5.2.11
DHCPACK from 10.5.2.11 (xid=0x6ad2d7)
bound to 10.5.2.50 -- renewal in 250 seconds.
```

## 2. Explorer un peu DHCP

--- 

La discussion entre le client et le serveur DHCP se fait en 4 messages simples, **"DORA"** :
* **"Discover"** : du client vers le serveur
  * le client cherche un serveur DHCP en envoyant des Discover en broadcast
* **"Offer"** : du serveur vers le client
  * Si un serveur reçoit un "Discover" il peut répondre un "Offer" au client
  * Il propose une IP au client
* **"Request"** : du client vers le serveur
  * Permet de demander une IP au serveur
  * C'est celle que le serveur lui a proposé
* **"Acknowledge"** : du serveur vers le client
  * Le serveur attribue l'adresse IP au client
  * Il crée un bail DHCP en local
  * Il peut aussi fournir au client d'autres infos comme l'adresse de gateway

---

Nous allons d'abord effectuer `dhcpclient -v -r` pour récuperer les trames

