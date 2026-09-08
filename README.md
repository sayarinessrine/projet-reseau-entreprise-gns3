# Infrastructure Réseau d'Entreprise — Projet Académique

**Auteur :** Nessrine Sayari — Étudiante ingénieure, ESPRIT Tunis 

## Objectif

Simuler l'infrastructure réseau d'une entreprise avec architecture Client-Serveur, incluant :
- Backbone OSPF maillé (routage dynamique)
- Adressage VLSM optimisé
- Distribution automatique d'adresses (DHCP)
- Traduction d'adresses (NAT/PAT)
- Service de partage de fichiers (NFS) réellement consommé par un client
- Sécurisation par ACL

## Architecture

- **Backbone** : 3 routeurs (R1, R2, R3) en topologie maillée, routage OSPF.
- **Département Client** : routeur RC + PC consommateur du service.
- **Département Serveur** : routeur RS + serveur NFS (Ubuntu Server).
- **R-Internet** : routeur simulant la passerelle vers Internet.

## Outils utilisés

- **GNS3** — simulation de routeurs Cisco (émulation IOS via Dynamips)
- **VirtualBox** — hébergement des machines Linux client/serveur
- **Ubuntu Server** — serveur NFS

## Compétences démontrées

- Conception et calcul d'adressage IP (VLSM)
- Configuration de routage dynamique OSPF
- Configuration NAT/PAT et DHCP sur équipements Cisco
- Déploiement et sécurisation d'un service réseau (NFS)
- Diagnostic réseau (analyse de tables de routage, ARP, OSPF, débogage de connectivité)
- Intégration d'un environnement de simulation réseau (GNS3) avec des machines virtuelles réelles (VirtualBox)

## Documentation complète

Voir le fichier [Explication-Complete-Projet-TechSolutions.docx](./Explication-Complete-Projet-TechSolutions.docx) pour le détail complet des configurations et des tests.

## Contexte

Projet réalisé dans le cadre de mon cycle d'ingénieur à ESPRIT Tunis .
