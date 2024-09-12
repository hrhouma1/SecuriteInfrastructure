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


# Annexe

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
