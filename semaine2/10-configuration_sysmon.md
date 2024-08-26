
## 2.1.2 Configuration de Sysmon

### Fichier de Configuration Personnalisé :
- **Création du Fichier :**
  - Pour une surveillance plus spécifique, il est recommandé d'utiliser un fichier de configuration XML. Ce fichier permet de filtrer les événements que vous souhaitez capturer, tels que la création de processus, les connexions réseau, ou les modifications de fichiers.
  - Un exemple de fichier de configuration pour capturer les événements critiques est disponible sur le dépôt GitHub de [SwiftOnSecurity](https://github.com/SwiftOnSecurity/sysmon-config).

### Application du Fichier de Configuration :
1. **Téléchargement du Fichier de Configuration :**
   - Téléchargez le fichier `sysmonconfig-export.xml` depuis le dépôt GitHub ou créez votre propre fichier XML personnalisé.
2. **Application de la Configuration :**
   - Exécutez la commande suivante pour appliquer la configuration :
     ```bash
     sysmon -c cheminers\sysmonconfig-export.xml
     ```
   - **Explication :** Cette commande met à jour Sysmon avec la nouvelle configuration, sans nécessiter de réinstallation.

### Vérification de la Configuration :
- **Consultation des Journaux :**
  - Après avoir appliqué la nouvelle configuration, vérifiez les journaux dans la Visionneuse d'événements pour vous assurer que les événements configurés sont bien capturés.
  - Les événements sont enregistrés sous **Applications and Services Logs > Microsoft > Windows > Sysmon/Operational**.

### Conseils pour une Configuration Optimale :
- **Utilisation des Filtres :**
  - Utilisez des filtres dans votre fichier XML pour réduire le bruit et capturer uniquement les événements pertinents.
- **Testez et Ajustez :**
  - Testez la configuration sur un environnement de test avant de l'appliquer en production pour vous assurer qu'elle capture les événements souhaités sans surcharger les journaux.
