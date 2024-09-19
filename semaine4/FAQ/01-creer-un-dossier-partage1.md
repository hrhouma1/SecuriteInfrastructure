- Pour créer un dossier partagé sur ton contrôleur de domaine (DC), puis le rendre accessible à une machine faisant partie du domaine, voici les étapes avec PowerShell :

# 1. **Création du dossier partagé sur le contrôleur de domaine :**

#### a. Crée un dossier sur le contrôleur de domaine :
Ouvre PowerShell en tant qu’administrateur et exécute la commande suivante pour créer un dossier, par exemple `C:\Partages`.

```powershell
New-Item -Path "C:\Partages" -ItemType Directory
```

#### b. Partage ce dossier :
Utilise `New-SmbShare` pour partager ce dossier avec les autorisations appropriées :

```powershell
New-SmbShare -Name "PartageTest" -Path "C:\Partages" -FullAccess "Domain Users"
```

Dans cet exemple :
- `-Name` est le nom du partage.
- `-Path` est l’emplacement du dossier à partager.
- `-FullAccess` permet aux utilisateurs du domaine d’avoir un accès complet.

# 2. **Vérification de l'état du partage sur le DC :**

Pour vérifier si le dossier est bien partagé, utilise cette commande :

```powershell
Get-SmbShare | Where-Object Name -eq "PartageTest"
```

Cette commande affichera les informations sur le partage si la configuration est correcte.

# 3. **Vérification de l'accessibilité depuis la machine Windows 10 (client) :**

#### a. Ouvre PowerShell sur la machine cliente (Windows 10).

#### b. Utilise la commande `Test-Path` pour vérifier si le partage est accessible depuis la machine cliente :

```powershell
Test-Path "\\<NomDC>\PartageTest"
```

Remplace `<NomDC>` par le nom de ton contrôleur de domaine (ou son adresse IP). Si le partage est accessible, cette commande retournera `True`.

#### c. Pour mapper le partage réseau sur la machine cliente :

```powershell
New-PSDrive -Name "Z" -PSProvider FileSystem -Root "\\<NomDC>\PartageTest" -Persist
```

Cela montera le partage comme un lecteur réseau (`Z:` dans cet exemple).

# 4. **Dépannage :**
Si tu ne peux pas accéder au partage, vérifie :
- Les autorisations NTFS sur le dossier.
- Les règles de pare-feu sur le contrôleur de domaine pour les connexions SMB.
- Que la machine cliente fait bien partie du domaine et peut résoudre le nom du contrôleur de domaine (`ping <NomDC>`).

Cela devrait te permettre de créer et vérifier l’accessibilité du partage depuis le client Windows 10.
