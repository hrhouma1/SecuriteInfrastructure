- Pour créer un dossier partagé sur le contrôleur de domaine et le rendre accessible depuis une machine cliente Windows 10, suivez ces étapes avec PowerShell :

## Création du partage sur le contrôleur de domaine

1. Connectez-vous au contrôleur de domaine et ouvrez PowerShell en tant qu'administrateur.

2. Créez le dossier à partager :

```powershell
New-Item -Path "C:\Partages\MonDossier" -ItemType Directory
```

3. Créez le partage SMB :

```powershell
New-SmbShare -Name "MonPartage" -Path "C:\Partages\MonDossier" -FullAccess "Tout le monde"
```

4. Vérifiez que le partage a été créé :

```powershell
Get-SmbShare -Name "MonPartage"
```

## Vérification de l'accessibilité depuis le client Windows 10

1. Sur la machine cliente Windows 10, ouvrez PowerShell en tant qu'administrateur.

2. Testez l'accès au partage :

```powershell
Test-Path "\\NomDuDC\MonPartage"
```

Si le résultat est "True", le partage est accessible[6].

3. Listez le contenu du partage :

```powershell
Get-ChildItem "\\NomDuDC\MonPartage"
```

## Dépannage

Si le partage n'est pas accessible, vérifiez les points suivants :

1. Assurez-vous que le pare-feu Windows autorise le trafic SMB (port 445)[1].

2. Vérifiez les permissions NTFS du dossier partagé sur le contrôleur de domaine :

```powershell
Get-Acl -Path "C:\Partages\MonDossier" | Format-List
```

3. Vérifiez les permissions du partage SMB :

```powershell
Get-SmbShareAccess -Name "MonPartage"
```

4. Sur le client Windows 10, assurez-vous que la découverte réseau est activée et que le client est bien connecté au domaine[3].

5. Essayez d'accéder au partage via l'Explorateur de fichiers en utilisant le chemin UNC : `\\NomDuDC\MonPartage`

Si le problème persiste, vérifiez les journaux d'événements sur le contrôleur de domaine et le client Windows 10 pour identifier d'éventuelles erreurs liées au partage ou à l'accès réseau[5].

