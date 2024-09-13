# Tutoriel : Configuration IPSec avec `netsh ipsec`

- Dans ce tutoriel, nous allons apprendre à configurer des règles IPSec à l'aide de la commande `netsh ipsec` pour Windows. 
- Nous verrons comment ajouter et afficher des politiques, des actions de filtrage, et des règles IPSec pour sécuriser les communications réseau.

### Prérequis :
- Système d'exploitation Windows (avec droits administratifs).
- Connexion réseau pour tester la configuration.
- Familiarité avec les concepts de base d'IPSec (Internet Protocol Security).

## 1. Afficher les politiques IPSec existantes

La première étape consiste à afficher les politiques IPSec déjà configurées sur le système et leur détails.


```bash
netsh ipsec static show policy all
```

### Exemple de sortie :
```
Politique 1 : IPSec Policy 1
    Filtre : Tout le trafic
    Action : Exiger une sécurité
```

## 2. Ajouter une règle IPSec

Ensuite, nous allons ajouter une règle IPSec qui s'applique au trafic HTTP en utilisant une politique IPSec nommée "Politique_IPSec_Basique" et une liste de filtres appelée "Tout_HTTP".

### Commande pour ajouter une règle :
```bash
netsh ipsec static add rule name="Regle_HTTP" policy="Politique_IPSec_Basique" filterlist="Tout_HTTP" filteraction="requireinrequireout"
```

### Explication :
- **name="Regle_HTTP"** : Le nom de la règle.
- **policy="Politique_IPSec_Basique"** : La politique à laquelle cette règle est appliquée.
- **filterlist="Tout_HTTP"** : La liste de filtres qui définit quel trafic est concerné (ici, le trafic HTTP).
- **filteraction="requireinrequireout"** : Action de filtrage qui spécifie qu'IPSec est requis à la fois pour les paquets entrants et sortants.

## 3. Modifier l'action de filtrage pour négocier IPSec

Dans cette étape, nous allons changer l'action de filtrage pour "négocier" au lieu de "requireinrequireout". La négociation permet aux deux parties de définir des paramètres de sécurité avant de sécuriser les communications.

### Commande pour modifier l'action de filtrage :
```bash
netsh ipsec static add rule name="Regle_HTTP" policy="Politique_IPSec_Basique" filterlist="Tout_HTTP" filteraction="negotiate"
```

### Explication :
- **filteraction="negotiate"** : Action de filtrage qui permet de négocier les paramètres IPSec avant de les appliquer.

## 4. Afficher les actions de filtrage

Pour vérifier les actions de filtrage existantes, utilisez cette commande :

```bash
netsh ipsec static show filteraction
```

Cela listera toutes les actions de filtrage IPSec configurées sur le système.

## 5. Ajouter une nouvelle action de filtrage

Si l'action de filtrage "requireinrequireout" n'existe pas encore, vous pouvez la créer en exécutant la commande suivante :

```bash
netsh ipsec static add filteraction name="requireinrequireout" action=negotiate
```

### Explication :
- **name="requireinrequireout"** : Le nom de l'action de filtrage.
- **action=negotiate** : Définir cette action pour négocier IPSec.

## 6. Ajouter une autre règle IPSec

Pour ajouter une nouvelle règle avec cette action de filtrage que vous avez créée, utilisez :

```bash
netsh ipsec static add rule name="Regle_HTTP" policy="Politique_IPSec_Basique" filterlist="Tout_HTTP" filteraction="requireinrequireout"
```

Cela applique la règle que nous avons configurée avec l'action de négociation IPSec.

## 7. Vérifier les configurations

Enfin, vous pouvez afficher toutes les politiques IPSec et leurs configurations actuelles à tout moment avec cette commande :

```bash
netsh ipsec static show policy
```

Pour obtenir des détails complets, utilisez :

```bash
netsh ipsec static show policy all
```

### Conclusion

Vous avez maintenant configuré une règle IPSec pour sécuriser le trafic HTTP en négociant ou en exigeant des communications sécurisées entre les machines. Les commandes `netsh ipsec` permettent une gestion fine des politiques IPSec pour garantir la confidentialité, l'authenticité et l'intégrité du trafic réseau.



```bash
netsh ipsec static show policy
netsh ipsec static add rule name="Regle_HTTP" policy="Politique_IPSec_Basique" filterlist="Tout_HTTP" filteraction="requireinrequireout"
netsh ipsec static add rule name="Regle_HTTP" policy="Politique_IPSec_Basique" filterlist="Tout_HTTP" filteraction="negotiate"
netsh ipsec static show filteraction
netsh ipsec static add filteraction name="requireinrequireout" action=negotiate
netsh ipsec static add rule name="Regle_HTTP" policy="Politique_IPSec_Basique" filterlist="Tout_HTTP" filteraction="negotiate"
netsh ipsec static add rule name="Regle_HTTP" policy="Politique_IPSec_Basique" filterlist="Tout_HTTP" filteraction="requireinrequireout"
netsh ipsec static show policy
netsh ipsec static show policy all
```
