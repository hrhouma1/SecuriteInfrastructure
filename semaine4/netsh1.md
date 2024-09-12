# Introduction à IPSec

**IPSec (Internet Protocol Security)** est un protocole essentiel pour sécuriser les communications sur un réseau en assurant l'authentification et le chiffrement des paquets IP. Utilisé principalement pour créer des connexions VPN et protéger les données en transit, IPSec permet de garantir que seuls les systèmes autorisés peuvent échanger des informations en toute sécurité.

IPSec utilise deux modes principaux :
1. **Mode Transport** : Chiffre uniquement les données du paquet IP.
2. **Mode Tunnel** : Chiffre l'intégralité du paquet IP, créant ainsi un "tunnel" sécurisé.

# Objectif de l'exercice

Dans cet exercice, nous allons :

1. Créer une nouvelle **politique IPSec** pour sécuriser le trafic HTTP.
2. Configurer une **action de filtre** personnalisée qui exige le chiffrement et l'authentification des données en entrée et en sortie.
3. **Créer une règle** associée à cette politique qui applique ces paramètres au trafic HTTP (port 80).
4. **Vérifier** la configuration pour s'assurer que tout fonctionne correctement.

# Étapes à suivre

# 1. Afficher les politiques et actions existantes

Avant de commencer, il est important de vérifier les politiques IPSec et actions de filtre déjà configurées sur le système :

```bash
# Afficher toutes les politiques IPSec existantes
netsh ipsec static show policy all

# Afficher toutes les actions de filtre existantes
netsh ipsec static show filteraction all
```

# 2. Créer une nouvelle action de filtre pour le chiffrement et l'authentification

Nous allons créer une action de filtre nommée **Chiffrer_et_Authentifier**, qui exigera une négociation IPSec en entrée et en sortie avec un chiffrement 3DES et une authentification SHA1 :

```bash
# Créer une nouvelle action de filtre avec chiffrement 3DES et SHA1 pour l'authentification
netsh ipsec static add filteraction name="Chiffrer_et_Authentifier" action=negotiate
```

# 3. Créer une politique IPSec pour sécuriser le trafic HTTP

Nous créons ensuite une nouvelle politique appelée **Trafic_Web_Sécurisé**. Cette politique sera utilisée pour sécuriser le trafic HTTP :

```bash
# Créer une politique IPSec pour sécuriser le trafic web
netsh ipsec static add policy name="Trafic_Web_Sécurisé"
```

# 4. Ajouter une règle pour sécuriser le trafic HTTP

Nous devons maintenant associer une règle à la politique créée. Cette règle va définir que le trafic HTTP (port 80) doit être sécurisé avec l'action de filtre que nous avons créée :

```bash
# Créer une liste de filtres pour le trafic HTTP
netsh ipsec static add filterlist name="Trafic_HTTP"
netsh ipsec static add filter filterlist="Trafic_HTTP" srcaddr=any dstaddr=any protocol=TCP dstport=80

# Associer la règle à la politique pour sécuriser le trafic HTTP
netsh ipsec static add rule name="Sécuriser_HTTP" policy="Trafic_Web_Sécurisé" filterlist="Trafic_HTTP" filteraction="Chiffrer_et_Authentifier"
```

# 5. Assigner la politique IPSec

Nous devons maintenant activer cette politique pour qu'elle soit appliquée sur le réseau :

```bash
# Assigner la politique pour l'appliquer
netsh ipsec static set policy name="Trafic_Web_Sécurisé" assign=y
```

# 6. Vérifier la configuration

Enfin, nous allons vérifier que la politique et l'action de filtre ont été correctement configurées :

```bash
# Vérifier toutes les politiques IPSec
netsh ipsec static show policy all

# Vérifier toutes les actions de filtre
netsh ipsec static show filteraction all
```

# Conclusion

Grâce à cet exercice, vous avez appris à créer une politique IPSec qui sécurise le trafic HTTP par le chiffrement et l'authentification. Vous savez également comment configurer des actions de filtre personnalisées et les associer à des politiques pour renforcer la sécurité réseau. IPSec est un outil puissant pour protéger les données en transit, en particulier dans des environnements sensibles où la confidentialité et l'intégrité des données sont essentielles.


# Annexe 1

```powershell
netsh ipsec static show policy all
netsh ipsec static show filteraction all
netsh ipsec static add filteraction name="Chiffrer_et_Authentifier" action=negotiate
netsh ipsec static add policy name="Trafic_Web_Sécurisé"
netsh ipsec static add filterlist name="Trafic_HTTP"
netsh ipsec static add filter filterlist="Trafic_HTTP" srcaddr=any dstaddr=any protocol=TCP dstport=80
netsh ipsec static add rule name="Sécuriser_HTTP" policy="Trafic_Web_Sécurisé" filterlist="Trafic_HTTP" filteraction="Chiffrer_et_Authentifier"
netsh ipsec static set policy name="Trafic_Web_Sécurisé" assign=y
netsh ipsec static show policy all
netsh ipsec static show filteraction all

```

