
---
# EXERCICE 1 - Changer d'Adresse IP Automatiquement Toutes les 5 Secondes
---

## Description

Ce script bash permet de changer automatiquement l'adresse IP de votre interface réseau toutes les 5 secondes sous Kali Linux. Le script désactive l'interface réseau, change l'adresse MAC (ce qui force le serveur DHCP à attribuer une nouvelle adresse IP), puis réactive l'interface. Cela peut être utile dans des situations où vous souhaitez rester anonyme sur un réseau.

## Prérequis

Avant de pouvoir exécuter ce script, vous devez disposer des éléments suivants :

- **Kali Linux** installé sur votre machine.
- **Accès root** ou un utilisateur avec des privilèges `sudo`.
- **Outils requis :** `macchanger` et `ifconfig` (installé via le paquet `net-tools`).

### Installation des Outils Requis

Pour installer les outils nécessaires, exécutez les commandes suivantes :

```bash
sudo apt-get update
sudo apt-get install macchanger net-tools
```

## Utilisation

1. **Création du Script :**

   Créez un fichier script nommé `change_ip.sh` :

   ```bash
   nano change_ip.sh
   ```

   Copiez et collez le code suivant dans le fichier :

   ```bash
   #!/bin/bash

   # Interface réseau (ex : eth0, wlan0)
   INTERFACE="wlan0"

   # Boucle infinie
   while true; do
       # Désactiver l'interface réseau
       sudo ifconfig $INTERFACE down

       # Changer l'adresse MAC
       sudo macchanger -r $INTERFACE

       # Réactiver l'interface réseau
       sudo ifconfig $INTERFACE up

       # Renouveler le bail DHCP pour obtenir une nouvelle adresse IP
       sudo dhclient $INTERFACE

       # Attendre 5 secondes avant de changer l'IP à nouveau
       sleep 5
   done
   ```

2. **Rendre le Script Exécutable :**

   Après avoir enregistré le script, rendez-le exécutable avec la commande suivante :

   ```bash
   chmod +x change_ip.sh
   ```

3. **Exécution du Script :**

   Pour exécuter le script, utilisez la commande suivante :

   ```bash
   sudo ./change_ip.sh
   ```

4. **Arrêter le Script :**

   Pour arrêter le script, appuyez sur `Ctrl+C` dans le terminal où il est en cours d'exécution.

## Notes Importantes

- **Utilisation Éthique :** Changer fréquemment d'adresse IP peut perturber les services réseau et est souvent considéré comme une activité suspecte. Assurez-vous d'avoir l'autorisation d'effectuer ces actions, surtout sur des réseaux que vous ne possédez pas.
- **Conséquences Légales :** Soyez conscient des implications légales de vos actions. Ce guide est fourni à des fins éducatives uniquement, et vous êtes responsable de l'utilisation que vous en faites.


- En suivant ce guide, vous serez en mesure de configurer un changement automatique de votre adresse IP toutes les 5 secondes sur Kali Linux.
  
---------------

---
# Exercice 2 - comment tu peux adapter le processus pour Ubuntu ?
---




### Prérequis

- **Ubuntu installé** sur ta machine.
- **Accès root** ou un utilisateur avec des privilèges `sudo`.
- **Outils requis :** `macchanger` et `ifconfig` (du paquet `net-tools`).

### Étapes pour Ubuntu

1. **Installer les Outils Nécessaires**

   Sur Ubuntu, tu dois d'abord installer `macchanger` et `net-tools` (qui contient `ifconfig`):

   ```bash
   sudo apt-get update
   sudo apt-get install macchanger net-tools
   ```

2. **Création du Script**

   Tu peux suivre les mêmes étapes que précédemment pour créer le script :

   ```bash
   nano change_ip.sh
   ```

   Copie et colle le code suivant dans le fichier :

   ```bash
   #!/bin/bash

   # Interface réseau (ex : eth0, wlan0)
   INTERFACE="wlan0"

   # Boucle infinie
   while true; do
       # Désactiver l'interface réseau
       sudo ifconfig $INTERFACE down

       # Changer l'adresse MAC
       sudo macchanger -r $INTERFACE

       # Réactiver l'interface réseau
       sudo ifconfig $INTERFACE up

       # Renouveler le bail DHCP pour obtenir une nouvelle adresse IP
       sudo dhclient $INTERFACE

       # Attendre 5 secondes avant de changer l'IP à nouveau
       sleep 5
   done
   ```

