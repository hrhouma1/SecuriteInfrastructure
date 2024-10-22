# Étude de cas #1 : **Problèmes de Connexion VPN sur un Réseau Public : Causes et Solutions**

---

### Table des Matières
1. [Introduction](#introduction)
2. [Mise en Situation](#mise-en-situation)
3. [Causes Potentielles des Problèmes de Connexion VPN](#causes-potentielles-des-problèmes-de-connexion-vpn)
   - [1. Blocage du VPN par le Réseau de l'Hôtel](#blocage-du-vpn-par-le-réseau-de-lhôtel)
   - [2. Restriction de Ports](#restriction-de-ports)
   - [3. Pare-feu de l'Hôtel](#pare-feu-de-lhôtel)
   - [4. Problèmes de Configuration VPN](#problèmes-de-configuration-vpn)
4. [Solutions pour se Connecter via VPN dans un Hôtel](#solutions-pour-se-connecter-via-vpn-dans-un-hôtel)
   - [1. Changer de Protocole VPN](#changer-de-protocole-vpn)
   - [2. Changer de Port](#changer-de-port)
   - [3. Utiliser un VPN Furtif](#utiliser-un-vpn-furtif)
   - [4. Contacter l'Hôtel](#contacter-lhôtel)
5. [Conclusion](#conclusion)

[Retour en haut](#)

---

### Introduction

L'utilisation d'un VPN dans des réseaux publics, comme ceux des hôtels, peut parfois poser des problèmes, même si le VPN fonctionne correctement sur des réseaux domestiques. Les causes de ces échecs de connexion peuvent varier, allant de restrictions imposées par le réseau de l'hôtel à des problèmes de configuration du VPN. Ce guide explore les causes potentielles des blocages de VPN dans les hôtels et propose des solutions détaillées pour contourner ces obstacles.

[Retour en haut](#)

---

### Mise en Situation

Imaginez-vous dans un hôtel, prêt à travailler ou à accéder à des contenus en toute sécurité via votre VPN, comme vous le faites habituellement chez vous. Vous vous connectez au Wi-Fi de l'hôtel, mais votre VPN refuse de se connecter. Vous vous demandez si le problème vient de votre configuration ou si l'hôtel bloque simplement les connexions VPN.

Voyons quelles sont les causes potentielles de ce problème et comment le résoudre.

[Retour en haut](#)

---

### Causes Potentielles des Problèmes de Connexion VPN

#### 1. Blocage du VPN par le Réseau de l'Hôtel

Certains hôtels, pour des raisons de sécurité ou de gestion de bande passante, bloquent délibérément les connexions VPN. Cela peut être fait via des filtres au niveau du réseau ou du pare-feu pour empêcher les utilisateurs d'accéder à des VPN. Cela permet aussi de prévenir des activités comme le streaming, qui consomment beaucoup de bande passante.

#### 2. Restriction de Ports

Les VPN utilisent généralement des ports spécifiques pour établir une connexion sécurisée. Cependant, certains réseaux publics bloquent l'accès à ces ports pour des raisons de sécurité. Par exemple, OpenVPN utilise le port 1194 en UDP, mais ce port pourrait être bloqué par le réseau de l'hôtel, empêchant la connexion.

#### 3. Pare-feu de l'Hôtel

Le pare-feu de l'hôtel pourrait filtrer certains types de trafic, notamment les connexions VPN. Cela est souvent fait pour restreindre des usages spécifiques du réseau, comme le téléchargement massif ou l'accès à des contenus géo-bloqués.

#### 4. Problèmes de Configuration VPN

Bien que votre VPN fonctionne correctement chez vous, il est possible que certaines configurations ne soient pas adaptées à des environnements plus restrictifs, comme les réseaux publics des hôtels. Par exemple, le protocole ou le port que vous utilisez pourrait ne pas être autorisé.

[Retour en haut](#)

---

### Solutions pour se Connecter via VPN dans un Hôtel

#### 1. Changer de Protocole VPN

Certains VPN offrent la possibilité de changer de protocole. Si votre VPN utilise OpenVPN, essayez de basculer vers un autre protocole comme L2TP, IKEv2 ou SSTP. Chaque protocole a ses avantages et certains sont plus susceptibles de fonctionner sur des réseaux publics.

#### 2. Changer de Port

Essayez de changer le port que votre VPN utilise pour se connecter. Vous pouvez, par exemple, configurer votre VPN pour qu'il utilise le port 443, qui est habituellement ouvert car il est utilisé pour le trafic HTTPS sécurisé. Cela pourrait permettre à votre VPN de contourner les restrictions.

#### 3. Utiliser un VPN Furtif

Certains VPN offrent une fonctionnalité furtive ou un mode obfuscation qui permet de déguiser le trafic VPN en trafic normal HTTPS. Cela rend plus difficile pour le réseau de détecter que vous utilisez un VPN, vous permettant ainsi de contourner les restrictions.

#### 4. Contacter l'Hôtel

En dernier recours, vous pouvez essayer de contacter le service technique de l'hôtel pour leur demander s'ils bloquent les VPN. Dans certains cas, ils peuvent être en mesure d'autoriser l'accès si vous en faites la demande.

[Retour en haut](#)

---

### Conclusion

Les problèmes de connexion VPN dans les hôtels peuvent être frustrants, mais ils ne sont pas insurmontables. En ajustant la configuration de votre VPN ou en utilisant des fonctionnalités avancées comme le mode furtif, vous pouvez contourner la plupart des restrictions imposées par les réseaux publics. Si tout échoue, il peut être utile de contacter l'hôtel pour clarifier la situation.

[Retour en haut](#)


# Anenxe 1 - exemple de script shell qui vous permet de changer dynamiquement le port ou d'activer un mode furtif pour un VPN utilisant OpenVPN. 

*Ce script vérifie si OpenVPN est installé et propose des options pour changer le port ou activer un protocole furtif.*

### Script Shell : `change_vpn_config.sh`

```bash
#!/bin/bash

# Vérification si OpenVPN est installé
if ! command -v openvpn &> /dev/null
then
    echo "OpenVPN n'est pas installé. Veuillez l'installer avant d'exécuter ce script."
    exit
fi

# Variables de configuration du fichier OpenVPN
CONFIG_FILE="/etc/openvpn/client.conf"  # Remplacez avec le chemin de votre fichier config OpenVPN

# Fonction pour changer le port
change_port() {
    echo "Entrez le nouveau port pour OpenVPN (par exemple, 443 pour HTTPS): "
    read -r new_port

    if grep -q "^remote " "$CONFIG_FILE"; then
        sed -i "s/remote .* [0-9]\+$/remote $(grep -o 'remote [^ ]*' "$CONFIG_FILE" | awk '{print $2}') $new_port/" "$CONFIG_FILE"
        echo "Port changé avec succès à $new_port dans la configuration VPN."
    else
        echo "Ligne 'remote' non trouvée dans le fichier de configuration."
    fi
}

# Fonction pour activer le mode furtif
enable_stealth_mode() {
    echo "Activation du mode furtif (obfuscation)..."

    # Ajout d'options de protocole furtif spécifiques à OpenVPN
    echo "Ajout des paramètres de furtivité au fichier de configuration..."

    # Vous pouvez personnaliser ces options selon le protocole furtif supporté par votre VPN
    echo "scramble obfuscate" >> "$CONFIG_FILE"
    echo "Paramètres de furtivité ajoutés."
}

# Menu principal
echo "=========================================="
echo "   Script de configuration OpenVPN        "
echo "=========================================="
echo "1. Changer de port"
echo "2. Activer le mode furtif (Obfuscation)"
echo "3. Quitter"
echo "Choisissez une option: "
read -r option

case $option in
    1)
        change_port
        ;;
    2)
        enable_stealth_mode
        ;;
    3)
        echo "Sortie du script."
        exit 0
        ;;
    *)
        echo "Option invalide. Veuillez réessayer."
        ;;
esac
```

### Explication du script :

1. **Vérification d'OpenVPN** : Le script commence par vérifier si OpenVPN est installé. Si ce n'est pas le cas, il vous invite à l'installer avant de poursuivre.

2. **Changer de Port** : Si vous choisissez l'option 1, le script vous demandera d'entrer un nouveau port. Il mettra à jour le port dans la configuration OpenVPN (recherche de la ligne commençant par `remote` et modifie le port).

3. **Activer le Mode Furtif** : L'option 2 ajoute la ligne `scramble obfuscate` au fichier de configuration, ce qui peut activer le mode furtif dans certains services VPN compatibles avec OpenVPN.

4. **Personnalisation** : Vous pouvez ajuster les paramètres comme le chemin vers votre fichier de configuration OpenVPN ou les options spécifiques pour le mode furtif, selon votre fournisseur VPN.

### Exécution du script :

1. Sauvegardez le script dans un fichier, par exemple : `change_vpn_config.sh`.
2. Donnez les permissions d'exécution :  
   ```bash
   chmod +x change_vpn_config.sh
   ```
3. Exécutez le script :  
   ```bash
   ./change_vpn_config.sh
   ```

Cela vous permettra de facilement changer le port ou d'activer un protocole furtif dans votre configuration OpenVPN !


# Annexe 2 - Est-ce que les VPNs sont compatibles avec Linux ?



Oui, les VPN sont tout à fait compatibles avec Linux, et il existe de nombreuses options pour utiliser des VPN sur des distributions Linux. Voici quelques détails concernant la compatibilité et les outils disponibles pour Linux :

### 1. **Clients VPN Open Source sur Linux**
   - **OpenVPN** : C'est l'un des protocoles VPN les plus populaires et il est largement supporté sur Linux. Il est open-source et propose des solutions pour la plupart des besoins en VPN. Vous pouvez l'installer et le configurer directement via la ligne de commande.
   - **WireGuard** : Un VPN moderne et extrêmement rapide, également open-source. Il est réputé pour sa simplicité, sa sécurité et ses performances élevées. Il est intégré nativement dans le noyau Linux à partir de la version 5.6.
   - **IPsec (avec strongSwan)** : IPsec est un autre protocole VPN courant, souvent utilisé dans des environnements professionnels. strongSwan est une implémentation IPsec open-source compatible avec Linux.
   - **L2TP/IPsec** : Un autre protocole VPN que vous pouvez utiliser sur Linux avec des outils comme `xl2tpd` et `strongSwan` pour établir des connexions VPN.

### 2. **Clients VPN Commercials sur Linux**
   Beaucoup de services VPN commerciaux offrent des clients natifs pour Linux ou des guides détaillés sur comment configurer leur service sur Linux en utilisant des clients comme OpenVPN ou WireGuard.
   - **NordVPN** : Ils offrent un client Linux et supportent OpenVPN et WireGuard (via leur protocole propriétaire NordLynx).
   - **ExpressVPN** : Ils ont un client natif pour Linux ainsi que des guides pour configurer OpenVPN manuellement.
   - **ProtonVPN** : Un autre fournisseur qui propose un client Linux natif ainsi qu'une option OpenVPN.

### 3. **Commandes de Gestion VPN sur Linux**
   - **Network Manager** : Les distributions Linux comme Ubuntu ou Fedora ont souvent le **Network Manager**, un outil graphique qui permet de configurer et de gérer les connexions VPN. Vous pouvez l'utiliser pour configurer OpenVPN, L2TP/IPsec, ou PPTP.
   - **Terminal** : Vous pouvez gérer vos VPN directement depuis le terminal en utilisant des clients comme OpenVPN, WireGuard ou strongSwan. Cela vous donne plus de flexibilité et de contrôle.

### 4. **Installation et Configuration sur Linux**

Voici des exemples d'installation de VPN sur Linux :

#### Pour OpenVPN :
```bash
sudo apt update
sudo apt install openvpn
```
Ensuite, vous pouvez utiliser un fichier de configuration `.ovpn` fourni par votre service VPN :
```bash
sudo openvpn --config your_vpn_config.ovpn
```

#### Pour WireGuard :
```bash
sudo apt install wireguard
```
Ensuite, configurez et démarrez votre VPN avec les fichiers de configuration WireGuard.

### 5. **Compatibilité avec les Protocoles Furtifs**
   Si vous avez besoin d'un mode furtif pour contourner les blocages VPN (par exemple dans certains réseaux publics ou pays), certains services VPN proposent des configurations furtives via OpenVPN ou WireGuard qui sont compatibles avec Linux.

En conclusion, **Linux est une excellente plateforme pour utiliser un VPN** grâce à la flexibilité et aux nombreuses options disponibles pour configurer et gérer des connexions VPN, que ce soit via des clients open-source ou des services commerciaux.
