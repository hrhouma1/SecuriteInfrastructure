
## 2.2.1 Téléchargement et Lancement de Process Monitor

### Étapes pour Télécharger Process Monitor :
1. **Accédez au site officiel de Sysinternals :**
   - Rendez-vous sur [Sysinternals](https://docs.microsoft.com/en-us/sysinternals/downloads/procmon) pour télécharger la dernière version de Process Monitor.
2. **Téléchargement de l'outil :**
   - Téléchargez le fichier compressé contenant Process Monitor et extrayez-le dans un dossier de votre choix sur la machine que vous souhaitez analyser.

### Lancement de Process Monitor :
1. **Ouvrir Process Monitor :**
   - Exécutez le fichier `procmon.exe` en tant qu'administrateur pour obtenir toutes les permissions nécessaires pour surveiller les activités système.
2. **Commencer la Capture des Événements :**
   - Lorsque Process Monitor est lancé, il commence automatiquement à capturer les événements système. Vous verrez une liste de toutes les opérations en cours sur le système, comme les accès aux fichiers, les modifications du registre, et l'activité des processus.

### Navigation dans l'Interface :
- **Panneau Principal :**
   - Le panneau principal affiche en temps réel les événements capturés. Chaque ligne représente une opération unique, avec des colonnes pour l'heure, le processus, le type d'opération, le chemin d'accès, et plus encore.
- **Barre d'Outils :**
   - Utilisez la barre d'outils pour filtrer les événements, démarrer ou arrêter la capture, et enregistrer les journaux. Vous pouvez également accéder à des options de configuration pour ajuster ce qui est capturé.
- **Options de Filtrage :**
   - Cliquez sur **Filter** pour définir des critères de filtrage, tels que le nom du processus ou le type d'opération, afin de vous concentrer sur les événements les plus pertinents pour votre analyse.

### Enregistrement des Journaux :
- **Sauvegarder la Capture :**
   - Pour sauvegarder les événements capturés pour une analyse ultérieure, cliquez sur **File** > **Save** et choisissez le format de fichier et l'emplacement où vous souhaitez enregistrer le journal.
- **Rejouer une Capture :**
   - Vous pouvez également charger une capture précédente pour l'analyser en cliquant sur **File** > **Open** et en sélectionnant le fichier journal.

Process Monitor est maintenant prêt à être utilisé pour une surveillance et une analyse approfondies des activités du système.