3. **Rendre le Script Exécutable**

   Comme sur Kali, rends le script exécutable :

   ```bash
   chmod +x change_ip.sh
   ```

4. **Exécuter le Script**

   Exécute le script avec :

   ```bash
   sudo ./change_ip.sh
   ```

5. **Arrêter le Script**

   Pour arrêter le script, appuie sur `Ctrl+C`.

### Notes Spécifiques pour Ubuntu

- **Interface Réseau :** Il est important de vérifier quelle est ton interface réseau. Sur Ubuntu, il se peut que ton interface ne s'appelle pas `wlan0`, mais quelque chose comme `wlp3s0`. Tu peux utiliser la commande `ifconfig` pour identifier le nom correct de l'interface.

   ```bash
   ifconfig
   ```

- **Droits d'Administration :** Assure-toi d'utiliser `sudo` pour toutes les commandes nécessitant des privilèges d'administrateur.

En suivant ces étapes, tu pourras exécuter le script sur une machine Ubuntu tout comme tu le ferais sur Kali Linux.


---
# Exercice 3 - changer l'Adresse MAC au Démarrage
---

Quelques autres idées et scripts intéressants que tu pourrais essayer sur Linux pour améliorer ton anonymat, automatiser certaines tâches ou explorer d'autres fonctionnalités cool :

### 1. **Changer l'Adresse MAC au Démarrage**
   Si tu veux changer automatiquement ton adresse MAC à chaque démarrage de ta machine, tu peux créer un script qui s'exécute au démarrage.

   **Script :**

   ```bash
   #!/bin/bash
   INTERFACE="wlan0"
   
   # Désactiver l'interface réseau
   sudo ifconfig $INTERFACE down

   # Changer l'adresse MAC
   sudo macchanger -r $INTERFACE

   # Réactiver l'interface réseau
   sudo ifconfig $INTERFACE up
   ```

   **Activation au démarrage :**
   
   - Sauvegarde ce script dans `/etc/network/if-pre-up.d/macchange`.
   - Rends le script exécutable :

   ```bash
   sudo chmod +x /etc/network/if-pre-up.d/macchange
   ```

   Ce script s'exécutera automatiquement avant que l'interface réseau ne soit activée au démarrage.

### 2. **Scan Automatique des Réseaux WiFi à Intervalles Réguliers**

   Tu peux automatiser le scan des réseaux WiFi disponibles toutes les X secondes pour voir s'il y a des réseaux disponibles.

   **Script :**

   ```bash
   #!/bin/bash
   while true; do
       # Scanner les réseaux WiFi disponibles
       sudo iwlist wlan0 scan | grep ESSID
       sleep 10
   done
   ```

   Ce script va scanner les réseaux WiFi disponibles toutes les 10 secondes et afficher leurs noms.

### 3. **Monitorer l'Utilisation de la Bande Passante**

   Si tu veux surveiller en temps réel l'utilisation de la bande passante de ton interface réseau, tu peux utiliser un script simple.

   **Script :**

   ```bash
   #!/bin/bash
   INTERFACE="wlan0"

   while true; do
       RX_PREV=$(cat /sys/class/net/$INTERFACE/statistics/rx_bytes)
       TX_PREV=$(cat /sys/class/net/$INTERFACE/statistics/tx_bytes)
       sleep 1
       RX_NEXT=$(cat /sys/class/net/$INTERFACE/statistics/rx_bytes)
       TX_NEXT=$(cat /sys/class/net/$INTERFACE/statistics/tx_bytes)
       RX_DIFF=$((RX_NEXT - RX_PREV))
       TX_DIFF=$((TX_NEXT - TX_PREV))
       RX_RATE=$((RX_DIFF / 1024))
       TX_RATE=$((TX_DIFF / 1024))
       echo "Download: $RX_RATE KB/s | Upload: $TX_RATE KB/s"
   done
   ```

   Ce script va afficher les taux de téléchargement et d'envoi en KB/s en temps réel.

