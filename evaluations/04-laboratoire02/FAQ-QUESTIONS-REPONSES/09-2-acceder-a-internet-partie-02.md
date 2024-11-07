# Objectif : 

- permettre aux machines de se connecter à Internet en utilisant un routeur classique.

---

## Scénario 1 : Cluster NLB avec Deux Cartes Réseau (Gestion et NLB)

Dans ce scénario, chaque machine possède deux cartes réseau :
- **Carte Réseau 1** : Utilisée pour la gestion (VLAN 100).
- **Carte Réseau 2** : Utilisée pour le trafic NLB (VLAN 200).

**Objectif** : Seules certaines machines, comme le contrôleur de domaine et le client WinCLI, ont besoin d'un accès Internet pour des tests, tandis que les serveurs du cluster n'ont pas d'accès direct à Internet.

### Configuration d'Accès Internet

1. **Configurer le Routeur pour le VLAN de Gestion (VLAN 100)** :
   - Connectez le routeur à VLAN 100, ce qui permettra aux machines du réseau de gestion d'accéder à Internet via ce VLAN.
   - Assurez-vous que le routeur dispose d'une connexion Internet fonctionnelle (câblée ou en bridge avec l'hôte VMware).
   - Configurez le **NAT (Network Address Translation)** sur le routeur pour permettre aux machines de masquer leurs adresses IP internes.

2. **Configurer le Routage Inter-VLAN si Nécessaire** :
   - Si vous souhaitez que certaines machines du VLAN 200 (NLB) accèdent temporairement à Internet, vous pouvez activer le routage inter-VLAN entre VLAN 100 et VLAN 200 sur le routeur. Cependant, pour limiter l'accès Internet au strict nécessaire, il est recommandé de ne pas activer le routage inter-VLAN vers VLAN 200.

3. **Configurer les Adresses IP et la Passerelle** :
   - Assignez une **adresse IP statique** au contrôleur de domaine et au client dans le VLAN 100.
   - Configurez la **passerelle par défaut** sur ces machines pour qu'elle pointe vers l'adresse IP du routeur.

### Résumé de Connexion pour le Scénario 1

Seul le VLAN de gestion (VLAN 100) aura accès à Internet via le routeur. Les serveurs du VLAN 200 (NLB) n'ont pas d'accès direct à Internet, ce qui renforce la sécurité en limitant les connexions externes.

---

## Scénario 2 : Cluster NLB avec Trois Cartes Réseau (Gestion, NLB et Internet)

Dans ce scénario, chaque machine possède trois cartes réseau :
- **Carte Réseau 1** : Utilisée pour la gestion (VLAN 100).
- **Carte Réseau 2** : Utilisée pour le trafic NLB (VLAN 200).
- **Carte Réseau 3** : Connexion en mode Bridge pour accéder à Internet.

**Objectif** : Permettre à chaque serveur de se connecter directement à Internet via le routeur.

### Configuration d'Accès Internet

1. **Configurer le Routeur pour le Réseau Bridge** :
   - Connectez le routeur au réseau de l’hôte en mode **Bridge**, ce qui permettra aux machines ayant une carte réseau configurée en Bridge de recevoir une adresse IP externe.
   - Activez le NAT sur le routeur pour masquer les adresses internes lors de la connexion à Internet.

2. **Configurer la Carte Réseau 3 (Internet - Bridge) pour Chaque Machine** :
   - Assignez une adresse IP obtenue via DHCP à partir du routeur pour chaque machine connectée en mode Bridge.
   - Configurez la **passerelle par défaut** de la carte en Bridge sur chaque serveur pour qu'elle pointe vers le routeur.

3. **Isoler le Trafic Interne (VLAN 100 et VLAN 200)** :
   - Le routeur doit uniquement servir de passerelle pour la carte réseau en Bridge (Internet), sans interférer avec les VLANs internes.
   - Cela permet aux machines de continuer à fonctionner en interne sur leurs VLANs respectifs pour la gestion et le NLB, tout en ayant accès à Internet via la carte réseau en mode Bridge.

### Résumé de Connexion pour le Scénario 2

Dans ce scénario, chaque serveur du cluster aura un accès direct à Internet via la carte réseau en mode Bridge. Cela facilite les mises à jour et les tests nécessitant une connexion Internet, mais introduit une surface d’exposition plus importante à Internet pour chaque serveur.

---

## Scénario 3 : Cluster NLB avec un Seul VLAN (Segment de Réseau Unique)

Dans ce scénario, toutes les machines sont connectées sur un seul VLAN (VLAN 100). Une seule carte réseau par machine est utilisée pour la gestion, le trafic NLB et l'accès à Internet.

**Objectif** : Fournir une connexion Internet via le routeur pour un réseau simplifié dans un seul VLAN.

### Configuration d'Accès Internet

1. **Configurer le Routeur pour le VLAN Unique (VLAN 100)** :
   - Connectez le routeur au VLAN 100, ce qui lui permettra de servir de passerelle pour toutes les machines.
   - Assurez-vous que le routeur est configuré avec une connexion Internet (directement ou via le réseau de l'hôte en mode Bridge).

2. **Configurer le NAT et la Passerelle** :
   - Activez le NAT sur le routeur pour permettre aux machines du VLAN unique de masquer leurs adresses IP lors de la connexion à Internet.
   - Configurez l’adresse IP du routeur en tant que **passerelle par défaut** pour toutes les machines du VLAN 100.

3. **Configurer les Adresses IP des Machines** :
   - Chaque machine doit avoir une adresse IP statique dans le même sous-réseau (192.168.0.x/24).
   - Configurez la passerelle par défaut pour chaque machine pour qu'elle pointe vers l'adresse IP du routeur dans le VLAN 100.

### Résumé de Connexion pour le Scénario 3

Dans ce scénario, toutes les machines connectées au VLAN unique peuvent accéder à Internet via le routeur. Cela simplifie la configuration, mais tous les types de trafic (gestion, NLB et Internet) partagent le même segment, ce qui peut poser des risques de sécurité.

---

## Comparaison de l'Accès Internet dans les Trois Scénarios

| Scénario                               | Accès Internet | Avantages                                              | Inconvénients                                   |
|----------------------------------------|----------------|--------------------------------------------------------|------------------------------------------------|
| **Scénario 1 : Deux Cartes Réseau**    | Limité au VLAN de gestion | Sécurisé, limite l'exposition à Internet               | Serveurs du VLAN NLB n'ont pas d'accès direct   |
| **Scénario 2 : Trois Cartes Réseau**   | Accès direct via Bridge | Permet un accès Internet direct pour chaque serveur     | Plus complexe, exposition de chaque serveur à Internet |
| **Scénario 3 : Un seul VLAN**          | Accès via VLAN unique | Simple, toutes les machines peuvent accéder à Internet | Moins sécurisé, tout le trafic est sur un seul VLAN |

---

## Conclusion

Le choix du scénario dépend de vos besoins en matière de sécurité et d'accès à Internet :

- **Scénario 1** convient si vous souhaitez limiter l'exposition à Internet tout en conservant un accès pour les tests sur certaines machines.
- **Scénario 2** est idéal si chaque serveur doit accéder à Internet pour des mises à jour et des tests, mais il introduit plus de complexité et de risques de sécurité.
- **Scénario 3** est le plus simple pour un environnement de laboratoire, mais il n’offre pas de séparation des trafics et est moins sécurisé pour les environnements de production.

Assurez-vous que chaque machine dispose des configurations de passerelle et de DNS appropriées pour utiliser le routeur comme point d'accès à Internet.
