
## 2.2.2 Filtrage des Activités

### Importance du Filtrage :
Process Monitor capture une énorme quantité d'informations en temps réel, ce qui peut être accablant. Pour se concentrer sur les événements pertinents, il est essentiel d'utiliser les filtres pour réduire le bruit et cibler les activités spécifiques que vous souhaitez analyser.

### Types de Filtres :
1. **Filtre par Processus :**
   - Filtrer par nom de processus permet de se concentrer sur un programme spécifique. Par exemple, si vous soupçonnez qu'un programme particulier effectue des opérations malveillantes, vous pouvez filtrer pour ne voir que les événements générés par ce processus.
   - **Exemple :** Filtrer par le nom du processus `notepad.exe`.

2. **Filtre par Type d'Opération :**
   - Vous pouvez filtrer les événements par type d'opération, tels que les lectures/écritures de fichiers, les modifications de registre, ou les connexions réseau.
   - **Exemple :** Filtrer pour ne voir que les opérations `RegSetValue` pour surveiller les modifications des clés de registre.

3. **Filtre par Chemin d'Accès :**
   - Filtrer par chemin d'accès vous permet de surveiller les activités sur des fichiers ou répertoires spécifiques. Cela est utile pour suivre les modifications apportées à des répertoires critiques ou des fichiers système sensibles.
   - **Exemple :** Filtrer pour ne voir que les événements liés à `C:\Windows\System32`.

### Configuration du Filtrage :
1. **Accéder au Menu de Filtrage :**
   - Cliquez sur **Filter** dans la barre d'outils principale ou appuyez sur `Ctrl + L` pour ouvrir la fenêtre de configuration du filtrage.
2. **Définir un Nouveau Filtre :**
   - Dans la fenêtre de filtrage, sélectionnez le critère que vous souhaitez appliquer, par exemple, `Process Name`, et entrez la valeur correspondante, comme `notepad.exe`.
   - Cliquez sur **Add** pour ajouter ce filtre à la liste des filtres actifs.
3. **Appliquer les Filtres :**
   - Après avoir ajouté les filtres souhaités, cliquez sur **OK** pour les appliquer. La liste des événements se mettra à jour pour ne montrer que les événements correspondant à vos critères.

### Exemples Pratiques :
- **Surveillance des Modifications de Registre :**
   - Configurez un filtre pour surveiller les opérations `RegSetValue` sur des clés de registre critiques afin de détecter toute tentative de modification par un processus non autorisé.
- **Analyse des Fichiers Accédés :**
   - Utilisez un filtre sur le chemin d'accès pour suivre l'activité sur un fichier spécifique et identifier quels processus y accèdent.

Le filtrage efficace des événements est essentiel pour maximiser la valeur de Process Monitor en se concentrant sur les activités les plus pertinentes pour votre analyse.
