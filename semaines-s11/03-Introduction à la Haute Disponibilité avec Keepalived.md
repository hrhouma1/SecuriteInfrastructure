## 🌐 Introduction à la Haute Disponibilité avec Keepalived

La haute disponibilité (HA) est un concept visant à minimiser les interruptions de service en assurant qu’une application ou un service reste accessible, même en cas de défaillance matérielle ou logicielle. **Keepalived** est un service qui facilite cette haute disponibilité en assurant le basculement (failover) entre plusieurs serveurs.

Keepalived utilise le **protocole VRRP (Virtual Router Redundancy Protocol)** pour gérer les adresses IP flottantes, permettant à un serveur de prendre le relais automatiquement si le serveur principal devient inactif.

---

### 🖥️ Vérification de la Version d'Ubuntu

Avant de commencer l'installation et la configuration de Keepalived, il est important de connaître la version de votre système d'exploitation pour assurer la compatibilité et suivre les instructions appropriées.

Pour obtenir les informations détaillées sur votre version d'Ubuntu, vous pouvez utiliser la commande suivante dans le terminal :

```bash
lsb_release -a
```

Cela affichera les informations de version d'Ubuntu, y compris la version 22.04 si vous êtes sur cette version spécifique.

---

#### Exemple de sortie

Pour Ubuntu 22.04, la sortie devrait ressembler à ceci :

```plaintext
No LSB modules are available.
Distributor ID: Ubuntu
Description:    Ubuntu 22.04.1 LTS
Release:        22.04
Codename:       jammy
```

Vous pouvez également utiliser :

```bash
cat /etc/os-release
```

#### Exemple de sortie avec `cat /etc/os-release`

```plaintext
NAME="Ubuntu"
VERSION="22.04.1 LTS (Jammy Jellyfish)"
ID=ubuntu
ID_LIKE=debian
PRETTY_NAME="Ubuntu 22.04.1 LTS"
VERSION_ID="22.04"
VERSION_CODENAME=jammy
UBUNTU_CODENAME=jammy
```

Ces commandes vous donneront tous les détails sur la version et le nom de code de votre système d'exploitation Ubuntu.

---

### 🚀 Concepts Clés de Keepalived

1. **VRRP (Virtual Router Redundancy Protocol)** :
   - VRRP permet la redondance des routeurs virtuels en attribuant une IP virtuelle flottante entre plusieurs serveurs.
   - Keepalived utilise ce protocole pour permettre à un autre serveur de récupérer l’IP virtuelle lorsque le serveur principal tombe.

2. **IP Flottante (VIP)** :
   - Une IP virtuelle qui peut basculer entre différents serveurs pour assurer que le service reste accessible.

3. **Nœud Maître et Nœud de Secours** :
   - **Maître** : Serveur principal qui détient l’IP virtuelle et gère les requêtes.
   - **Secours** : Serveur de secours qui prend automatiquement le relais si le maître tombe.

4. **Scripts de Surveillance** :
   - Keepalived peut exécuter des scripts personnalisés pour surveiller l'état des services et décider du basculement.

---

### ⚙️ Installation et Configuration de Keepalived

#### Pré-requis

- **Deux serveurs Ubuntu 22.04** (ou plus) connectés au même réseau.
- **Accès root** ou des privilèges sudo pour configurer les serveurs.
- **Connaissance de la version du système d'exploitation** pour s'assurer de la compatibilité (comme vérifié précédemment).

#### Installation de Keepalived

Sur un système Ubuntu 22.04, exécutez :

```bash
sudo apt update
sudo apt install keepalived
```

---

### 📝 Configuration de Base de Keepalived

La configuration de Keepalived se trouve dans le fichier `/etc/keepalived/keepalived.conf`. Voici les étapes pour configurer un cluster de haute disponibilité avec une IP flottante.

#### 1. Configurer le Serveur Maître

1. **Ouvrez le fichier de configuration :**

   ```bash
   sudo nano /etc/keepalived/keepalived.conf
   ```

2. **Ajoutez la configuration suivante** pour définir l'IP virtuelle et les paramètres VRRP :

   ```bash
   vrrp_instance VI_1 {
       state MASTER                      # Indique que ce serveur est le maître
       interface eth0                    # Interface réseau à surveiller (remplacez par votre interface réseau)
       virtual_router_id 51              # ID unique du routeur virtuel (doit être identique sur les deux serveurs)
       priority 100                      # Priorité (le maître doit avoir une priorité plus élevée)
       advert_int 1                      # Intervalle d'annonce en secondes

       authentication {
           auth_type PASS                # Méthode d'authentification
           auth_pass 1234                # Mot de passe partagé
       }

       virtual_ipaddress {
           192.168.1.100                 # Adresse IP virtuelle partagée (remplacez par votre IP)
       }
   }
   ```