### 4. **Automatisation du Téléchargement via `wget` avec Vérification de Connexion**

   Ce script vérifie si l'ordinateur est connecté à Internet, puis télécharge un fichier via `wget` dès qu'il y a une connexion.

   **Script :**

   ```bash
   #!/bin/bash
   URL="https://example.com/fichier.zip"
   while true; do
       ping -c 1 google.com &> /dev/null
       if [ $? -eq 0 ]; then
           echo "Connexion Internet disponible. Téléchargement en cours..."
           wget $URL
           break
       else
           echo "Pas de connexion Internet. Nouvelle tentative dans 5 secondes."
           sleep 5
       fi
   done
   ```

   Ce script peut être utile pour automatiser le téléchargement de fichiers lorsque la connexion est instable.

### 5. **Script de Surveillance de Ports Ouverts**

   Tu peux automatiser la surveillance de certains ports sur ta machine ou un autre serveur.

   **Script :**

   ```bash
   #!/bin/bash
   IP="127.0.0.1"  # IP à surveiller (localhost dans ce cas)
   PORTS=(22 80 443)  # Liste des ports à surveiller

   while true; do
       for PORT in "${PORTS[@]}"; do
           nc -z -v $IP $PORT &> /dev/null
           if [ $? -eq 0 ]; then
               echo "Le port $PORT est ouvert."
           else
               echo "Le port $PORT est fermé."
           fi
       done
       sleep 10
   done
   ```

   Ce script surveille les ports 22 (SSH), 80 (HTTP), et 443 (HTTPS) sur une machine et indique s'ils sont ouverts ou fermés.

### 6. **Vérification Automatique de Mises à Jour Système**

   Tu peux automatiser la vérification des mises à jour disponibles sur ton système Ubuntu ou Debian et recevoir une notification.

   **Script :**

   ```bash
   #!/bin/bash

   while true; do
       sudo apt-get update -qq
       UPDATES=$(sudo apt-get -s upgrade | grep -P '^\d+ upgraded')
       
       if [ ! -z "$UPDATES" ]; then
           echo "Mises à jour disponibles : $UPDATES"
       else
           echo "Aucune mise à jour disponible."
       fi
       sleep 3600  # Vérifier toutes les heures
   done
   ```

   Ce script va vérifier les mises à jour toutes les heures.

---

Ces scripts peuvent te permettre de personnaliser et d'automatiser diverses tâches sur ton système Linux, améliorant ainsi ton efficacité et ton anonymat. Tu peux les modifier et les adapter selon tes besoins !


# ANNEXE 

Voici un script bash qui change l'adresse IP de l'interface réseau à chaque 5 minutes. Ce script utilise `ifconfig` pour changer l'adresse IP sur une interface réseau donnée. Il doit être exécuté avec des privilèges root pour modifier l'adresse IP :

### Script Bash

```bash
#!/bin/bash

# Interface réseau (ex : eth0 ou wlan0)
INTERFACE="eth0"

# Délai entre chaque changement (5 minutes)
DELAI=300

# Boucle infinie pour changer l'adresse IP toutes les 5 minutes
while true; do
    # Générer une nouvelle adresse IP aléatoire
    IP="192.168.$((RANDOM % 255)).$((RANDOM % 255))"

    # Appliquer la nouvelle adresse IP
    sudo ifconfig $INTERFACE $IP netmask 255.255.255.0 up

    # Afficher la nouvelle adresse IP
    echo "Nouvelle adresse IP pour $INTERFACE : $IP"

    # Attendre 5 minutes avant de changer l'adresse IP
    sleep $DELAI
done
```

### Explications :
- **INTERFACE** : Définissez votre interface réseau (par exemple `eth0`, `wlan0`, etc.).
- **DELAI** : Délai en secondes avant chaque changement (300 secondes = 5 minutes).
- **sudo ifconfig** : Modifie l'adresse IP de l'interface réseau.
- **sleep** : Le script attend 5 minutes avant de changer à nouveau l'adresse IP.

### Exécution :
1. Sauvegardez le script dans un fichier, par exemple `change_ip.sh`.
2. Rendez le fichier exécutable avec la commande : 
   ```bash
   chmod +x change_ip.sh
   ```
3. Exécutez le script avec des privilèges super-utilisateur :
   ```bash
   sudo ./change_ip.sh
   ```

Cela changera automatiquement l'adresse IP toutes les 5 minutes.