# Annexe 2 - illustration ce que chaque commande fait dans le processus de configuration IPSec avec `netsh` :

```
+---------------------------------------------------------+
| Étape 1 : Afficher toutes les politiques existantes      |
| Commande : netsh ipsec static show policy all            |
|                                                         |
| Description : Vérifie toutes les politiques IPSec déjà   |
| configurées sur le système.                             |
+---------------------------------------------------------+

         |
         v

+---------------------------------------------------------+
| Étape 2 : Afficher toutes les actions de filtre existantes|
| Commande : netsh ipsec static show filteraction all      |
|                                                         |
| Description : Montre les actions de filtre disponibles. |
| Ces actions définissent comment IPSec traite les paquets|
| (négocier, autoriser, bloquer).                         |
+---------------------------------------------------------+

         |
         v

+---------------------------------------------------------+
| Étape 3 : Créer une nouvelle action de filtre            |
| Commande : netsh ipsec static add filteraction           |
|           name="Chiffrer_et_Authentifier" action=negotiate|
|                                                         |
| Description : Crée une action de filtre qui négocie le   |
| traitement des paquets, exigeant que ceux-ci soient      |
| chiffrés et authentifiés.                                |
+---------------------------------------------------------+

         |
         v

+---------------------------------------------------------+
| Étape 4 : Créer une nouvelle politique IPSec             |
| Commande : netsh ipsec static add policy name="Trafic_Web_Sécurisé" |
|                                                         |
| Description : Crée une politique nommée "Trafic_Web_Sécurisé" qui  |
| regroupe les règles de sécurité IPSec pour protéger le trafic web. |
+---------------------------------------------------------+

         |
         v

+---------------------------------------------------------+
| Étape 5 : Créer une liste de filtres pour le trafic HTTP |
| Commande : netsh ipsec static add filterlist             |
|           name="Trafic_HTTP"                            |
|                                                         |
| Description : Crée une liste de filtres pour spécifier  |
| que le trafic HTTP (port 80) doit être pris en compte.  |
+---------------------------------------------------------+

         |
         v

+---------------------------------------------------------+
| Étape 6 : Ajouter un filtre pour le trafic HTTP          |
| Commande : netsh ipsec static add filter                 |
|           filterlist="Trafic_HTTP" srcaddr=any           |
|           dstaddr=any protocol=TCP dstport=80            |
|                                                         |
| Description : Définit que le trafic HTTP (port 80) en    |
| provenance de n'importe quelle adresse source et         |
| destiné à n'importe quelle adresse de destination est    |
| concerné.                                                |
+---------------------------------------------------------+

         |
         v

+---------------------------------------------------------+
| Étape 7 : Ajouter une règle pour la politique            |
| Commande : netsh ipsec static add rule                   |
|           name="Sécuriser_HTTP"                          |
|           policy="Trafic_Web_Sécurisé"                   |
|           filterlist="Trafic_HTTP"                       |
|           filteraction="Chiffrer_et_Authentifier"        |
|                                                         |
| Description : Ajoute une règle à la politique qui dit    |
| que le trafic HTTP doit être sécurisé par le filtre      |
| "Chiffrer_et_Authentifier".                              |
+---------------------------------------------------------+

         |
         v

+---------------------------------------------------------+
| Étape 8 : Assigner la politique                         |
| Commande : netsh ipsec static set policy                 |
|           name="Trafic_Web_Sécurisé" assign=y            |
|                                                         |
| Description : Active la politique "Trafic_Web_Sécurisé"  |
| pour qu'elle soit utilisée sur le réseau.                |
+---------------------------------------------------------+

         |
         v

+---------------------------------------------------------+
| Étape 9 : Vérifier les politiques IPSec                  |
| Commande : netsh ipsec static show policy all            |
|                                                         |
| Description : Vérifie que la politique "Trafic_Web_Sécurisé" |
| a bien été configurée et activée.                       |
+---------------------------------------------------------+

         |
         v

+---------------------------------------------------------+
| Étape 10 : Vérifier les actions de filtre                |
| Commande : netsh ipsec static show filteraction all      |
|                                                         |
| Description : Vérifie que l'action de filtre             |
| "Chiffrer_et_Authentifier" est bien configurée.          |
+---------------------------------------------------------+
```

### Explications simplifiées :
- **"Negotiate"** : Cela signifie que IPSec essaie de négocier une connexion sécurisée entre les deux points (chiffrement et authentification).
- **Action de filtre** : Détermine comment IPSec traite les paquets réseau (chiffrement, authentification, etc.).
- **Politique** : Un ensemble de règles IPSec qui définissent comment le trafic doit être sécurisé.
- **Filtre** : Définit quel type de trafic est concerné (ici, le trafic HTTP sur le port 80).

Chaque étape construit progressivement une configuration IPSec pour sécuriser le trafic HTTP en appliquant une politique spécifique.
