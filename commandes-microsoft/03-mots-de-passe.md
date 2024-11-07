
# 01 - Commandes:

```bash
netsh wlan show profiles
netsh wlan show profile name="MonWifi" key=clear
```



# 02 - Explications: 
Pour afficher les mots de passe des profils Wi-Fi enregistrés sous Windows en utilisant `netsh`, vous pouvez utiliser la commande suivante :

```bash
netsh wlan show profile name="NOM_DU_RESEAU" key=clear
```

### Explications et options :

- **`netsh wlan show profile`** : Affiche les profils Wi-Fi enregistrés sur l'ordinateur.
- **`name="NOM_DU_RESEAU"`** : Remplacez `"NOM_DU_RESEAU"` par le nom exact du réseau Wi-Fi pour lequel vous voulez voir les détails. Si vous ne connaissez pas le nom, vous pouvez lister tous les profils disponibles avec :
  ```bash
  netsh wlan show profiles
  ```
- **`key=clear`** : Affiche le mot de passe du réseau en texte clair dans la section **Key Content**.

### Exemple :

Pour voir le mot de passe du réseau Wi-Fi nommé `"MonWifi"` :
```bash
netsh wlan show profile name="MonWifi" key=clear
```

Cela affichera les détails de la configuration du réseau `"MonWifi"`, y compris le mot de passe sous "Contenu de la clé" (Key Content) si celui-ci est stocké.


# 03 - Script-version-01


Voici un script PowerShell qui récupère automatiquement tous les profils Wi-Fi enregistrés, extrait les mots de passe pour chaque profil, et les exporte dans un fichier CSV :

```powershell
# Définir le fichier de sortie CSV
$outputFile = "$env:USERPROFILE\Desktop\wifi_passwords.csv"

# Créer ou vider le fichier CSV avec les en-têtes
"Profile Name,Password" | Out-File -FilePath $outputFile -Encoding UTF8

# Obtenir tous les profils Wi-Fi
$profiles = netsh wlan show profiles | Select-String "All User Profile\s*:\s*(.*)" | ForEach-Object { $_.Matches[0].Groups[1].Value }

foreach ($profile in $profiles) {
    # Obtenir les informations du profil, y compris le mot de passe
    $profileInfo = netsh wlan show profile name="$profile" key=clear
    
    # Extraire le mot de passe si présent
    $password = ($profileInfo | Select-String "Key Content\s*:\s*(.*)").Matches[0].Groups[1].Value

    # Ajouter les données au fichier CSV
    "$profile,$password" | Out-File -FilePath $outputFile -Encoding UTF8 -Append
}

Write-Output "Exportation des mots de passe Wi-Fi terminée. Fichier CSV disponible à : $outputFile"
```

### Explications du script :
- **`$outputFile`** : Définit l’emplacement du fichier de sortie sur le bureau de l’utilisateur. Vous pouvez modifier ce chemin selon vos besoins.
- **`Select-String`** : Utilisé pour extraire le nom de chaque profil ainsi que le mot de passe.
- **`ForEach-Object`** : Pour chaque profil, le script exécute la commande `netsh` pour afficher les détails du profil, incluant le mot de passe en clair.
- **`Out-File -Append`** : Ajoute chaque profil et mot de passe au fichier CSV.

Après exécution, un fichier `wifi_passwords.csv` contenant tous les profils Wi-Fi et leurs mots de passe sera créé sur le bureau.



# 04 - Script-version-02 (sans warnings ou erreur)

$outputFile = "$env:USERPROFILE\Desktop\wifi_passwords.csv"

# Créer ou vider le fichier CSV avec les en-têtes
"Profile Name,Password" | Out-File -FilePath $outputFile -Encoding UTF8

# Obtenir tous les profils Wi-Fi
$profiles = netsh wlan show profiles | Select-String "All User Profile\s*:\s*(.*)" | ForEach-Object { $_.Matches[0].Groups[1].Value }

foreach ($profile in $profiles) {
    # Obtenir les informations du profil, y compris le mot de passe
    $profileInfo = netsh wlan show profile name="$profile" key=clear
    
    # Extraire le mot de passe si présent
    $passwordMatch = $profileInfo | Select-String "Key Content\s*:\s*(.*)"
    
    # Vérifier si un mot de passe a été trouvé
    if ($passwordMatch) {
        $password = $passwordMatch.Matches[0].Groups[1].Value
    } else {
        $password = "Non disponible"
    }

    # Ajouter les données au fichier CSV
    "$profile,$password" | Out-File -FilePath $outputFile -Encoding UTF8 -Append
}

Write-Output "Exportation des mots de passe Wi-Fi terminée. Fichier CSV disponible à : $outputFile"
