# Documentation technique du projet

Ce document détaille les choix de conception, le plan d'adressage, les configurations appliquées et le protocole de validation de l'infrastructure décrite dans le [README](./README.md).

## 1\. Contexte et choix techniques

### 1.1 Environnement de simulation

La topologie est simulée sous **GNS3** (émulation de routeurs Cisco via Dynamips). Les deux postes finaux — le client du département Client et le serveur NFS du département Serveur — sont des machines virtuelles **VirtualBox** réelles, intégrées à la topologie GNS3 via des nœuds **Cloud**, qui font le pont entre le réseau simulé et des interfaces réseau *Host-Only* dédiées.

Ce choix a été retenu après un premier essai avec un nœud QEMU (image TinyCore Linux), abandonné en raison d'une incompatibilité d'accélération matérielle (HAXM) sur la machine hôte. VirtualBox a permis d'obtenir un poste client Linux complet, capable d'exécuter des commandes non disponibles sur un nœud VPCS standard (`mount`, `ip route`, etc.), condition nécessaire pour consommer réellement le service NFS.

### 1.2 Intégration GNS3 ↔ VirtualBox

* **Cloud1** relie l'interface Host-Only du serveur à **RS**
* **Cloud2** relie l'interface Host-Only du client à **RC**

Chaque VM dispose de deux interfaces réseau : une interface NAT pour l'accès Internet et l'administration, et une interface Host-Only intégrée à la topologie simulée.

## 2\. Plan d'adressage VLSM

Bloc alloué : `192.168.0.0/17` (32 766 adresses).

|Segment|Besoin|Masque|Taille|
|-|-|-|-|
|LAN Client (RC)|1790 hôtes|/21|2048|
|LAN Serveur (RS)|112 hôtes|/25|128|
|Liens point-à-point (×6)|2 hôtes|/30|4|

### Table d'allocation

|Segment|Réseau|Masque|Plage utilisable|
|-|-|-|-|
|LAN Client|192.168.0.0|/21|.0.1 – .7.254|
|LAN Serveur|192.168.8.0|/25|.8.1 – .8.126|
|R1–R2|192.168.8.128|/30|.129 – .130|
|R1–R3|192.168.8.132|/30|.133 – .134|
|R2–R3|192.168.8.136|/30|.137 – .138|
|R1–R-Internet|192.168.8.140|/30|.141 – .142|
|R2–RC|192.168.8.144|/30|.145 – .146|
|R3–RS|192.168.8.148|/30|.149 – .150|

## 3\. Configuration des équipements

Les configurations complètes de chaque équipement (RC, R1, R2, R3, RS, R-Internet) sont disponibles dans le dossier [`configs/`](./configs). Elles couvrent :

* L'adressage IP des interfaces
* L'activation d'OSPF (aire 0) sur les liens backbone et LAN
* La configuration DHCP (RC et RS) avec exclusion des adresses réservées aux équipements
* Le NAT/PAT en sortie de chaque département (`ip nat inside/outside` + liste d'accès de traduction)
* Une ACL de sécurité (`PROTECT-SERVEUR`) sur RS, limitant l'accès au serveur NFS aux flux ICMP, NFS (port 2049) et RPC (portmapper), et autorisant OSPF

Côté machines virtuelles, l'adressage réseau (DHCP côté client, IP fixe côté serveur) est configuré via **Netplan** pour être conservé après redémarrage.

## 4\. Protocole de validation

|Étape|Commande / action|Preuve attendue|
|-|-|-|
|1|`show ip interface brief`|Toutes les interfaces utilisées en up/up|
|2|`show ip ospf neighbor`|Voisinage OSPF en FULL sur tous les routeurs|
|3|`show ip route ospf`|Routes apprises dynamiquement (marquées O)|
|4|`ip addr show` (client)|Adresse DHCP obtenue automatiquement|
|5|`ping` + `show ip nat translations`|Traduction NAT effective|
|6|`mount` NFS + écriture/lecture croisée|Fichier transféré de bout en bout|
|7|`show access-lists PROTECT-SERVEUR`|Compteurs non nuls sur les règles autorisées|

*Ce document complète le* [*README*](./README.md) *du projet.*