3. **Enregistrez et fermez le fichier**.

4. **Démarrez et activez Keepalived** :

   ```bash
   sudo systemctl start keepalived
   sudo systemctl enable keepalived
   ```

#### 2. Configurer le Serveur de Secours

Répétez les étapes ci-dessus sur le serveur de secours avec une petite modification :

- Remplacez `state MASTER` par `state BACKUP`.
- Réduisez la **priorité** à un nombre inférieur à celui du maître (par exemple, `priority 90`).
- Assurez-vous que les autres paramètres (comme `virtual_router_id`, `authentication`, `virtual_ipaddress`) sont identiques.

---

### 🔄 Fonctionnement du Basculement (Failover) avec Keepalived

1. **Basculement automatique** : Si le serveur maître devient inactif (défaillance matérielle, arrêt du service Keepalived, etc.), le serveur de secours détecte l'absence d'annonces VRRP du maître et prend l’IP virtuelle.

2. **Retour automatique** : Si le serveur maître revient en ligne, il peut reprendre automatiquement l'IP virtuelle en fonction des priorités configurées.

---

### 📊 Surveillance et Scripts de Contrôle de Service avec Keepalived

Keepalived permet également de configurer des **scripts de surveillance** pour vérifier l’état des services. Par exemple, vous pouvez surveiller un serveur web en configurant un script de vérification.

1. **Créer un script de surveillance** (par exemple, pour vérifier si Nginx est actif) :

   ```bash
   sudo nano /etc/keepalived/check_nginx.sh
   ```

   Ajoutez le contenu suivant au script :

   ```bash
   #!/bin/bash
   if ! systemctl is-active --quiet nginx; then
       exit 1
   fi
   exit 0
   ```

2. **Rendre le script exécutable** :

   ```bash
   sudo chmod +x /etc/keepalived/check_nginx.sh
   ```

3. **Ajouter le script à la configuration Keepalived** :

   Dans le fichier `/etc/keepalived/keepalived.conf`, ajoutez le bloc suivant dans votre instance VRRP :

   ```bash
   vrrp_script chk_nginx {
       script "/etc/keepalived/check_nginx.sh"
       interval 2                         # Intervalle de vérification en secondes
   }

   track_script {
       chk_nginx
   }
   ```

   Avec cette configuration, Keepalived surveille le service Nginx toutes les 2 secondes et bascule vers le serveur de secours si Nginx échoue.

---

### 🛠️ Cas d’Utilisation et Scénarios Pratiques

- **Serveur Web Haute Disponibilité** : Utilisez Keepalived pour assurer la disponibilité d’un site web en ajoutant des nœuds de secours pour prendre le relais en cas de panne.

- **Équilibrage de Charge et Proxy** : En combinaison avec un outil comme HAProxy, Keepalived peut assurer l’équilibrage de charge et la redondance en attribuant une IP flottante entre plusieurs instances de proxy.

---

### 🖥️ Vérification de la Version d'Ubuntu (Rappel)

Pour vous assurer que vous travaillez sur la bonne version du système d'exploitation, vous pouvez vérifier la version d'Ubuntu installée sur vos serveurs.

**Utilisez la commande suivante dans le terminal :**

```bash
lsb_release -a
```

**Exemple de sortie pour Ubuntu 22.04 :**

```plaintext
No LSB modules are available.
Distributor ID: Ubuntu
Description:    Ubuntu 22.04.1 LTS
Release:        22.04
Codename:       jammy
```

**Ou utilisez :**

```bash
cat /etc/os-release
```

**Exemple de sortie avec `cat /etc/os-release` :**

```plaintext
NAME="Ubuntu"
VERSION="22.04.1 LTS (Jammy Jellyfish)"
ID=ubuntu
ID_LIKE=debian
PRETTY_NAME="Ubuntu 22.04.1 LTS"
VERSION_ID="22.04"
VERSION_CODENAME=jammy
UBUNTU_CODENAME=jammy
```

Ces commandes vous donneront tous les détails sur la version et le nom de code de votre système d'exploitation Ubuntu, ce qui est essentiel pour garantir la compatibilité avec Keepalived et suivre les instructions spécifiques à votre version.

---

### 📌 Conclusion

Keepalived est une solution fiable pour les environnements haute disponibilité sous Linux, offrant des fonctionnalités puissantes pour la redondance et le basculement avec une configuration relativement simple. Grâce à son intégration avec VRRP et ses capacités de surveillance, Keepalived garantit la continuité des services critiques en redirigeant automatiquement le trafic en cas de défaillance.

Assurez-vous de connaître la version de votre système d'exploitation Ubuntu (comme Ubuntu 22.04) pour suivre les instructions appropriées et garantir la compatibilité avec Keepalived.


