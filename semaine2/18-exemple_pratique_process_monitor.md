
## 2.2.5 Exemple Pratique : Utilisation de Process Monitor

### Scénario :
Supposons que vous souhaitiez surveiller l'activité d'un processus spécifique sur votre système pour détecter des comportements suspects. Vous pouvez configurer Process Monitor pour capturer uniquement les événements générés par ce processus et analyser les résultats pour identifier d'éventuelles anomalies.

### Étapes :
1. **Filtrer par Processus :**
   - Lancez Process Monitor et configurez un filtre pour ne capturer que les événements générés par le processus `notepad.exe`.
   - Pour ce faire, allez dans **Filter** > **Process Name** > **is** > entrez `notepad.exe` > **Add** > **OK**.

2. **Exécution du Processus :**
   - Lancez **notepad.exe** sur votre système. Process Monitor commencera à capturer les événements associés à ce processus, tels que l'accès aux fichiers et les modifications du registre.

3. **Surveiller les Événements Capturés :**
   - Observez en temps réel les événements que Process Monitor capture. Par exemple, chaque fois que Notepad accède à un fichier ou enregistre des modifications, un événement correspondant apparaîtra dans la liste.

4. **Analyse des Informations Capturées :**
   - Cliquez sur les événements pour voir les détails, tels que le chemin d'accès aux fichiers, le type d'opération (lecture, écriture), et le résultat de l'opération. Recherchez des comportements inhabituels, comme l'accès à des fichiers système ou la modification de clés de registre.

5. **Enregistrement et Documentation :**
   - Enregistrez les résultats capturés en allant sur **File** > **Save**. Documentez vos observations, en particulier tout comportement suspect identifié lors de l'analyse.

### Conclusion :
Cet exemple montre comment Process Monitor peut être utilisé pour surveiller et analyser l'activité d'un processus spécifique. En configurant des filtres appropriés et en analysant les événements capturés, vous pouvez identifier des comportements anormaux qui pourraient indiquer une activité malveillante ou un problème système.
