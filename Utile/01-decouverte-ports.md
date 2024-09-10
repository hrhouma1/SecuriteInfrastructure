# 01 - Découverte de ports, services, et la gestion des processus sur différents systèmes d'exploitation.

| Commande | Description | Environnement Supporté |
|----------|-------------|------------------------|
| netstat -tuln | Liste les ports TCP/UDP ouverts et les services en écoute | Linux, macOS (ancien), Windows |
| netstat -tuln \| grep :8080 | Filtre pour afficher les ports en écoute sur 8080 | Linux, macOS (ancien), Windows |
| ss -tuln | Affiche les ports ouverts et en écoute (remplace netstat) | Linux |
| ss -tuln \| grep :8080 | Filtre pour afficher le port 8080 parmi les connexions actives | Linux |
| lsof -i | Liste les connexions réseau et les processus associés | Linux, macOS |
| lsof -i :8080 | Filtre pour afficher les processus utilisant le port 8080 | Linux, macOS |
| nmap <ip ou hostname> | Scanne les ports ouverts sur une machine | Linux, macOS, Windows |
| nmap -p 8080 <ip ou hostname> | Scanne uniquement le port 8080 sur une machine | Linux, macOS, Windows |
| nmap -sV <ip ou hostname> | Scanne et tente de déterminer les versions des services | Linux, macOS, Windows |
| ps aux | Affiche tous les processus en cours | Linux, macOS |
| ps aux \| grep apache | Filtre pour trouver un processus par nom | Linux, macOS |
| kill 1234 | Tuer un processus avec un PID spécifique | Linux, macOS |
| kill -15 1234 | Terminer un processus en douceur avec SIGTERM | Linux, macOS |
| kill -9 1234 | Forcer l'arrêt immédiat d'un processus avec SIGKILL | Linux, macOS |
| killall apache | Tuer tous les processus nommés "apache" | Linux, macOS |
| tcpdump -i eth0 | Capture le trafic sur l'interface eth0 | Linux, macOS |
| tcpdump -i eth0 port 8080 | Capture le trafic sur le port 8080 de l'interface eth0 | Linux, macOS |
| netcat -l -p 8080 | Écoute sur le port 8080 | Linux, macOS |
| netcat <ip> 8080 | Se connecte au port 8080 d'une machine distante | Linux, macOS |
| iptables -L | Liste les règles du pare-feu | Linux |
| ufw status | Affiche l'état du pare-feu UFW | Linux (Ubuntu) |
| curl http://localhost:8080 | Teste une connexion HTTP sur le port 8080 | Linux, macOS, Windows |
| wget http://example.com/file | Télécharge un fichier depuis un serveur web | Linux, macOS, Windows |
| ifconfig | Affiche la configuration réseau (obsolète) | Linux, macOS |
| ip addr | Affiche la configuration réseau (remplace ifconfig) | Linux |
| traceroute example.com | Trace la route vers un hôte | Linux, macOS |
| dig example.com | Effectue une requête DNS | Linux, macOS |
| wireshark | Analyse de paquets réseau (interface graphique) | Linux, macOS, Windows |
| netstat -r | Affiche la table de routage | Linux, macOS, Windows |
| route print | Affiche la table de routage (Windows) | Windows |
| arp -a | Affiche la table ARP | Linux, macOS, Windows |



# 02 - Liste des commandes les plus utilisées :

```bash
# Affiche les ports TCP/UDP ouverts et les services en écoute
netstat -tuln

# Affiche les ports ouverts et en écoute (remplace netstat, plus rapide)
ss -tuln

# Liste toutes les connexions réseau et les processus associés
lsof -i

# Filtre pour afficher les processus utilisant un port spécifique, ici 8080
lsof -i :8080

# Scanne les ports ouverts sur une machine (remplace <ip ou hostname> par l'adresse cible)
nmap <ip ou hostname>

# Scanne uniquement le port 8080 sur une machine spécifique
nmap -p 8080 <ip ou hostname>

# Affiche tous les processus en cours sur le système
ps aux

# Filtre pour trouver un processus spécifique (par exemple "apache")
ps aux | grep apache

# Tuer un processus en utilisant son PID (par exemple PID 1234)
kill 1234

# Envoie un signal SIGTERM pour terminer proprement un processus
kill -15 1234

# Forcer l'arrêt d'un processus en utilisant SIGKILL (arrêt immédiat)
kill -9 1234

# Tuer tous les processus portant le nom "apache"
killall apache

# Tester la connexion à un service HTTP sur un port spécifique (par exemple 8080)
curl http://localhost:8080

# Télécharger un fichier depuis un serveur (par exemple "file.zip" depuis example.com)
wget http://example.com/file.zip

# Affiche la configuration des interfaces réseau (adresse IP, état des interfaces)
ip a

# Affiche la table de routage réseau (les chemins pris par les paquets réseau)
ip route

# Capture le trafic réseau sur une interface spécifique (par exemple eth0)
tcpdump -i eth0

# Capture uniquement le trafic réseau sur le port 8080 via l'interface eth0
tcpdump -i eth0 port 8080

# Vérifie l'état actuel du pare-feu UFW (pour Ubuntu et certaines distributions Linux)
sudo ufw status

# Autorise le port 8080 dans le pare-feu UFW
sudo ufw allow 8080

# Bloque le port 8080 dans le pare-feu UFW
sudo ufw deny 8080
```

