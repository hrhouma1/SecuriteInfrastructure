# **1. Introduction au protocole IPSec**

IPSec est un protocole de sécurité utilisé pour protéger les données qui circulent sur un réseau. Il est souvent utilisé pour **chiffrer** les communications entre deux machines, par exemple lors d’une connexion VPN. IPSec fonctionne avec deux modes :
- **Mode Transport** : Chiffre seulement les données à l'intérieur du paquet (pas les en-têtes IP).
- **Mode Tunnel** : Chiffre l’intégralité du paquet IP (données + en-têtes), ce qui est utile pour les connexions VPN.

# **2. Implémentation de politiques de sécurité IPSec**

Pour sécuriser les communications sur un réseau, vous allez configurer des **politiques IPSec** qui définissent quelles connexions doivent être chiffrées. Voici les étapes détaillées pour configurer IPSec sur une machine Windows en utilisant des commandes **Netsh**.

#### Étape 1 : Ouvrir une invite de commandes en tant qu'administrateur

1. Allez dans la barre de recherche de Windows et tapez **cmd**.
2. Faites un clic droit sur **Invite de commandes** et choisissez **Exécuter en tant qu'administrateur**.

#### Étape 2 : Créer une politique IPSec

Vous allez créer une politique IPSec de base qui chiffre le trafic entre votre machine et une autre machine sur le réseau.

Tapez la commande suivante pour créer une politique de sécurité IPSec :

```bash
netsh ipsec static add policy name="Politique_IPSec_Basique" description="Politique de chiffrement IPSec pour sécuriser le trafic"
```

Cette commande crée une politique appelée "Politique_IPSec_Basique". Vous pouvez remplacer ce nom par ce que vous voulez, mais assurez-vous que ce nom soit cohérent dans toutes les étapes suivantes.

#### Étape 3 : Ajouter une règle IPSec à la politique

Maintenant, nous devons ajouter une règle à cette politique pour définir quel type de trafic doit être chiffré. Pour cet exemple, nous allons sécuriser le **trafic HTTP** (port 80).

Tapez la commande suivante pour ajouter une règle :

```bash
netsh ipsec static add rule name="Regle_HTTP" policy="Politique_IPSec_Basique" filterlist="Tout_HTTP" filteraction="requireinrequireout"
```

