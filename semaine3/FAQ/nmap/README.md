# Pratiques NMAP (YOUTUBE)
- https://www.youtube.com/watch?v=4t4kBkMsDbQ&ab_channel=NetworkChuck
- https://www.youtube.com/watch?v=NYgDzO8iQJ0&ab_channel=An0nAli
- https://www.youtube.com/watch?v=W7076RPIgfQ&ab_channel=GetCyber
- https://www.youtube.com/watch?v=-d2vrayQRfE&ab_channel=codefm
- https://www.youtube.com/watch?v=j5Yw3ZsM-2w&ab_channel=PentestsandTech


# Références : 
- https://medium.com/@riteshs4hu/a-step-by-step-guide-to-nmap-scanning-for-beginners-45d00dd759f9
- https://bencane.com/ten-nmap-commands-every-sysadmin-should-know-2390c559a7c3
  
### Guide Pas à Pas pour Scanner avec Nmap pour les Débutants

#### Introduction

Nmap, également connu sous le nom de Network Mapper, est un outil gratuit et open-source qui aide à l'exploration, à la gestion et à l'audit de la sécurité des réseaux. C'est un scanner de réseau polyvalent qui peut identifier les hôtes et les services sur un réseau informatique et détecter les vulnérabilités de sécurité sur un réseau. Dans cet article, nous discuterons des différentes étapes du scan avec Nmap, de la spécification des cibles, de la découverte des hôtes, de la spécification des ports et de l'ordre des scans, de la détection des versions des services, du scan de scripts, de la détection des systèmes d'exploitation et de la sortie des résultats. Nous fournirons également des exemples de commandes pour chaque étape.

#### Options courantes utilisées avec Nmap :

