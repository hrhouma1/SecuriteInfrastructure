
## 6.2 Exercice 1 : Configurer Sysmon sur une Machine Virtuelle et Détecter une Activité Anormale

### Objectif :
Cet exercice a pour but de vous familiariser avec la configuration de Sysmon sur une machine virtuelle et d'utiliser cet outil pour détecter des activités anormales sur le système.

### Étapes de l'Exercice :

1. **Création de la Machine Virtuelle :**
   - **Outil recommandé :** Utilisez un hyperviseur tel que VirtualBox ou VMware pour créer une machine virtuelle.
   - **Système d'exploitation :** Installez Windows 10 ou Windows Server 2019 sur la machine virtuelle.
   - **Configuration réseau :** Assurez-vous que la machine virtuelle est connectée à un réseau pour permettre la simulation d'activités réseau.

2. **Installation de Sysmon :**
   - **Téléchargement :** Téléchargez Sysmon depuis le site officiel de [Sysinternals](https://docs.microsoft.com/en-us/sysinternals/downloads/sysmon).
   - **Installation :** Ouvrez une invite de commande en tant qu'administrateur et exécutez la commande suivante pour installer Sysmon :
     ```bash
     sysmon.exe -accepteula -i sysmonconfig-export.xml
     ```
   - **Explication :** Cette commande accepte automatiquement l'accord de licence utilisateur final (EULA) et installe Sysmon en utilisant le fichier de configuration par défaut `sysmonconfig-export.xml`.

3. **Configuration Personnalisée de Sysmon :**
   - **Téléchargement du fichier de configuration :** Vous pouvez utiliser un fichier de configuration personnalisé tel que celui disponible sur GitHub [SwiftOnSecurity Sysmon Config](https://github.com/SwiftOnSecurity/sysmon-config).
   - **Application de la configuration :** Pour appliquer cette configuration, utilisez la commande suivante :
     ```bash
     sysmon.exe -c cheminVers\sysmonconfig-export.xml
     ```
   - **Explication :** Ce fichier de configuration permet de surveiller des événements spécifiques tels que la création de processus, les connexions réseau, et les modifications de fichiers, en fonction des besoins spécifiques de l'entreprise.

4. **Détection d'une Activité Anormale :**
   - **Simuler une activité suspecte :** 
     - Pour cela, vous pouvez exécuter un script PowerShell malveillant qui crée un fichier texte dans un répertoire système. 
     - **Exemple de script PowerShell :**
       ```powershell
       # Ce script crée un fichier texte dans le répertoire système de Windows
       $path = "C:\Windows\System32\malicious.txt"
       New-Item -Path $path -ItemType "file" -Value "This is a malicious file"
       ```
     - **Instructions :**
       1. Ouvrez PowerShell en tant qu'administrateur sur votre machine virtuelle.
       2. Copiez-collez le script ci-dessus dans la console PowerShell.
       3. Exécutez le script pour créer un fichier suspect.

   - **Vérification avec la Visionneuse d'événements (Event Viewer) :**
     - Ouvrez l'Event Viewer en tapant `eventvwr.msc` dans la boîte de dialogue **Exécuter** (`Win + R`).
     - Naviguez vers **Applications and Services Logs > Microsoft > Windows > Sysmon/Operational** pour examiner les événements générés par Sysmon.
     - **Points à rechercher :** Vous devriez voir des événements indiquant la création du fichier texte et l'exécution du script PowerShell. Les événements liés à la création de processus et à l'accès aux fichiers doivent être surveillés.

5. **Documentation :**
   - **Rapport :** Notez les événements détectés, notamment ceux liés à l'activité anormale. Incluez des captures d'écran des journaux Sysmon pertinents et décrivez l'efficacité de Sysmon dans la détection de cette activité.

### Conclusion :
Cet exercice vous a permis de configurer Sysmon sur une machine virtuelle et de simuler une activité anormale pour tester ses capacités de surveillance. Documenter vos résultats vous aidera à comprendre comment Sysmon peut être utilisé dans des scénarios réels pour détecter des activités malveillantes.