- `name="Regle_HTTP"` : Nom de la règle, vous pouvez le changer si nécessaire.
- `filterlist="Tout_HTTP"` : Liste des filtres (nous allons la définir dans l'étape suivante).
- `filteraction="requireinrequireout"` : Cette action exige que tout le trafic entrant et sortant soit chiffré.

#### Étape 4 : Définir la liste des filtres

Nous allons maintenant définir la **liste des filtres** qui correspond au trafic que nous voulons sécuriser (le trafic HTTP).

Tapez la commande suivante pour ajouter une liste de filtres :

```bash
netsh ipsec static add filterlist name="Tout_HTTP"
netsh ipsec static add filter filterlist="Tout_HTTP" srcaddr=Me dstaddr=Any protocol=TCP srcport=80 dstport=80
```

- `srcaddr=Me` : La source est votre machine.
- `dstaddr=Any` : La destination peut être n’importe quelle machine.
- `srcport=80` et `dstport=80` : Nous voulons sécuriser le trafic HTTP (port 80).

#### Étape 5 : Activer la politique IPSec

Enfin, pour appliquer la politique IPSec, vous devez activer cette politique :

```bash
netsh ipsec static set policy name="Politique_IPSec_Basique" assign=yes
```

Cela active immédiatement la politique sur votre machine. À ce stade, tout le trafic HTTP sera chiffré selon la règle que vous avez définie.

# **3. Intégration d’IPSec avec Active Directory (AD DS)**

Si vous gérez plusieurs machines dans un domaine Active Directory, vous pouvez déployer les règles IPSec à l'aide des **Stratégies de Groupe**. Cela signifie que toutes les machines du domaine appliqueront automatiquement les politiques IPSec sans que vous ayez à les configurer individuellement.

#### Étape 1 : Ouvrir l'éditeur de gestion des stratégies de groupe

1. Appuyez sur **Windows + R**, tapez **gpmc.msc**, puis appuyez sur **Entrée** pour ouvrir la console de gestion des stratégies de groupe.
2. Dans la console, cliquez droit sur **Stratégies de Groupe** dans votre domaine, puis sélectionnez **Créer une GPO dans ce domaine et la lier ici...**.
3. Donnez un nom à votre nouvelle stratégie, par exemple **Politique_IPSec_Domaine**.

#### Étape 2 : Configurer la règle IPSec dans la GPO

1. Cliquez droit sur votre GPO et sélectionnez **Modifier**.
2. Allez dans **Configuration ordinateur > Paramètres Windows > Paramètres de sécurité > Stratégies IPSec**.
3. Cliquez droit dans le volet droit, puis sélectionnez **Créer une nouvelle règle**.
4. Suivez l'assistant pour définir vos filtres et actions (comme dans les étapes ci-dessus), en spécifiant le type de trafic que vous souhaitez chiffrer (HTTP, HTTPS, etc.).

Une fois que vous avez terminé, cette GPO sera appliquée à toutes les machines du domaine Active Directory.

# **4. Dépannage des stratégies IPSec dans un environnement AD**

Si vous rencontrez des problèmes avec IPSec, vous pouvez utiliser quelques outils pour vérifier si les politiques sont correctement appliquées.

#### Vérification avec Netsh

Tapez la commande suivante pour afficher la politique IPSec active :

```bash
netsh ipsec static show policy
```

Cette commande vous montrera toutes les politiques IPSec en cours d'application sur la machine. Si vous ne voyez pas la politique que vous avez créée, cela signifie qu’elle n’est pas appliquée.

#### Capture de paquets avec Wireshark

1. Téléchargez et installez **Wireshark** (un outil de capture de paquets).
2. Lancez Wireshark et commencez une capture sur l'interface réseau utilisée pour la communication.
3. Filtrez les paquets en tapant `ip.src == <votre_ip>` dans la barre de filtre.

Si les paquets capturés sont chiffrés, vous verrez des paquets ESP (Encapsulating Security Payload), ce qui signifie que IPSec fonctionne correctement.

Si vous voyez du trafic en clair, c’est que la règle IPSec n’est pas appliquée correctement.

---
---
# Version POWER-SHELL
----


# **Étape 1 : Ouvrir PowerShell en tant qu’administrateur**

1. Appuyez sur **Windows + X** et sélectionnez **Windows PowerShell (Admin)**.
2. Confirmez avec **Oui** pour ouvrir PowerShell en tant qu'administrateur.

# **Étape 2 : Créer une politique IPSec**

La première étape est de définir une nouvelle politique IPSec. Voici comment créer une nouvelle politique appelée `Politique_IPSec_PowerShell` en PowerShell :

```powershell
New-NetIPsecPolicy -DisplayName "Politique_IPSec_PowerShell" -Description "Politique IPSec via PowerShell pour sécuriser le trafic HTTP"
```

Cette commande crée une nouvelle politique avec le nom "Politique_IPSec_PowerShell". Vous pouvez changer ce nom, mais assurez-vous de l’utiliser dans les étapes suivantes.

# **Étape 3 : Définir une règle pour sécuriser le trafic HTTP**

Nous allons maintenant créer une règle pour sécuriser le trafic HTTP (port 80). Vous pouvez personnaliser cette règle pour d'autres types de trafic si nécessaire.

#### Étape 3.1 : Créer un filtre pour le trafic HTTP

```powershell
$filter = New-NetIPsecTrafficFilter -DisplayName "Filtre_HTTP" -LocalPort 80 -Protocol TCP -Direction Inbound -Action RequireEncryption
```

Cette commande crée un filtre pour sécuriser le trafic entrant (Inbound) sur le port 80 en exigeant le chiffrement (`RequireEncryption`).

#### Étape 3.2 : Appliquer le filtre à la politique IPSec

Ensuite, nous associons ce filtre à la politique IPSec que nous avons créée précédemment.

```powershell
Set-NetIPsecRule -DisplayName "Regle_HTTP" -InboundSecurity Require -PolicyStore ActiveStore -TrafficFilter $filter
```

Cela crée une règle IPSec qui applique le chiffrement pour tout le trafic entrant sur le port 80, selon le filtre que nous avons défini.

# **Étape 4 : Créer une règle pour le trafic sortant (Outbound)**

Si vous voulez sécuriser également le trafic sortant, vous pouvez créer une autre règle pour cela :

```powershell
$filterOutbound = New-NetIPsecTrafficFilter -DisplayName "Filtre_HTTP_Outbound" -RemotePort 80 -Protocol TCP -Direction Outbound -Action RequireEncryption
Set-NetIPsecRule -DisplayName "Regle_HTTP_Outbound" -OutboundSecurity Require -PolicyStore ActiveStore -TrafficFilter $filterOutbound
```

# **Étape 5 : Activer la politique IPSec**

Pour activer la politique IPSec que vous venez de configurer, utilisez la commande suivante :

```powershell
Enable-NetIPsecRule -DisplayName "Regle_HTTP"
```

Cette commande active la règle que nous avons créée pour le trafic HTTP.

# **Étape 6 : Vérifier la configuration**

Pour vérifier que la règle IPSec est correctement appliquée, vous pouvez utiliser la commande suivante :

```powershell
Get-NetIPsecRule
```

Cela affichera toutes les règles IPSec configurées et leur statut. Vous devriez voir la règle que vous venez de créer.

# **Étape 7 : Supprimer une règle IPSec (si nécessaire)**

Si vous voulez supprimer une règle IPSec, vous pouvez utiliser cette commande :

```powershell
Remove-NetIPsecRule -DisplayName "Regle_HTTP"
```

Cela supprimera la règle IPSec avec le nom `Regle_HTTP`. Vous pouvez remplacer "Regle_HTTP" par le nom de la règle que vous souhaitez supprimer.

---

# **Résumé des commandes PowerShell :**

1. **Créer une politique IPSec :**

   ```powershell
   New-NetIPsecPolicy -DisplayName "Politique_IPSec_PowerShell" -Description "Politique IPSec via PowerShell pour sécuriser le trafic HTTP"
   ```

2. **Créer un filtre pour le trafic HTTP :**

   ```powershell
   $filter = New-NetIPsecTrafficFilter -DisplayName "Filtre_HTTP" -LocalPort 80 -Protocol TCP -Direction Inbound -Action RequireEncryption
   ```

3. **Appliquer le filtre à la politique :**

   ```powershell
   Set-NetIPsecRule -DisplayName "Regle_HTTP" -InboundSecurity Require -PolicyStore ActiveStore -TrafficFilter $filter
   ```

4. **Créer un filtre pour le trafic sortant (Outbound) :**

   ```powershell
   $filterOutbound = New-NetIPsecTrafficFilter -DisplayName "Filtre_HTTP_Outbound" -RemotePort 80 -Protocol TCP -Direction Outbound -Action RequireEncryption
   Set-NetIPsecRule -DisplayName "Regle_HTTP_Outbound" -OutboundSecurity Require -PolicyStore ActiveStore -TrafficFilter $filterOutbound
   ```

5. **Activer la règle IPSec :**

   ```powershell
   Enable-NetIPsecRule -DisplayName "Regle_HTTP"
   ```

# Annexe 1 :


1. Vérifiez si le module NetSecurity est disponible :

```powershell
Get-Module -ListAvailable | Where-Object { $_.Name -eq "NetSecurity" }
```

2. Si le module est présent, essayez de l'importer :

```powershell
Import-Module NetSecurity
```

3. Si le module n'est pas disponible ou si l'importation échoue, vous utilisez probablement une version de Windows qui ne prend pas en charge ce module (comme Windows Server 2008 R2).

Dans ce cas, vous pouvez utiliser la commande netsh comme alternative pour créer une politique IPSec :

```
netsh ipsec static add policy name="Politique_IPSec_PowerShell" description="Politique IPSec via PowerShell pour sécuriser le trafic HTTP"
```

4. Ensuite, ajoutez une règle à cette politique :

```
netsh ipsec static add rule name="Regle_HTTP" policy="Politique_IPSec_PowerShell" filterlist="Liste_HTTP" filteraction="Chiffrer"
```

5. Définissez la liste de filtres :

```
netsh ipsec static add filterlist name="Liste_HTTP"
netsh ipsec static add filter filterlist="Liste_HTTP" srcaddr=Any dstaddr=Any protocol=TCP srcport=80 dstport=80
```

6. Enfin, activez la politique :

```
netsh ipsec static set policy name="Politique_IPSec_PowerShell" assign=yes
```


# Annexe 2 :  Vérification si  les règles et politiques sont correctement configurées et actives.

### Problème avec netsh ipsec static add rule name="Regle_HTTP" policy="Politique_IPSec_Basique" filterlist="Tout_HTTP" filteraction="requireinrequireout"
Si la commande  `netsh ipsec static add rule name="Regle_HTTP" policy="Politique_IPSec_Basique" filterlist="Tout_HTTP" filteraction="requireinrequireout"` ne fonctionne pas, c'est probablement en raison de plusieurs raisons possibles comme des erreurs dans la syntaxe ou la configuration de l'environnement réseau. 
- Voici quelques étapes pour corriger et améliorer la configuration :

### 1. Vérifiez l'existence de la politique IPSec et de la liste de filtres

Avant d'ajouter une règle IPSec, assurez-vous que la **politique IPSec** et la **liste de filtres** existent bien dans votre configuration.

#### Commande pour vérifier les politiques existantes :
```bash
netsh ipsec static show policy
```

#### Commande pour vérifier les filtres existants :
```bash
netsh ipsec static show filterlist
```

Si vous ne voyez pas la politique `Politique_IPSec_Basique` ou la liste de filtres `Tout_HTTP`, cela signifie qu'ils n'ont pas été créés ou qu'il y a un problème avec leur configuration.

### 2. Créez la politique et la liste de filtres si nécessaire

Si la politique ou la liste de filtres n'existent pas, vous devez les créer :

#### Créer la politique :
```bash
netsh ipsec static add policy name="Politique_IPSec_Basique" description="Politique de base pour chiffrer le trafic HTTP"
```

#### Créer la liste de filtres :
```bash
netsh ipsec static add filterlist name="Tout_HTTP"
```

#### Ajouter le filtre HTTP à la liste de filtres :
```bash
netsh ipsec static add filter filterlist="Tout_HTTP" srcaddr=Me dstaddr=Any protocol=TCP srcport=80 dstport=80
```

### 3. Ajouter la règle IPSec

Une fois la politique et la liste de filtres configurées, vous pouvez essayer d'ajouter à nouveau la règle IPSec :

```bash
netsh ipsec static add rule name="Regle_HTTP" policy="Politique_IPSec_Basique" filterlist="Tout_HTTP" filteraction="requireinrequireout"
```

### 4. Activer la politique IPSec

N'oubliez pas d'activer la politique une fois que la règle a été ajoutée :

```bash
netsh ipsec static set policy name="Politique_IPSec_Basique" assign=yes
```

### 5. Dépannage

Si vous rencontrez toujours des problèmes, vous pouvez utiliser les commandes suivantes pour obtenir plus d'informations sur les règles et les politiques IPSec en cours d'exécution :

#### Vérifier les règles IPSec :
```bash
netsh ipsec static show rule
```

#### Vérifier les détails de la politique IPSec :
```bash
netsh ipsec static show policy name="Politique_IPSec_Basique"
```



# Annexe 3  - action de filtre incorrecte ou non supportée


### 1. Actions de filtre valides pour les règles IPSec :
Les actions de filtre typiques pour IPSec sont :

- `permit` – Autoriser le trafic sans chiffrement IPSec.
- `block` – Bloquer le trafic.
- `negotiate` – Négocier la protection IPSec.
- `requireinbound` ou `requireoutbound` – Exiger IPSec pour le trafic entrant ou sortant.
- `require` – Exiger IPSec pour le trafic entrant et sortant (ce qui correspond probablement à ce que vous vouliez faire avec `requireinrequireout`).

### Commande corrigée :
Remplacez `requireinrequireout` par `require` dans votre commande.

```bash
netsh ipsec static add rule name="Regle_HTTP" policy="Politique_IPSec_Basique" filterlist="Tout_HTTP" filteraction="require"
```

Cela garantira que le trafic entrant et sortant sera chiffré.

### Étapes suivantes :
1. Exécutez la commande corrigée pour ajouter la règle.
2. Vérifiez la règle avec :
   ```bash
   netsh ipsec static show rule
   ```
3. Activez la politique si ce n'est pas déjà fait :
   ```bash
   netsh ipsec static set policy name="Politique_IPSec_Basique" assign=yes
   ```

# Annexe 4 - action de filtrage nommée "requireinrequireout" n'existe pas


1. **Créer l'action de filtrage manquante :**

   Vous devez d'abord créer l'action de filtrage avec le nom "requireinrequireout". Utilisez la commande suivante pour le faire :

   ```bash
   netsh ipsec static add filteraction name="requireinrequireout" action=requireinrequireout
   ```

   Assurez-vous que le nom et l'action correspondent à ce que vous souhaitez configurer.

2. **Vérifier les actions de filtrage existantes :**

   Pour vérifier les actions de filtrage existantes, utilisez la commande suivante :

   ```bash
   netsh ipsec static show filteraction
   ```

   Cela vous permettra de voir si l'action de filtrage a été correctement créée.

3. **Recréer la règle :**

   Une fois l'action de filtrage créée, essayez de recréer la règle IPSec :

   ```bash
   netsh ipsec static add rule name="Regle_HTTP" policy="Politique_IPSec_Basique" filterlist="Tout_HTTP" filteraction="requireinrequireout"
   ```

4. **Vérifier la syntaxe :**

   Assurez-vous que la syntaxe de votre commande est correcte et que toutes les ressources nécessaires (policy, filterlist, filteraction) existent.

En suivant ces étapes, vous devriez être en mesure de résoudre l'erreur et de configurer correctement votre règle IPSec.
