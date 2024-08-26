
## 2.1.1 Téléchargement et Installation de Sysmon

### Étapes pour Télécharger Sysmon :
1. **Accédez au site officiel de Sysinternals :**
   - Visitez [Sysinternals](https://docs.microsoft.com/en-us/sysinternals/downloads/sysmon) pour télécharger la dernière version de Sysmon.
2. **Téléchargement de l'archive :**
   - Téléchargez le fichier compressé contenant Sysmon et extrayez-le dans un dossier de votre choix sur la machine que vous souhaitez surveiller.

### Installation de Sysmon :
1. **Ouvrir une Invite de Commande en tant qu'Administrateur :**
   - Cliquez avec le bouton droit sur le menu Démarrer et sélectionnez "Invite de commandes (admin)" ou "Windows PowerShell (admin)" pour ouvrir une session avec les privilèges nécessaires.
2. **Accéder au Répertoire d'Installation :**
   - Naviguez jusqu'au répertoire où vous avez extrait l'archive de Sysmon.
3. **Exécuter la Commande d'Installation :**
   - Utilisez la commande suivante pour installer Sysmon avec la configuration par défaut :
     ```bash
     sysmon -accepteula -i
     ```
   - **Explication :** Cette commande accepte l'accord de licence utilisateur final (EULA) et installe Sysmon en utilisant la configuration par défaut.

### Vérification de l'Installation :
- **Confirmation de l'Installation :**
   - Après l'installation, vous pouvez vérifier que Sysmon fonctionne correctement en consultant les journaux d'événements Windows sous **Applications and Services Logs > Microsoft > Windows > Sysmon/Operational** dans la Visionneuse d'événements.
- **Vérification de la Version :**
   - Exécutez `sysmon -s` pour vérifier la version de Sysmon installée et pour voir la configuration actuelle.
