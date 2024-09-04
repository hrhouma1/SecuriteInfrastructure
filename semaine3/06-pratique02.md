### **Démonstration complète : Capture et analyse du trafic SMB 1.0 avec Wireshark**

Dans cette partie de la démonstration, vous allez capturer le trafic SMB 1.0 non chiffré entre un client Windows et un serveur Windows pour montrer à vos étudiants les risques liés à l’utilisation de SMB 1.0. Vous utiliserez Wireshark pour analyser les paquets réseau et lire les données transférées en clair.

---

### **Objectif :**
- **Montrer** comment SMB 1.0 transfère les fichiers sans chiffrement.
- **Démontrer** la facilité avec laquelle un attaquant peut intercepter et lire les données.

---

### **Étape 1 : Préparation des machines et réactivation de SMB 1.0**

1. **Activer SMB 1.0 sur le serveur Windows** :

   Exécutez cette commande sur votre serveur Windows pour réactiver SMB 1.0 :

   ```powershell
   Set-SmbServerConfiguration –EnableSMB1Protocol $true
   ```

   **Note** : SMB 1.0 est vulnérable et doit être réactivé uniquement pour la démonstration.

---

### **Étape 2 : Installation de Wireshark sur Kali Linux (si nécessaire)**

Wireshark est généralement préinstallé sur Kali Linux. Si ce n'est pas le cas, installez-le en suivant ces étapes :

1. **Mettez à jour Kali Linux** pour s'assurer que vous avez la dernière version des paquets :

   ```bash
   sudo apt update && sudo apt upgrade
   ```

2. **Installez Wireshark** :

   ```bash
   sudo apt install wireshark
   ```

   Lors de l'installation, acceptez les permissions supplémentaires pour que Wireshark puisse capturer le trafic réseau en tant qu'utilisateur.

---

### **Étape 3 : Démarrer la capture du trafic SMB avec Wireshark**

1. **Ouvrir Wireshark** sur Kali Linux :
   - Tapez `wireshark` dans le terminal de Kali pour lancer l'interface graphique de Wireshark.

2. **Sélectionner l'interface réseau** :
   - Dans Wireshark, sélectionnez l'interface réseau que vous utilisez pour la connexion au réseau (souvent `eth0` pour Ethernet ou `wlan0` pour Wi-Fi).
   - Cliquez sur le bouton vert **"Start capturing packets"** pour commencer à capturer les paquets sur l'interface sélectionnée.

   ![Start Capturing in Wireshark](https://www.wireshark.org/docs/wsug_html_chunked/wsug_graphics/ws-start-capturing.png)

---

### **Étape 4 : Effectuer un transfert de fichier avec SMB 1.0**

1. **Depuis votre machine Windows 10 (client)** :
   - Ouvrez l'**Explorateur de fichiers**.
   - Dans la barre d'adresse, tapez l'adresse du dossier partagé sur le serveur Windows. Par exemple, entrez `\\192.168.1.10\SharedFolder`, où `192.168.1.10` est l'adresse IP du serveur et `SharedFolder` est le nom du dossier partagé.
   
2. **Créer un fichier texte** :
   - Créez un simple fichier texte (par exemple, `test_smb.txt`) avec des données visibles telles que `Ceci est un test pour SMB 1.0`.

3. **Draguez et déposez** le fichier dans le dossier partagé :
   - Depuis l'Explorateur de fichiers, faites glisser le fichier `test_smb.txt` vers le dossier partagé `SharedFolder` sur le serveur Windows.

4. **Attendez que le fichier soit copié** sur le serveur.

---

### **Étape 5 : Analyser le trafic SMB capturé dans Wireshark**

1. **Revenir à Wireshark** :
   - Pendant que vous copiez le fichier depuis Windows 10 vers le serveur, Wireshark devrait capturer une grande quantité de paquets réseau.

2. **Filtrer le trafic SMB uniquement** :
   - Dans la barre de filtre de Wireshark en haut de la fenêtre, tapez **`smb`** puis appuyez sur **Entrée**. Cela va filtrer tous les paquets capturés et n'afficher que ceux relatifs au protocole SMB.
   
   ![Filter SMB packets in Wireshark](https://www.wireshark.org/docs/wsug_html_chunked/wsug_graphics/ws-filter-smb.png)

3. **Identifier les paquets liés au transfert de fichier** :
   - Cherchez des paquets **SMB Write AndX Request/Response**. Ces paquets contiennent les données du fichier que vous venez de transférer.

4. **Lire le contenu du fichier dans Wireshark** :
   - Sélectionnez un paquet **SMB Write AndX Request**. Dans le panneau de détail des paquets, vous verrez une section **"Data"** ou **"Payload"** qui contient les données transférées.
   - Si vous avez utilisé un fichier texte comme `test_smb.txt`, vous verrez le contenu du fichier (par exemple, `Ceci est un test pour SMB 1.0`) en clair dans cette section.

   ![View data in Wireshark](https://osqa-ask.wireshark.org/upfiles/file%20data%20hex%20view.png)

5. **Expliquer aux étudiants** :
   - Montrez comment les données du fichier sont lisibles en clair. Expliquez que SMB 1.0 ne chiffre pas les données en transit, ce qui rend l'interception très facile pour un attaquant.

---

### **Étape 6 : Sauvegarder la capture (facultatif)**

1. **Sauvegarder la capture** pour analyse ultérieure :
   - Cliquez sur **File > Save As** dans le menu de Wireshark.
   - Donnez un nom à votre capture, par exemple `SMB1_traffic_capture.pcap`, et enregistrez-la pour une utilisation ultérieure.

---

### **Résumé des actions dans cette section :**

1. **Installer Wireshark** si nécessaire.
2. **Démarrer la capture du trafic réseau** avec Wireshark.
3. **Effectuer un transfert de fichier** avec SMB 1.0 activé sur le serveur Windows.
4. **Filtrer et analyser le trafic SMB** pour montrer que les données sont transférées en clair.
5. **Expliquer aux étudiants** pourquoi SMB 1.0 est vulnérable aux attaques de type "man-in-the-middle".

---

### **Étape suivante : Passer à SMB 3.1.1**

Maintenant que vous avez montré la vulnérabilité de SMB 1.0, vous pouvez passer à SMB 3.1.1 avec chiffrement et capturer à nouveau le trafic pour montrer que les données sont désormais sécurisées. Vous utiliserez un processus similaire avec Wireshark, mais cette fois, le contenu du fichier sera chiffré et illisible.

---

### **Conclusion**

En suivant ces étapes, vous pouvez démontrer de manière concrète et détaillée à vos étudiants les failles de sécurité de **SMB 1.0**, et pourquoi il est essentiel de migrer vers une version sécurisée du protocole, comme **SMB 3.1.1** avec chiffrement AES.
