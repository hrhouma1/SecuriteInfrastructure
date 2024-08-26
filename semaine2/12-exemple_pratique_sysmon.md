
## 2.1.4 Exemple Pratique : Surveillance avec Sysmon

### Scénario :
Supposons que vous souhaitiez surveiller toute création de processus sur votre système afin de détecter des comportements suspects. Vous pouvez configurer Sysmon pour capturer ces événements et vérifier si des processus inconnus ou inattendus sont créés.

### Étapes :
1. **Configuration de Sysmon pour la Création de Processus :**
   - Utilisez un fichier de configuration XML qui spécifie la capture des événements de création de processus (Event ID 1).
   - Appliquez cette configuration à Sysmon en utilisant la commande :
     ```bash
     sysmon -c cheminers\sysmonconfig.xml
     ```

2. **Exécution d'un Processus :**
   - Pour tester la configuration, lancez un programme simple, par exemple **notepad.exe**.
   - Ce processus sera enregistré par Sysmon et un événement sera généré.

3. **Vérification dans la Visionneuse d'Événements :**
   - Ouvrez la Visionneuse d'événements et naviguez jusqu'au journal Sysmon comme décrit précédemment.
   - Recherchez l'événement ID 1 correspondant à la création du processus **notepad.exe**.

4. **Analyse des Informations Capturées :**
   - Examinez les détails de l'événement, y compris le chemin d'accès du processus, le PID, et l'utilisateur qui a initié le processus.
   - Comparez ces informations avec la liste des processus légitimes pour identifier tout processus suspect.

### Conclusion :
En suivant ces étapes, vous pouvez utiliser Sysmon pour surveiller la création de processus sur votre système, un aspect crucial pour la détection proactive des activités malveillantes. Cet exemple montre comment Sysmon peut être configuré et utilisé dans un scénario de sécurité réel.
