Voici un script PowerShell interactif qui vous demande de choisir une option parmi plusieurs actions courantes, puis exécute l'option sélectionnée. Ce script utilise un menu pour présenter les différentes options disponibles et exécute la commande associée en fonction du choix de l'utilisateur.

### Script PowerShell : Menu Interactif

```powershell
# Définir le menu des options
function Show-Menu {
    Clear-Host
    Write-Host "-----------------------"
    Write-Host "   MENU PRINCIPAL"
    Write-Host "-----------------------"
    Write-Host "1: Ouvrir le Gestionnaire des utilisateurs (dsa.msc)"
    Write-Host "2: Ouvrir les Connexions réseau (ncpa.cpl)"
    Write-Host "3: Ouvrir le Gestionnaire des périphériques (devmgmt.msc)"
    Write-Host "4: Ouvrir la Gestion des disques (diskmgmt.msc)"
    Write-Host "5: Ouvrir l'Observateur d'événements (eventvwr.msc)"
    Write-Host "6: Ouvrir l'Éditeur de stratégie de groupe locale (gpedit.msc)"
    Write-Host "7: Ouvrir les Programmes et fonctionnalités (appwiz.cpl)"
    Write-Host "8: Ouvrir les Propriétés système (sysdm.cpl)"
    Write-Host "9: Quitter"
    Write-Host "-----------------------"
}

# Fonction pour exécuter l'option sélectionnée
function Execute-Choice {
    param (
        [int]$choice
    )

    switch ($choice) {
        1 { Start-Process "dsa.msc" }
        2 { Start-Process "ncpa.cpl" }
        3 { Start-Process "devmgmt.msc" }
        4 { Start-Process "diskmgmt.msc" }
        5 { Start-Process "eventvwr.msc" }
        6 { Start-Process "gpedit.msc" }
        7 { Start-Process "appwiz.cpl" }
        8 { Start-Process "sysdm.cpl" }
        9 { 
            Write-Host "Fermeture du script."
            exit
        }
        default { Write-Host "Option non valide, veuillez réessayer." }
    }
}

# Boucle pour afficher le menu et attendre le choix de l'utilisateur
do {
    Show-Menu
    $choice = Read-Host "Veuillez entrer le numéro de votre choix"
    Execute-Choice -choice $choice
    Write-Host "Appuyez sur Entrée pour continuer..."
    Read-Host
} while ($true)
```

### Instructions d'utilisation :
1. **Copiez le script** dans un éditeur de texte comme Notepad.
2. **Enregistrez-le** avec l'extension `.ps1` (par exemple, `menu.ps1`).
3. **Exécutez-le** dans PowerShell :
   - Ouvrez PowerShell avec des privilèges d'administrateur.
   - Exécutez le script en tapant `.\menu.ps1` après avoir navigué dans le dossier où se trouve votre script.

### Explication :
- Le script vous présente un **menu interactif** avec plusieurs options, comme ouvrir le gestionnaire des utilisateurs, les connexions réseau, le gestionnaire des périphériques, etc.
- Vous tapez le **numéro correspondant** à l'option que vous voulez exécuter.
- Le script **exécute** la commande associée à l'option choisie.
- L'option `9` permet de **quitter** le script.

Si vous voulez ajouter d'autres options ou personnaliser le menu, il vous suffit de modifier la section `Show-Menu` et la fonction `Execute-Choice`.
