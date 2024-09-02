# **Travail Pratique 1 : Sécurisation et Analyse du Trafic SMB sur une Machine Windows 10**

### **Objectif :**
- Ce TP vous guidera à travers la simulation d'une activité suspecte sur un réseau en utilisant un script Python qui tente d'accéder de manière répétée à un partage SMB avec des informations d'identification incorrectes.
- Vous allez installer et utiliser les outils Sysinternals et PowerShell pour auditer cette activité, analyser les journaux des événements, et sécuriser le trafic SMB via la configuration de l'authentification et du chiffrement SMB.
- Enfin, vous rédigerez un rapport d'incident pour documenter vos découvertes et les actions correctives.

---

# ÉTAPE 1 : Préparation de la Machine Virtuelle Windows 10

1. **Téléchargement de VirtualBox :**
   - **Lien :** Accédez à [VirtualBox](https://www.virtualbox.org/wiki/Downloads).
   - **Installation :**
     - Téléchargez la version compatible avec votre système d'exploitation hôte (Windows, macOS, Linux).
     - Une fois téléchargé, exécutez le fichier d'installation.
     - Suivez les instructions à l'écran pour installer VirtualBox sur votre ordinateur.

2. **Téléchargement de l'ISO Windows 10 :**
   - **Lien :** Accédez au site officiel de Microsoft pour télécharger l'ISO de Windows 10. [Lien de téléchargement](https://www.microsoft.com/en-us/software-download/windows10ISO).
   - **Sélection de l'édition :**
     - Choisissez l'édition de Windows 10 que vous souhaitez installer.
     - Sélectionnez la langue et la version (32 ou 64 bits) selon votre configuration.

3. **Création d'une machine virtuelle Windows 10 dans VirtualBox :**
   - **Étape 1 :** Ouvrez VirtualBox et cliquez sur "Nouvelle".
   - **Étape 2 :** Donnez un nom à votre machine virtuelle, sélectionnez "Windows 10 (64-bit)" comme type et version, puis cliquez sur "Suivant".
   - **Étape 3 :** Allouez au moins 4 Go de mémoire RAM.
   - **Étape 4 :** Créez un nouveau disque dur virtuel, sélectionnez "VDI (VirtualBox Disk Image)", puis choisissez "Dynamically allocated".
   - **Étape 5 :** Choisissez la taille du disque dur (au moins 50 Go recommandés), puis cliquez sur "Créer".
   - **Étape 6 :** Sélectionnez la machine virtuelle dans la liste et cliquez sur "Démarrer". Lorsque vous y êtes invité, sélectionnez le fichier ISO de Windows 10 que vous avez téléchargé.
   - **Étape 7 :** Suivez les instructions à l'écran pour installer Windows 10 dans la machine virtuelle.

4. **Configuration de la machine virtuelle :**
   - **Réseau isolé :**
     - Dans VirtualBox, sélectionnez votre machine virtuelle, puis cliquez sur "Configuration".
     - Allez dans l'onglet "Réseau", et sous "Attacher à", sélectionnez "Réseau privé hôte" pour isoler la machine du réseau externe.
   - **Sauvegarde :**
     - Avant de commencer le TP, allez dans le menu "Machine" > "Prendre un instantané". Nommez cet instantané "Windows 10 - Configuration initiale". Cela vous permettra de revenir à cet état si nécessaire.

---

# ÉTAPE 2 : Installation de Python, des Outils Sysinternals, et Configuration de PowerShell

1. **Installation de Python sur Windows 10 :**
   - **Téléchargement :**
     - Accédez à [python.org/downloads](https://www.python.org/downloads/).
     - Téléchargez la version 3.x la plus récente de Python pour Windows.
   - **Installation :**
     - Exécutez l'installateur Python que vous venez de télécharger.
     - **IMPORTANT :** Lors de l'installation, cochez la case *"Add Python to PATH"*.
     - Cliquez sur *"Install Now"* et laissez l'installation se terminer.
   - **Vérification de l'installation :**
     - Appuyez sur `Win + R`, tapez `cmd` et appuyez sur `Entrée`.
     - Dans la fenêtre de commande, tapez `python --version` et appuyez sur `Entrée`. Vous devriez voir la version de Python installée.

2. **Installation des Outils Sysinternals :**
   - **Téléchargement :**
     - Allez sur [Sysinternals Suite](https://docs.microsoft.com/en-us/sysinternals/downloads/sysinternals-suite) pour télécharger l'ensemble des outils Sysinternals.
     - Cliquez sur "Download Sysinternals Suite" pour obtenir le fichier ZIP.
   - **Décompression :**
     - Après le téléchargement, localisez le fichier ZIP (généralement dans le dossier "Téléchargements").
     - Faites un clic droit sur le fichier ZIP et sélectionnez "Extraire tout". Choisissez un dossier, par exemple "C:\Outils\Sysinternals".
     - Cliquez sur "Extraire".

3. **Configuration de PowerShell pour permettre l'exécution de scripts :**
   - **Ouvrir PowerShell en tant qu'administrateur :**
     - Appuyez sur `Win + X` et sélectionnez "Windows PowerShell (Admin)".
   - **Autoriser l'exécution de scripts :**
     - Dans PowerShell, tapez la commande suivante pour permettre l'exécution de scripts :
       ```powershell
       Set-ExecutionPolicy RemoteSigned
       ```
     - Lorsque vous êtes invité à confirmer, tapez `Y` et appuyez sur `Entrée`.

4. **Installation de la bibliothèque `pysmb` pour le support SMB :**
   - **Installation avec pip :**
     - Ouvrez une invite de commande en appuyant sur `Win + R`, tapez `cmd`, puis appuyez sur `Entrée`.
     - Tapez la commande suivante pour installer la bibliothèque `pysmb` :
       ```bash
       pip install pysmb
       ```
     - Assurez-vous que l'installation se termine sans erreurs.

---

# ÉTAPE 3 : Création et Configuration du Script Python pour Simuler une Menace SMB

1. **Création du Script Python :**
   - **Ouvrir un éditeur de texte :**
     - Si vous avez installé Notepad++, ouvrez-le. Sinon, vous pouvez utiliser le Bloc-notes Windows en tapant `notepad` dans la barre de recherche Windows.
   - **Copier et configurer le code suivant :**
     - Copiez le code Python suivant dans votre éditeur de texte :
       ```python
       import os
       import sys
       import time
       import winreg as reg
       from smb.SMBConnection import SMBConnection

       def add_to_startup(file_path):
           key = reg.HKEY_CURRENT_USER
           sub_key = r"Software\Microsoft\Windows\CurrentVersion\Run"
           open_key = reg.OpenKey(key, sub_key, 0, reg.KEY_SET_VALUE)
           reg.SetValueEx(open_key, "SMBThreat", 0, reg.REG_SZ, file_path)
           reg.CloseKey(open_key)

       def attempt_smb_connection():
           server_ip = '192.168.1.100'  # Remplacez par l'IP de la machine cible sur le réseau
           server_name = 'TARGET'  # Remplacez par le nom NetBIOS du serveur SMB
           share_name = 'SHARE'  # Remplacez par le nom du partage SMB ciblé
           user_name = 'user'  # Remplacez par un nom d'utilisateur existant sur le serveur SMB
           password = 'incorrect_password'  # Utilisez un mot de passe incorrect pour simuler un accès non autorisé

           conn = SMBConnection(user_name, password, "Attacker", server_name, use_ntlm_v2=True)
           
           try:
               conn.connect(server_ip, 139)  # Tentative de connexion sur le port SMB (139 ou 445)
               print(f"Tentative d'accès au partage {share_name} sur {server_ip}")
               conn.listPath(share_name, '/')  # Tente de lister le répertoire racine du partage
           except Exception as e:
               print(f"Tentative échouée: {e}")
           finally:
               conn.close()

       if __name__ == "__main__":
           script_path = sys.argv[0]
           add_to_startup(script_path)
           
           while True:
               attempt_smb_connection()
               time.sleep(10)  # Répète toutes les 10 secondes
       ```
     - **Configuration des paramètres :**
       - **server_ip:** Remplacez `'192.168.1.100'` par l'adresse IP réelle de la machine cible sur votre réseau.
       - **server_name:** Remplacez `'TARGET'` par le nom NetBIOS de la machine cible (vous pouvez obtenir ce nom en tapant `hostname` sur la machine cible).
       - **share_name:** Remplacez `'SHARE'` par le nom réel d'un partage SMB existant sur le serveur.
      

 - **user_name:** Remplacez `'user'` par un nom d'utilisateur existant sur la machine cible.
       - **password:** Laissez `'incorrect_password'` ou utilisez un autre mot de passe incorrect pour simuler une tentative d'accès échouée.

2. **Enregistrement du Script Python :**
   - Enregistrez le fichier sous le nom `SMBThreat.py` dans un répertoire facile à retrouver, par exemple dans le dossier *Documents*.

---

# ÉTAPE 4 : Exécution et Analyse du Script sur Windows 10

1. **Exécution du script :**
   - **Ouvrir une invite de commande :** 
     - Appuyez sur `Win + R`, tapez `cmd`, et appuyez sur `Entrée` pour ouvrir une invite de commande.
   - **Naviguer vers le répertoire du script :**
     - Utilisez la commande `cd` pour vous déplacer vers le répertoire où vous avez enregistré `SMBThreat.py`.
     - Par exemple : 
       ```bash
       cd C:\Users\VotreNomUtilisateur\Documents
       ```
   - **Lancer le script :**
     - Tapez `python SMBThreat.py` et appuyez sur `Entrée` pour exécuter le script.
     - Le script va maintenant tenter de se connecter de manière répétée au partage SMB cible avec des informations d'identification incorrectes.

2. **Vérification dans le Registre :**
   - **Ouvrir l'Éditeur de Registre :**
     - Appuyez sur `Win + R`, tapez `regedit`, et appuyez sur `Entrée`.
   - **Vérification de l'entrée dans le Registre :**
     - Naviguez vers `HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Run`.
     - Vous devriez voir une entrée nommée `SMBThreat` pointant vers le chemin du script Python.
     - **Important :** Cela signifie que le script est configuré pour s'exécuter automatiquement au démarrage.

3. **Analyse des Tentatives de Connexion avec PowerShell :**
   - **Ouvrir PowerShell en tant qu'administrateur :**
     - Appuyez sur `Win + X` et sélectionnez "Windows PowerShell (Admin)".
   - **Audit des connexions SMB :**
     - Tapez la commande suivante pour vérifier les connexions SMB actives :
       ```powershell
       Get-SmbConnection
       ```
     - **Observation :** Prenez note des connexions suspectes ou échouées qui apparaissent, notamment celles générées par le script.

---

# ÉTAPE 5 : Analyse du Trafic SMB avec Wireshark et Sysinternals sur Windows 10

1. **Installation de Wireshark :**
   - **Téléchargement :**
     - Allez sur le site officiel de [Wireshark](https://www.wireshark.org/download.html).
     - Téléchargez la version Windows de Wireshark.
   - **Installation :**
     - Exécutez le fichier d'installation et suivez les instructions à l'écran.
     - Lorsqu'on vous le demande, acceptez d'installer WinPcap (un module nécessaire pour la capture du trafic réseau).
   - **Lancement :**
     - Après l'installation, lancez Wireshark.

2. **Capture du Trafic SMB avec Wireshark :**
   - **Démarrer une Capture :**
     - Sélectionnez l'interface réseau utilisée par votre machine virtuelle.
     - Cliquez sur l'icône de requin pour démarrer la capture du trafic.
   - **Filtrage du Trafic SMB :**
     - Dans la barre de filtre, tapez `smb` et appuyez sur `Entrée` pour filtrer uniquement le trafic SMB.
   - **Observation :**
     - Surveillez les tentatives de connexion répétées et échouées envoyées par le script.
     - Prenez des captures d'écran des paquets suspects pour votre rapport d'incident.

3. **Utilisation de Process Monitor pour l'Analyse :**
   - **Lancer Process Monitor :**
     - Accédez au dossier Sysinternals (ex. `C:\Outils\Sysinternals`) et double-cliquez sur `procmon.exe`.
     - Cliquez sur "Oui" pour accepter les termes de la licence si demandé.
   - **Filtrer les événements :**
     - Utilisez les filtres pour observer les activités réseau et système associées au script Python.
     - **Observation :** Notez toute activité suspecte liée à des tentatives de connexion au réseau.

4. **Utilisation de Process Explorer et Autoruns :**
   - **Process Explorer :**
     - Accédez au dossier Sysinternals et double-cliquez sur `procexp.exe`.
     - Identifiez le processus associé à `python.exe` généré par le script.
     - **Observation :** Notez l'utilisation des ressources par ce processus.
   - **Autoruns :**
     - Accédez au dossier Sysinternals et double-cliquez sur `autoruns.exe`.
     - Allez dans l'onglet *Logon* et recherchez l'entrée `SMBThreat`.
     - **Désactiver :** Décochez cette entrée pour empêcher le script de s'exécuter automatiquement au démarrage.

---

# ÉTAPE 6 : Sécurisation du Trafic SMB avec PowerShell et Group Policy sur Windows 10

1. **Désactivation de SMBv1 sur Windows 10 :**
   - **PowerShell :**
     - Ouvrez PowerShell en tant qu'administrateur (`Win + X` > "Windows PowerShell (Admin)").
   - **Désactiver SMBv1 :**
     - Exécutez la commande suivante pour désactiver le protocole SMBv1 :
       ```powershell
       Set-SmbServerConfiguration -EnableSMB1Protocol $false
       ```
     - **Vérification :** Re-tapez la commande pour vérifier que la valeur est bien `False`.

2. **Activer SMB Signing et Encryption :**
   - **Activer le chiffrement SMB :**
     - Dans PowerShell, tapez la commande suivante pour activer le chiffrement SMB :
       ```powershell
       Set-SmbServerConfiguration -EncryptData $true
       ```
   - **Exiger la signature SMB :**
     - Activez la signature SMB pour renforcer la sécurité :
       ```powershell
       Set-SmbServerConfiguration -RequireSecuritySignature $true
       ```
   - **Vérification :** Exécutez la commande suivante pour vérifier les paramètres actuels :
     ```powershell
     Get-SmbServerConfiguration
     ```
     - Confirmez que `EncryptData` est `True` et que `RequireSecuritySignature` est également `True`.

3. **Configuration via Group Policy (facultatif) :**
   - **Group Policy Management :**
     - Si vous avez accès à un domaine Active Directory, vous pouvez configurer une stratégie de groupe pour appliquer ces paramètres de sécurité à tous les ordinateurs du domaine.
     - Ouvrez l'outil de gestion des stratégies de groupe (Group Policy Management).
     - Créez une nouvelle stratégie ou modifiez la stratégie de domaine par défaut pour inclure les paramètres de sécurité SMB.
     - **Application :** Appliquez cette stratégie aux ordinateurs du domaine pour renforcer la sécurité SMB sur tous les systèmes.

---

# ÉTAPE 7 : Nettoyage et Vérification Finale sur Windows 10

1. **Suppression de l'entrée du registre :**
   - **Éditeur de Registre :**
     - Ouvrez `regedit` et naviguez vers `HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Run`.
     - Cliquez avec le bouton droit sur l'entrée `SMBThreat` et sélectionnez "Supprimer".
     - **Vérification :** Assurez-vous que l'entrée est bien supprimée pour empêcher le script de s'exécuter automatiquement au démarrage.

2. **Redémarrer la machine :**
   - Redémarrez la machine virtuelle Windows 10 pour vérifier que le script ne s'exécute plus automatiquement au démarrage.
   - **Vérification :** Ouvrez `Notepad` ou `Chrome` pour vérifier que ces applications s'ouvrent normalement, sans être fermées automatiquement par le script.

3. **Vérification Finale des Paramètres SMB :**
   - **Vérification des Connexions SMB :**
     - Ouvrez PowerShell en tant qu'administrateur et exécutez la commande suivante pour vous assurer que toutes les connexions SMB sont sécurisées :
       ```powershell
       Get-SmbConnection
       ```
   - **Test du SMB Sécurisé :**
     - Essayez d'accéder à un partage SMB sur le réseau et vérifiez que les connexions non sécurisées sont bloquées et que le trafic est bien chiffré.

---

# ÉTAPE 8 : Rédaction du Rapport d'Incident

1. **Résumé de l'Incident :**
   - Décrivez en détail les tentatives de connexion SMB non autorisées que vous avez simulées à l'aide du script Python.
   - Mentionnez les outils utilisés pour détecter et analyser ces activités.

2. **Outils Utilisés :**
   - Détaillez comment Wireshark, Process Monitor, Process Explorer, et PowerShell ont été utilisés pour identifier les tentatives de connexion non autorisées et analyser le trafic réseau.

3. **Résultats :**
   - Décrivez les mesures que vous avez prises pour sécuriser le réseau, notamment la désactivation de SMBv1 et l'activation de la signature et du chiffrement SMB.

4. **Recommandations :**
   - Proposez des mesures pour renforcer la sécurité SMB à l'avenir, comme l'application systématique des politiques de groupe pour exiger le chiffrement et la signature SMB, et la surveillance continue des journaux d'événements.

---

### **Conclusion :**
Ce travail pratique vous a permis de simuler une menace réseau, de configurer une machine Windows 10 pour se défendre contre cette menace, et d'utiliser des outils avancés pour détecter et analyser les activités réseau. Les instructions détaillées et la configuration pas-à-pas garantissent que vous comprenez chaque aspect du processus de sécurisation des connexions SMB, et que vous êtes capable de documenter et résoudre efficacement des incidents de sécurité.
