
## 2.1.3 Surveillance des Événements avec Sysmon

### Accéder aux Journaux Sysmon :
1. **Ouvrir la Visionneuse d'Événements :**
   - Appuyez sur `Win + R`, tapez `eventvwr.msc` et appuyez sur Entrée pour ouvrir la Visionneuse d'événements.
2. **Naviguer vers les Journaux Sysmon :**
   - Dans le volet de gauche, déroulez **Applications and Services Logs** > **Microsoft** > **Windows** > **Sysmon** > **Operational**.
   - Ici, vous pouvez voir tous les événements capturés par Sysmon.

### Types d'Événements à Surveiller :
1. **Création de Processus (Event ID 1) :**
   - Cet événement est généré chaque fois qu'un nouveau processus est créé. Il inclut des informations détaillées sur le processus parent, le chemin d'accès, et les arguments de la ligne de commande.
2. **Connexion Réseau (Event ID 3) :**
   - Cet événement enregistre les connexions réseau effectuées par les processus sur la machine. Il contient des détails tels que l'adresse IP source, l'adresse IP de destination, et les ports utilisés.
3. **Modification de Fichier (Event ID 11) :**
   - Cet événement se déclenche lorsqu'un fichier est créé ou modifié. Il est particulièrement utile pour surveiller les répertoires critiques.

### Analyse des Journaux :
- **Filtrage des Événements :**
  - Utilisez les fonctionnalités de filtrage de la Visionneuse d'événements pour rechercher des événements spécifiques, par exemple, en filtrant par ID d'événement ou par chemin d'accès de fichier.
- **Corrélation avec d'autres Sources :**
  - Corrélez les événements capturés par Sysmon avec d'autres journaux système, tels que les journaux de sécurité Windows, pour une analyse complète des incidents.

### Bonnes Pratiques :
- **Surveiller les Processus Non Signés :**
  - Prêtez une attention particulière aux processus qui ne sont pas signés numériquement, car ils peuvent être des indicateurs de compromission.
- **Analyser les Connexions Inhabituelles :**
  - Les connexions réseau vers des adresses IP non reconnues ou vers des pays à risque peuvent indiquer une activité malveillante.