- **-v (verbose) / -vv (double verbose)** : Fournit des détails supplémentaires pendant le scan, tels que la cible en cours de scan, l'état de chaque port et les erreurs éventuelles.
- **-p (spécification de port)** : Permet de spécifier les ports à scanner en utilisant une liste de ports séparés par des virgules ou une plage de ports.
- **-sS (scan SYN TCP)** : Type de scan par défaut de Nmap, envoie des paquets SYN aux ports de la cible pour déterminer s'ils sont ouverts ou non.
- **-sT (scan de connexion TCP)** : Utilise une connexion TCP complète pour déterminer si les ports de la cible sont ouverts ou non.
- **-sU (scan UDP)** : Envoie des paquets UDP aux ports de la cible pour déterminer s'ils sont ouverts ou non.
- **-A (détection de l'OS et des versions)** : Active la détection des versions, la détection de l'OS, le scan de scripts et le traceroute en une seule commande.
- **-O (détection de l'OS)** : Permet la détection du système d'exploitation de la cible.
- **--script (scan de scripts)** : Permet d'exécuter des scripts contre la cible, tels que les scanners de vulnérabilités ou les attaques par force brute.
- **-iL (fichier d'entrée)** : Permet de spécifier un fichier contenant une liste de cibles à scanner.
- **-oA (fichier de sortie)** : Permet de spécifier un fichier de sortie pour les résultats du scan, pouvant être en plusieurs formats, y compris XML, grepable et normal.

#### Spécification des cibles

La spécification des cibles est une étape cruciale de l'utilisation de Nmap. Elle permet de spécifier les hôtes, réseaux ou adresses IP que vous souhaitez scanner. Cela peut être fait de différentes manières, y compris en spécifiant des adresses IP individuelles, une notation CIDR pour les plages d'adresses IP ou des noms d'hôtes. Nmap supporte également divers formats de liste de cibles, tels qu'un fichier contenant une liste d'hôtes ou une entrée provenant de l'entrée standard.

Voici quelques options courantes pour la spécification des cibles :

1. **Noms d'hôtes ou adresses IP**.
   Une des façons les plus simples de spécifier la cible est de fournir le nom d'hôte ou l'adresse IP de l'hôte. Par exemple :
   ```bash
   nmap -v scanme.nmap.org
   nmap -v 192.168.1.1
   ```

2. **Notation CIDR**.
   La notation CIDR (Classless Inter-Domain Routing) permet de spécifier une plage d'adresses IP en utilisant une longueur de préfixe. Par exemple :
   ```bash
   nmap -v -p 80,443 example.com/24
   nmap -v 192.168.1.1/24
   nmap 192.168.1.1/24
   ```

3. **Plage d'adresses IP**.
   Nous pouvons également spécifier une plage d'adresses IP en utilisant la notation par tiret. Par exemple, pour scanner toutes les adresses IP entre 192.168.0.1 et 192.168.0.254, nous pouvons utiliser la commande suivante :
   ```bash
   nmap 192.168.0.1-254
   nmap 10.0.0-255.1-254
   ```

4. **Entrée depuis une liste d'hôtes/réseaux**.
   L'option `-iL` permet à l'utilisateur de lire une liste d'hôtes et de réseaux à partir d'un fichier. Par exemple, disons que nous avons un fichier nommé `targets.txt`, qui contient une liste d'hôtes et de réseaux. Nous pouvons utiliser la commande suivante pour scanner ces cibles :
   ```bash
   nmap -v -iL targets.txt
   nmap -iL targets.txt
   ```

5. **Choisir des cibles aléatoires**.
   L'option `-iR` permet à l'utilisateur de choisir des cibles aléatoires. Par exemple :
   ```bash
   nmap -iR 3
   nmap -v -p 80,443,22,21,445 -iR 3
   ```

6. **Exclure des hôtes/réseaux**.
   L'option `--exclude` permet à l'utilisateur d'exclure certains hôtes et réseaux du scan. Par exemple, pour exclure les hôtes `192.168.0.1` et `192.168.0.2` du scan, nous pouvons utiliser la commande suivante :
   ```bash
   nmap 192.168.0.0/24 --exclude 192.168.0.1,192.168.0.2
   ```

7. **Exclure une liste depuis un fichier**.
   L'option `--excludefile` permet à l'utilisateur d'exclure une liste d'hôtes et de réseaux depuis un fichier. Par exemple, disons que nous avons un fichier nommé `exclude.txt`, qui contient une liste d'hôtes et de réseaux à exclure. Nous pouvons utiliser la commande suivante pour exclure ces cibles du scan :
   ```bash
   nmap 192.168.0.0/24 --excludefile exclude.txt
   ```

#### Découverte des hôtes

La découverte des hôtes est essentielle pour effectuer un scan de réseau, que ce soit pour des tests d'intrusion internes ou externes. Ce processus consiste à identifier quels hôtes sont actifs et fonctionnent sur un réseau, ainsi qu'à déterminer leurs adresses IP et d'autres informations critiques. Plusieurs méthodes et commandes Nmap peuvent être utilisées pour la découverte des hôtes, en fonction du type de test effectué.

Pour les tests d'intrusion internes, les techniques suivantes de Nmap peuvent être utilisées pour la découverte des hôtes :

- **Scan ARP**
- **Scan ICMP**
- **Scan TCP (SYN/ACK)**
- **Scan UDP**

Un scan ARP envoie des paquets ARP pour déterminer quels hôtes sont actifs sur le réseau. Un scan ICMP envoie des paquets ICMP pour déterminer quels hôtes sont actifs, tandis qu'un scan TCP SYN/ACK envoie des paquets TCP SYN/ACK pour identifier les hôtes qui écoutent sur des ports spécifiques. Un scan UDP envoie des paquets UDP pour déterminer quels hôtes acceptent le trafic UDP.

Pour les tests d'intrusion externes, les techniques suivantes de Nmap peuvent être utilisées pour la découverte des hôtes :

- **Scan ICMP**
- **Scan TCP (SYN/ACK)**
- **Scan UDP**

En plus de ces commandes, d'autres options Nmap peuvent être utilisées pour la découverte des hôtes, notamment :

1. **Scan de liste (-sL)**
   Le scan de liste est une méthode pour lister tous les hôtes sur un réseau sans les scanner réellement. Cette méthode peut être utile pour générer une liste de cibles pour un scan ultérieur. Pour effectuer un scan de liste, utilisez la commande suivante :
   ```bash
   nmap -sL 192.168.0.0/24
   ```

2. **Scan ping (-sn)**
   Cette option est utilisée pour désactiver le scan des ports et effectuer un scan ping. Si le scan est exécuté sur un LAN, il utilise le protocole ARP. S'il est exécuté dans un test d'intrusion externe, il utilise le protocole ICMP par défaut. Si ICMP ne répond pas, il envoie des paquets TCP SYN sur les ports 80 et 443.
   ```bash
   nmap -sn 192.168.0.0/24
   ```

3. **Ne jamais faire de résolution DNS (-n)**
   Cette option est utilisée pour désactiver la résolution DNS. Par défaut, le scan ping effectue une résolution DNS pour déterminer le nom d'hôte de la cible. Cependant, dans certains cas, la résolution DNS peut ralentir le processus de scan, et le flag `-n` peut être utilisé pour la désactiver.
   ```bash
   nmap -n 192.168.1.1
   nmap -n 192.168.1.1/24
   nmap -n example.com
   ```

4. **Traiter tous les hôtes comme en ligne et ignorer la découverte des hôtes (-Pn)**
   Cette option est utilisée pour ignorer la découverte des

 hôtes et traiter tous les hôtes comme en ligne. Lorsque ce flag est utilisé, nmap suppose que tous les hôtes sont en ligne et scanne directement les ports ciblés.
   ```bash
   nmap -v -Pn 192.168.1.1/24
   nmap -Pn 192.168.1.1
   nmap -Pn example.com
   ```

5. **Découverte TCP SYN/ACK, UDP ou SCTP sur des ports spécifiques (-PS/PA/PU/PY[portlist])**
   Ces options spécifient le type de probe à utiliser pour la découverte des hôtes. Elles envoient des paquets TCP SYN/ACK, UDP ou SCTP aux ports spécifiés pour déterminer quels hôtes sont actifs.
   ```bash
   nmap -v -sn -PS 192.168.1.1/24
   nmap -v -sn -PA 192.168.1.1/24
   nmap -sn -PU 192.168.1.1/24
   ```

6. **Probes ICMP echo, timestamp et netmask request (-PE/PP/PM)**
   Ces options spécifient le type de probe ICMP à utiliser pour la découverte des hôtes. Elles envoient des paquets ICMP echo, timestamp ou netmask request pour déterminer quels hôtes sont actifs.
   ```bash
   nmap -v -sn -PE 192.168.1.1/24
   nmap -v -sn -PP 192.168.1.1/24
   nmap -sn -PM 192.168.1.1/24
   ```

7. **Spécifier des serveurs DNS personnalisés (dns-servers <serv1[,serv2],…>)**
   Cette option permet de spécifier des serveurs DNS personnalisés à utiliser pour la résolution DNS.
   ```bash
   nmap --dns-servers 8.8.8.8 192.168.1.1
   ```

8. **Utiliser le DNS système (system-dns)**
   Cette option indique à Nmap d'utiliser le résolveur DNS du système d'exploitation pour la résolution DNS.
   ```bash
   nmap --system-dns 192.168.1.1
   ```

9. **Traceroute**
   Cette option effectue un traceroute vers chaque hôte pour déterminer le chemin entre l'hôte de scan et l'hôte cible.
   ```bash
   nmap --traceroute 192.168.1.1
   ```

#### Spécification des ports et ordre de scan

Par défaut, Nmap scanne les 1000 ports les plus couramment utilisés par divers services et applications. La spécification des ports et l'ordre des scans font référence au processus de spécification des ports à scanner lors de la reconnaissance du réseau. Cela est important pour un scan efficace car cela permet d'exclure les ports inutiles et de prioriser le scan en fonction de la fréquence d'utilisation. Nmap propose plusieurs options pour la spécification des ports et l'ordre des scans afin de personnaliser vos scans selon vos besoins spécifiques.

Voici quelques options courantes pour la spécification des ports et l'ordre des scans :

1. **-p <port ranges>**
   Cette option permet de spécifier quels ports scanner. Vous pouvez utiliser une liste de ports séparés par des virgules, une plage de ports séparée par un tiret ou une combinaison des deux. Par exemple :
   ```bash
   nmap -v -p 0-65535 192.168.1.1
   nmap -v -p 22 192.168.1.1
   nmap -Pn 192.168.1.1/24
   nmap -v -p T:80,443,U:53,111 192.168.1.1
   nmap -v -p- example.com
   ```

2. **--exclude-ports <port ranges>**
   Cette option permet d'exclure certains ports du scan. Vous pouvez utiliser la même syntaxe que pour l'option `-p` pour spécifier les ports à exclure.
   ```bash
   nmap -v --exclude-ports 80 example.com
   nmap -v --exclude-ports 100-400 192.168.1.1/24
   nmap --exclude-ports 1-1000,3389-3390
   ```

3. **-F**
   Cette option active le mode "rapide", qui scanne un ensemble plus réduit de ports que le scan par défaut. Par défaut, Nmap scanne les 1000 ports TCP les plus courants, mais avec l'option `-F`, il ne scannera que les 100 ports les plus courants.
   ```bash
   nmap -F 192.168.1.1
   nmap -v -F 192.168.1.1
   ```

4. **-r**
   Cette option indique à Nmap de scanner les ports dans l'ordre séquentiel au lieu de randomiser l'ordre. Cela peut être utile dans certaines situations, comme lors du scan de réseaux lents ou peu fiables.
   ```bash
   nmap -r -p- 192.168.1.1-10
   nmap -v -r -F example.com
   ```

5. **--top-ports <number>**
   Cette option spécifie le nombre de ports les plus courants à scanner.
   ```bash
   nmap --top-ports 100 192.168.1.1/24
   nmap --top-ports 100 example.com
   ```

#### Techniques de scan

Les techniques de scan font référence aux différentes méthodes utilisées par un scanner de réseau pour découvrir et recueillir des informations sur les hôtes, les réseaux et les services. Nmap, l'un des scanners de réseau les plus populaires, offre une large gamme de techniques de scan qui peuvent être utilisées à diverses fins, y compris la cartographie du réseau, l'évaluation des vulnérabilités et les tests d'intrusion.

Voici quelques options courantes pour les techniques de scan :

1. **Scan SYN (-sS)**
   C'est la technique de scan la plus populaire et la plus utilisée dans Nmap. Elle envoie un paquet SYN à l'hôte cible et attend une réponse. Si la cible répond avec un paquet SYN/ACK, cela indique que le port est ouvert, et si elle répond avec un paquet RST, cela indique que le port est fermé. Cette technique de scan est également connue sous le nom de scan semi-ouvert car elle ne complète pas la poignée de main TCP à trois voies. Le scan SYN est rapide et furtif, ce qui en fait une technique de scan idéale pour la cartographie de grands réseaux.
   ```bash
   nmap -sS -p- 192.168.1.1/24
   nmap -v -sS -p 21,22,25,80,443 example.com
   nmap -v -Pn -sS example.com/24
   ```

2. **Scan de connexion (-sT)**
   C'est un scan de connexion TCP complet qui complète la poignée de main TCP à trois voies. Cette technique de scan est plus lente que le scan SYN et est facilement détectable par les systèmes de détection d'intrusion et les pare-feu. Le scan de connexion est utile pour détecter la présence de certains types de pare-feu qui bloquent les paquets SYN.
   ```bash
   nmap -v -sT -p- 192.168.1.1/24
   nmap -v -sT -p 1-100 example.com
   nmap -v -sT example.com/24
   nmap -v -sT -p 80,443,22,21,25,445,69,53 example.com
   ```

3. **Scan ACK (-sA)**
   Cette technique de scan envoie un paquet ACK à l'hôte cible et attend une réponse. Si la cible répond avec un paquet RST, cela indique que le port est non filtré, et si elle ne répond pas du tout, cela indique que le port est filtré par un pare-feu. Le scan ACK aide à détecter la présence de pare-feu filtrant les paquets sans état.
   ```bash
   nmap -v -sA -p- 192.168.1.1/24
   nmap -v -sA -Pn example.com
   nmap -v -sA example.com/24
   ```

4. **Scan de fenêtre (-sW)**
   Cette technique de scan envoie un paquet avec une taille de fenêtre de zéro à l'hôte cible et attend une réponse. Si la cible répond avec un paquet RST, cela indique que le port est fermé, et si elle répond avec un paquet SYN/ACK, cela signifie que le port est ouvert. Le scan de fenêtre aide à détecter la présence de certains types de pare-feu qui bloquent des types spécifiques de paquets.
   ```bash
   nmap -sW 192.168.1.1/24
   nmap -v -sW example.com
   nmap -v -sW example.com/24
   ```

5. **Scan Maimon (-sM)**
   Cette technique de scan est similaire au scan de fenêtre, mais elle envoie un paquet avec le drapeau URG défini au lieu d'une taille de fenêtre de

 zéro. Le scan Maimon aide à détecter la présence de certains types de pare-feu qui bloquent les paquets de scan de fenêtre.
   ```bash
   nmap -sM 192.168.1.1/24
   nmap -v -sM example.com
   nmap -v -sM example.com/24
   ```

6. **Personnaliser les drapeaux de scan TCP (--scanflags <flags>)**
   La personnalisation des drapeaux de scan TCP permet de manipuler et de définir les drapeaux TCP pendant un scan réseau pour atteindre des objectifs de scan spécifiques. Les drapeaux TCP contrôlent l'état d'une connexion TCP, et leur modification peut entraîner des scans sur mesure qui révèlent des informations sur le système cible, y compris les ports ouverts, les versions des services et les informations sur le système d'exploitation.
   ```bash
   nmap -Pn -v --scanflags SYN -p 80,443 example.com
   ```

7. **Scan UDP (-sU)**
   L'option `-sU` dans nmap signifie scan UDP. Cela permet à l'utilisateur de scanner les ports UDP ouverts sur un système cible. Contrairement au TCP, qui utilise une poignée de main à trois voies pour établir une connexion, l'UDP est un protocole sans connexion qui ne nécessite pas de poignée de main pour établir une connexion. Cela rend le scan UDP plus difficile, car le scanner doit envoyer un paquet UDP à chaque port et attendre une réponse.
   ```bash
   nmap -sU 192.168.1.1
   nmap -v sU example.com
   ```

8. **Scan Idle (-sI)**
   L'option `-sI` dans nmap signifie scan idle. Cette technique permet à l'utilisateur d'effectuer un scan furtif en utilisant un hôte "zombie" qui est déjà approuvé par le système cible. Le scan idle consiste à envoyer une série de paquets à l'hôte zombie, qui à son tour envoie des paquets au système cible. En analysant les numéros de séquence des paquets, nmap peut déterminer quels ports sont ouverts sur le système cible sans être détecté.
   ```bash
   nmap -sI zombie_host target_host
   ```

9. **Scan FTP bounce (-b)**
   L'option `-b` dans nmap signifie scan FTP bounce. Cette technique consiste à utiliser un serveur FTP comme proxy pour scanner un système cible. Le scanner envoie une commande PORT au serveur FTP, ce qui entraîne la connexion du serveur au système cible et l'envoi des résultats du scan au scanner. Cette technique peut être utile pour contourner les pare-feu et autres mesures de sécurité qui bloquent les scans directs, mais elle est également considérée comme un risque de sécurité et n'est pas couramment utilisée.
   ```bash
   nmap -b ftp_server target_host
   ```

#### Scans TCP Null, FIN et Xmas :

1. **Scan Null TCP**
   Un scan Null TCP est un type de scan dans lequel un attaquant envoie un paquet sans drapeaux définis (c'est-à-dire null) aux ports du système cible. Si le port est ouvert, le système cible ne répondra pas au paquet, tandis que si le port est fermé, le système cible enverra un paquet TCP RST (reset) en réponse.
   ```bash
   nmap -v -sN -p 80,443 192.168.1.1
   nmap -v -sN -p 80,443 example.com
   nmap -v -Pn -sN example.com
   ```

2. **Scan FIN TCP**
   Un scan FIN TCP est un type de scan dans lequel un attaquant envoie un paquet avec le drapeau FIN défini aux ports du système cible. Si le port est ouvert, le système cible ne répondra pas au paquet, tandis que si le port est fermé, le système cible enverra un paquet TCP RST (reset) en réponse.
   ```bash
   nmap -v -sF -p- 192.168.1.1
   nmap -v -sF -p 80,443 example.com
   ```

3. **Scan Xmas**
   Un scan Xmas est un type de scan dans lequel un attaquant envoie un paquet avec les drapeaux FIN, PSH et URG définis (c'est-à-dire "allumés comme un arbre de Noël") aux ports du système cible. Si le port est ouvert, le système cible ne répondra pas au paquet, tandis que si le port est fermé, le système cible enverra un paquet TCP RST (reset) en réponse.
   ```bash
   nmap -v -sX -p 80,443 192.168.1.1
   nmap -v -sX -p 80,443 example.com
   ```

#### Sortie des résultats

La sortie de Nmap est présentée dans un format lisible par les humains qui peut être facilement compris par les administrateurs réseau et les analystes de sécurité. La sortie peut être personnalisée pour afficher des détails spécifiques, et Nmap supporte divers formats de sortie, y compris le texte brut, le XML et le HTML.

Voici quelques options courantes pour la sortie des résultats :

1. **-oN / -oX / -oS / -oG**
   Ces options permettent d'enregistrer les résultats du scan dans les formats normal, XML, s|<rIpt kIddi3, et Grepable, respectivement, dans le fichier donné.
   ```bash
   nmap --top-ports 100 192.168.1.1/24 -oN scan-results.txt
   nmap --top-ports 100 example.com -oN scan-results.txt
   nmap -sn -PU 192.168.1.1/24 -oN scan-results.txt
   nmap -v -p 0-65535 192.168.1.1 -oN scan-results.txt
   nmap -v -p T:80,443,U:53,111 192.168.1.1 -oN scan-results.txt
   ```

2. **-oA <basename>**
   L'option `-oA` permet d'enregistrer les résultats dans les trois formats principaux (normal, XML et Grepable) en même temps. Cela peut être utile si vous souhaitez avoir plusieurs copies des résultats dans différents formats ou si vous souhaitez comparer les résultats de différents formats.
   ```bash
   nmap --top-ports 100 192.168.1.1/24 -oA <filename>
   nmap --top-ports 100 example.com -oA <filename>
   nmap -sn -PU 192.168.1.1/24 -oA <filename>
   nmap -v -p 0-65535 192.168.1.1 -oA <filename>
   nmap -v -p T:80,443,U:53,111 192.168.1.1 -oA <filename>
   ```

3. **-v : Augmenter le niveau de verbosité**
   L'option `-v` permet d'augmenter le niveau de verbosité de Nmap. Par défaut, Nmap n'affiche que des informations de base sur le scan, mais avec l'option `-v`, vous pouvez obtenir des informations plus détaillées sur le scan. Vous pouvez utiliser l'option `-v` plusieurs fois pour augmenter encore plus le niveau de verbosité. Par exemple, l'option `-vv` affichera encore plus d'informations détaillées que l'option `-v`.
   ```bash
   nmap -v -p 80,443 192.168.1.1
   nmap -vv -p 80,443 192.168.1.1
   ```

4. **-d : Augmenter le niveau de débogage**
   L'option `-d` permet d'augmenter le niveau de débogage de Nmap. Cela peut être utile si vous essayez de résoudre un problème avec Nmap ou si vous souhaitez obtenir encore plus d'informations détaillées sur le scan. Comme l'option `-v`, vous pouvez utiliser l'option `-d` plusieurs fois pour augmenter encore plus le niveau de débogage. Par exemple, l'option `-dd` affichera encore plus d'informations détaillées que l'option `-d`.
   ```bash
   nmap -d -p 80,443 192.168.1.1
   nmap -dd -p 80,443 192.168.1.1
   ```

5. **--reason : Afficher la raison de l'état d'un port**
   L'option `--reason` permet d'afficher la raison pour laquelle un port est dans un état particulier. Par exemple, si un port est affiché comme ouvert, l'option `--reason` peut être utilisée pour afficher pourquoi Nmap pense que le port est ouvert. Cela peut être utile pour comprendre pourquoi certains ports sont ouverts ou fermés et pour résoudre les problèmes de scan.
   ```bash
   nmap --top-ports 100 192.168.1.1/24 --reason
   nmap --top-ports 100 example.com --reason
   nmap -sn -PU 192.168.1.1/24 --reason
   nmap -v -p 0-65535 192.168.1

.1 --reason
   nmap -v -p T:80,443,U:53,111 192.168.1.1 --reason
   ```

#### Détection des versions des services

La fonctionnalité de détection des versions des services dans Nmap est un outil puissant pour identifier les services et les versions exécutés sur les ports ouverts du système cible. Cette information est utile pour identifier les vulnérabilités et les faiblesses du système cible, qui peuvent être utilisées pour améliorer sa posture de sécurité. En utilisant la fonctionnalité de détection des versions des services de Nmap, vous pouvez obtenir des informations précieuses sur le système cible et prendre des décisions éclairées concernant sa sécurité.

Pour utiliser la fonctionnalité de détection des versions des services, vous devez spécifier l'option `-sV` avec le scan de connexion TCP. L'option `-sV` sonde les ports ouverts pour déterminer les informations de service et de version. De plus, vous pouvez utiliser les options suivantes pour contrôler l'intensité de la détection des versions :

1. **Détection de la version de base**
   ```bash
   nmap -sV 192.168.1.1
   ```

2. **Définir l'intensité de la version**
   ```bash
   nmap -sV --version-intensity 5 192.168.1.1
   nmap -v -n -sV --version-intensity 5 192.168.1.1
   nmap -v -n -sT -sV --version-intensity 5 192.168.1.0/24
   ```

3. **Utiliser Version Light**
   ```bash
   nmap -sV --version-light 192.168.1.1
   ```

4. **Utiliser Version All**
   ```bash
   nmap -sV --version-all 192.168.1.1
   ```

#### Détection des systèmes d'exploitation

La fonctionnalité de détection des systèmes d'exploitation de Nmap est un outil puissant pour déterminer le système d'exploitation exécuté sur le système cible. Cette information est utile pour identifier les vulnérabilités spécifiques au système d'exploitation et optimiser les attaques contre le système cible. En utilisant la fonctionnalité de détection des systèmes d'exploitation de Nmap, vous pouvez obtenir des informations précieuses sur le système cible et prendre des décisions éclairées concernant sa sécurité. Cependant, il est important de noter que la fonctionnalité de détection des systèmes d'exploitation n'est pas toujours précise et doit être utilisée en conjonction avec d'autres outils et techniques pour confirmer le système d'exploitation.

Voici quelques exemples d'utilisation de la fonctionnalité de détection des systèmes d'exploitation de Nmap :

1. **Détection de l'OS de base**
   ```bash
   nmap -O 192.168.1.1
   nmap -vv -sT -O -p- 192.168.1.1/24
   nmap -v -O example.com -oA <filename>
   ```

2. **Limiter la détection de l'OS**
   ```bash
   nmap -O --osscan-limit 192.168.1.0/24
   ```

3. **Détection de l'OS agressive**
   ```bash
   nmap -O --osscan-guess 192.168.1.1
   ```

4. **Détection de l'OS avancée**
   ```bash
   nmap -A 192.168.1.1
   ```

#### Scan de scripts

Le scan de scripts Nmap est une fonctionnalité puissante de Nmap qui permet aux utilisateurs d'automatiser des tâches à l'aide de scripts pré-écrits ou d'écrire leurs propres scripts en utilisant le langage de programmation Lua. Les scripts peuvent être utilisés pour recueillir des informations sur les hôtes, les services et les vulnérabilités, ou pour effectuer des tâches telles que des attaques par force brute, des attaques par déni de service et des exploits.

La fonctionnalité de scan de scripts de Nmap est activée en utilisant l'option `-sC` ou l'option `--script=default`. Cette option indique à Nmap d'exécuter l'ensemble de scripts par défaut fourni avec l'outil. L'ensemble de scripts par défaut contient plus de 600 scripts couvrant un large éventail de tâches, de la collecte d'informations à la détection de vulnérabilités.
```bash
nmap -v -p- -sT -sV -sC -T4 192.168.1.0/24
nmap -v -Pn -sT -sV -O -sC example.com
nmap -v -Pn -sT -sV -O --script=http-vuln-cve2006-3392.nse example.com
```

En conclusion, Nmap est un outil incroyablement polyvalent et puissant qui peut être utilisé pour l'exploration de réseau, la gestion et l'audit de sécurité. Tout au long de cet article, nous avons exploré les différentes étapes du scan avec Nmap, y compris la spécification des cibles, la découverte des hôtes, la spécification des ports et l'ordre des scans, et la sortie des résultats. Nous avons également fourni des exemples d'utilisation des commandes pour chaque étape, ainsi que quelques options courantes utilisées avec Nmap.

La spécification des cibles est une étape critique de l'utilisation de Nmap, et il existe plusieurs façons de spécifier la cible, y compris les adresses IP individuelles ou les noms d'hôtes, la notation CIDR ou une plage d'adresses IP. La découverte des hôtes est également une partie essentielle du scan de réseau, et diverses techniques Nmap peuvent être utilisées pour y parvenir.

Dans l'ensemble, en apprenant à utiliser Nmap, vous serez mieux équipé pour gérer et auditer la sécurité de votre réseau. Nmap fournit des informations inestimables sur les vulnérabilités de votre réseau, vous permettant de sécuriser proactivement vos systèmes et de protéger vos données. Que vous soyez un professionnel de l'informatique, un chercheur en sécurité ou simplement intéressé par la sécurité des réseaux, Nmap est un outil essentiel dans votre boîte à outils. Nous espérons que cet article vous a fourni les connaissances et la confiance nécessaires pour utiliser Nmap efficacement.

Bon scan !
