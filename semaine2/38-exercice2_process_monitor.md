
## 6.3 Exercice 2 : Utiliser Process Monitor pour Suivre l'Activité d'un Processus Malveillant Simulé

### Objectif :
Cet exercice vous apprendra à utiliser Process Monitor pour surveiller et analyser l'activité d'un processus malveillant simulé. Vous apprendrez à filtrer les événements pour identifier les activités suspectes sur le système.

### Étapes de l'Exercice :

1. **Lancement de Process Monitor :**
   - **Téléchargement :** Téléchargez Process Monitor depuis le site officiel de [Sysinternals](https://docs.microsoft.com/en-us/sysinternals/downloads/procmon).
   - **Exécution :** Exécutez **procmon.exe** en tant qu'administrateur sur votre machine virtuelle Windows 10 ou Windows Server 2019.
   - **Enregistrement des événements :** Assurez-vous que l'enregistrement des événements est activé (il l'est par défaut lors du lancement de Process Monitor).

2. **Simulation d'un Processus Malveillant :**
   - **Script PowerShell Malveillant :** Utilisez le script suivant pour simuler un processus malveillant qui modifie une clé de registre importante :
     ```powershell
     # Ce script modifie une clé de registre critique de Windows
     Set-ItemProperty -Path "HKLM:\Software\Microsoft\Windows\CurrentVersion\Run" -Name "MaliciousApp" -Value "C:\MaliciousApp.exe"
     ```
     - **Instructions :**
       1. Ouvrez PowerShell en tant qu'administrateur.
       2. Copiez-collez le script ci-dessus dans la console PowerShell.
       3. Exécutez le script pour ajouter une entrée au registre qui simule un programme malveillant au démarrage.

3. **Filtrage des Événements :**
   - **Filtrer les événements de modification du registre :**
     - Cliquez sur **Filter** dans Process Monitor et ajoutez un filtre pour **Operation** est **RegSetValue**.
     - **Explication :** Ce filtre vous permet de concentrer l'affichage sur les événements où des valeurs de registre sont définies ou modifiées, ce qui est pertinent pour notre script.

4. **Analyse des Détails du Processus :**
   - **Examiner les événements capturés :**
     - Identifiez l'événement lié à la modification de la clé de registre effectuée par le script PowerShell.
     - **Vérification des détails :** Notez le chemin du registre modifié, la valeur ajoutée, et le processus responsable de cette modification.

5. **Documentation :**
   - **Rapport :** Rédigez un rapport détaillant les étapes suivies pour simuler l'activité malveillante, les événements capturés par Process Monitor, et une analyse de l'impact potentiel de cette activité sur le système.

### Conclusion :
Cet exercice vous a permis de comprendre comment utiliser Process Monitor pour surveiller les activités en temps réel sur un système Windows, en particulier les modifications critiques du registre. En documentant vos observations, vous pourrez mieux comprendre les implications de ces activités sur la sécurité du système.
