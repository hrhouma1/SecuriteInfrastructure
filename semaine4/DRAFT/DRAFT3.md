# Introduction à IPSec

**IPSec (Internet Protocol Security)** est un protocole utilisé pour sécuriser les communications sur un réseau, principalement via le chiffrement et l'authentification des paquets IP. Il est couramment utilisé pour créer des réseaux privés virtuels (VPNs) et garantir que les données transmises entre différents points du réseau sont protégées contre les interceptions ou les altérations. IPSec offre deux principales fonctions :
1. **Authentification** : Garantit que les paquets de données proviennent de sources fiables.
2. **Chiffrement** : Protège la confidentialité des données en les rendant illisibles pour des tiers non autorisés.

IPSec fonctionne en mode **Transport** (chiffre uniquement la charge utile des paquets) ou en mode **Tunnel** (chiffre l'ensemble des paquets IP), selon les besoins de sécurité du réseau.

### Exercice pratique

Dans l'exercice suivant, nous allons apprendre à configurer et gérer des politiques IPSec à l'aide de la commande `netsh`. Voici ce que nous allons accomplir :

1. **Afficher les politiques IPSec existantes** : Nous commencerons par examiner les politiques IPSec déjà configurées sur le système pour comprendre la configuration actuelle.
   - Commande : `netsh ipsec static show policy all`

2. **Examiner les actions de filtre** : Nous allons ensuite vérifier les actions de filtre disponibles, qui définissent comment les paquets IP doivent être traités (autorisés, bloqués, ou négociés avec IPSec).
   - Commande : `netsh ipsec static show filteraction all`

3. **Ajouter une nouvelle action de filtre** : Ensuite, nous créerons une nouvelle action de filtre appelée **requireinrequireout**, qui exigera une négociation IPSec à la fois en entrée et en sortie pour sécuriser les paquets.
   - Commande : `netsh ipsec static add filteraction name="requireinrequireout" action=negotiate`

4. **Appliquer une politique IPSec pour le trafic HTTP** : Nous configurerons une règle pour sécuriser le trafic HTTP en appliquant la politique IPSec et en utilisant la négociation IPSec.
   - Commande : `netsh ipsec static add rule name="Regle_HTTP" policy="Politique_IPSec_Basique" filterlist="Tout_HTTP" filteraction="negotiate"`

5. **Renforcer la sécurité de la règle HTTP** : Enfin, nous modifierons cette règle pour exiger que tout le trafic HTTP soit sécurisé à la fois en entrée et en sortie avec IPSec.
   - Commande : `netsh ipsec static add rule name="Regle_HTTP" policy="Politique_IPSec_Basique" filterlist="Tout_HTTP" filteraction="requireinrequireout"`


---
# Correction
---

```bash
# Afficher toutes les politiques IPSec
netsh ipsec static show policy all

# Afficher toutes les actions de filtre
netsh ipsec static show filteraction all

# Ajouter une nouvelle action de filtre pour exiger la négociation en entrée et en sortie
netsh ipsec static add filteraction name="requireinrequireout" action=negotiate

# Ajouter une règle pour appliquer la politique IPSec au trafic HTTP avec négociation
netsh ipsec static add rule name="Regle_HTTP" policy="Politique_IPSec_Basique" filterlist="Tout_HTTP" filteraction="negotiate"

# Mettre à jour la règle HTTP pour imposer la négociation IPSec en entrée et en sortie
netsh ipsec static add rule name="Regle_HTTP" policy="Politique_IPSec_Basique" filterlist="Tout_HTTP" filteraction="requireinrequireout"

# Vérifier toutes les politiques IPSec
netsh ipsec static show policy all

# Vérifier toutes les actions de filtre
netsh ipsec static show filteraction all
```

# Explications :

1. **`netsh ipsec static show policy all`** : Cette commande affiche toutes les politiques IPSec configurées sur le système. Les politiques IPSec définissent comment les connexions réseau doivent être sécurisées, incluant les règles de filtrage et les actions à appliquer.

2. **`netsh ipsec static show filteraction all`** : Cette commande montre toutes les actions de filtre configurées. Les actions de filtre déterminent comment gérer les paquets réseau (les autoriser, les bloquer ou les négocier avec IPSec).

3. **`netsh ipsec static add filteraction name="requireinrequireout" action=negotiate`** : Ici, une action de filtre nommée `requireinrequireout` est créée, spécifiant que tout le trafic entrant et sortant doit être négocié avec IPSec, assurant ainsi une sécurité des paquets par chiffrement.

4. **`netsh ipsec static add rule name="Regle_HTTP" policy="Politique_IPSec_Basique" filterlist="Tout_HTTP" filteraction="negotiate"`** : Cette commande ajoute une règle qui applique une politique IPSec nommée `Politique_IPSec_Basique` au trafic HTTP (via le filtre `Tout_HTTP`), en utilisant la négociation pour la sécurisation des paquets réseau.

5. **`netsh ipsec static add rule name="Regle_HTTP" policy="Politique_IPSec_Basique" filterlist="Tout_HTTP" filteraction="requireinrequireout"`** : Ici, la règle HTTP est mise à jour pour exiger la négociation IPSec aussi bien en entrée qu'en sortie, assurant que toutes les communications HTTP passent par la sécurisation IPSec.

6. **`netsh ipsec static show policy all`** (répétée) : Utilisée à nouveau pour vérifier que toutes les politiques IPSec sont correctement configurées.

7. **`netsh ipsec static show filteraction all`** (répétée) : Permet de vérifier les actions de filtre ajoutées ou modifiées.

Ces commandes sont utilisées pour configurer et gérer des politiques de sécurité IPSec, qui permettent de sécuriser les communications réseau en négociant et en chiffrant le trafic, en fonction des règles et actions définies.





---
# Exercice 1  : 
---

- Proposez une configuration pour créer une politique qui sécurise le trafic HTTP avec le chiffrement et l'authentification IPsec.
- Respectez les éléments suivants :

1. Utilisation de noms plus descriptifs pour les politiques, listes de filtres et actions.
2. Ajout d'une liste de filtres spécifique pour le trafic HTTP sur le port 80.
3. Création d'une action de filtrage personnalisée "Chiffrer_et_Authentifier" qui exige une sécurité entrante et sortante, utilisant le chiffrement 3DES et SHA1 pour l'authentification.
4. Ajout de commandes pour créer la politique et l'assigner.
5. Inclusion d'étapes de vérification à la fin pour afficher les politiques et actions de filtrage configurées.



---
# Correction de l'exercice 1
---




```powershell
# Afficher les politiques et actions de filtrage existantes
netsh ipsec static show policy name=all
netsh ipsec static show filteraction name=all

# Créer une nouvelle action de filtrage pour le chiffrement et l'authentification
netsh ipsec static add filteraction name="Chiffrer_et_Authentifier" action=requireinrequireout qmsecmethods=esp:3des-sha1 

# Créer une politique
netsh ipsec static add policy name="Trafic_Web_Sécurisé"

# Créer une liste de filtres pour le trafic HTTP
netsh ipsec static add filterlist name="Trafic_HTTP"
netsh ipsec static add filter filterlist="Trafic_HTTP" srcaddr=any dstaddr=any protocol=TCP dstport=80

# Ajouter une règle à la politique
netsh ipsec static add rule name="Sécuriser_HTTP" policy="Trafic_Web_Sécurisé" filterlist="Trafic_HTTP" filteraction="Chiffrer_et_Authentifier"

# Assigner la politique
netsh ipsec static set policy name="Trafic_Web_Sécurisé" assign=y

# Vérifier la configuration
netsh ipsec static show policy name=all
netsh ipsec static show filteraction name=all
```

### Conclusion

- Cet exercice vous permet de comprendre comment configurer des politiques et des actions de filtre IPSec pour sécuriser des types spécifiques de trafic, comme HTTP, sur un réseau. 
- Grâce à ces configurations, vous apprendrez à renforcer la sécurité de vos communications réseau et à personnaliser la manière dont IPSec négocie et protège les paquets de données.



# Annexe

```powershell
netsh ipsec static show policy all
netsh ipsec static show filteraction all
netsh ipsec static add filteraction name="requireinrequireout" action=negotiate
netsh ipsec static add rule name="Regle_HTTP" policy="Politique_IPSec_Basique" filterlist="Tout_HTTP" filteraction="negotiate"
netsh ipsec static add rule name="Regle_HTTP" policy="Politique_IPSec_Basique" filterlist="Tout_HTTP" filteraction="requireinrequireout"
netsh ipsec static show policy all
netsh ipsec static show filteraction all
netsh ipsec static add rule name="Regle_HTTP" policy="Politique_IPSec_Basique" filterlist="Tout_HTTP" filteraction="requireinrequireout"
netsh ipsec static add rule name="Regle_HTTP" policy="Politique_IPSec_Basique" filterlist="Tout_HTTP" filteraction="negotiate"
netsh ipsec static show filteraction all
```
