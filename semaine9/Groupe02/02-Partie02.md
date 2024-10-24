# Examen de mi-session partie 2 : **Sécurisation et Monitoring dans une Infrastructure Microsoft**

#### Instructions générales :
- Vous avez le choix entre **deux versions** de l'examen : la **Version 1** ou la **Variante 2**.  
- **Choisissez une seule version** et répondez aux questions correspondantes.  
- Fournissez des explications détaillées et justifiez vos réponses avec des exemples concrets basés sur des scénarios réels.  
- Utilisez les concepts vus en cours, les outils de sécurité Microsoft et les meilleures pratiques dans vos réponses.  

---
# Version 1
---

### 1. **Monitoring et Sécurisation du Trafic Réseau dans une Infrastructure Microsoft**

#### Mise en situation :
Un administrateur réseau travaille dans une entreprise qui doit surveiller l’ensemble de son infrastructure réseau Microsoft. Il doit s'assurer que le trafic réseau est sécurisé, notamment les transferts de fichiers SMB, tout en configurant des outils pour détecter d’éventuelles anomalies dans les journaux d’événements. De plus, l’entreprise souhaite protéger ses communications critiques avec IPSec et ses fichiers sensibles avec EFS.

#### Questions :

1. **Audit et analyse des journaux d’événements :**  
   Décrivez en détail comment un audit avancé et l’analyse des journaux d’événements peuvent aider à détecter des anomalies dans un réseau Microsoft. Utilisez des exemples concrets pour montrer comment l'outil **Microsoft Event Viewer** peut être utilisé pour identifier des tentatives d'accès non autorisées, des erreurs réseau, ou des changements de configuration. Mentionnez également les types d'événements critiques qu'un administrateur devrait surveiller régulièrement pour garantir la sécurité.

2. **Sécurisation du trafic SMB et analyse du réseau :**  
   Expliquez comment sécuriser efficacement le trafic SMB dans un environnement Windows. Décrivez les meilleures pratiques de sécurisation, comme l'utilisation du chiffrement SMB et les politiques d'accès. Ensuite, décrivez l'utilisation de **Microsoft Message Analyzer** pour surveiller le trafic réseau. Donnez un exemple détaillé de l'application de cet outil pour identifier des anomalies dans le trafic, et expliquez comment vous pourriez diagnostiquer des problèmes ou des menaces potentielles à travers l’analyse du trafic réseau.

3. **Sécurisation globale avec pare-feu et chiffrement IPSec :**  
   Décrivez les étapes pour configurer un **pare-feu Windows avec des fonctions avancées** afin de protéger le trafic réseau de l'entreprise. Montrez comment un pare-feu bien configuré peut aider à filtrer le trafic dangereux et à bloquer les connexions non autorisées. Ensuite, expliquez comment utiliser **IPSec** pour sécuriser les communications entre les différents segments du réseau, tels que les succursales de l’entreprise. Décrivez les avantages d'utiliser IPSec et comment il peut être intégré dans une stratégie de sécurisation complète des communications réseau.

4. **Chiffrement des fichiers avec EFS :**  
   Expliquez en détail comment utiliser **EFS (Encrypting File System)** pour chiffrer les fichiers sensibles dans un environnement Windows. Décrivez les scénarios dans lesquels EFS est le plus utile et les meilleures pratiques pour l'intégrer dans une infrastructure de sécurité. De plus, décrivez comment configurer et gérer des **agents de récupération** pour garantir que les fichiers chiffrés restent accessibles en cas de perte de clé. Donnez un exemple pratique où EFS est combiné avec d'autres outils de protection pour renforcer la sécurité des données sensibles.

---

### 5. **Mise en Place d'une Infrastructure d'Authentification Centralisée avec RADIUS**

Une organisation veut implémenter un serveur RADIUS pour centraliser l’authentification des utilisateurs sur son réseau. Le service IT doit déployer un serveur NPS (Network Policy Server) et configurer l'authentification via RADIUS.

#### Questions :
1. Décrivez les étapes nécessaires pour implémenter une solution RADIUS avec un serveur NPS dans un environnement d’AD DS.
2. Proposez une méthode pour tester et auditer la journalisation des accès des utilisateurs authentifiés via RADIUS.

---
# Variante 2
---

### 1. **Surveillance et Sécurisation des Communications Réseau dans une Infrastructure Microsoft**

#### Mise en situation :
Un administrateur réseau travaille dans une entreprise qui doit protéger ses communications entre sites distants. Il doit implémenter une solution VPN pour sécuriser les connexions, mais rencontre des blocages sur certains réseaux publics. L'administrateur doit ajuster la configuration du VPN tout en assurant le monitoring des activités réseau et la protection des fichiers sensibles via EFS.

#### Questions :

1. **Surveillance des journaux d'événements :**  
   Décrivez comment configurer l’audit avancé dans **Active Directory** pour surveiller les événements liés aux connexions VPN et à la sécurité réseau. Quels types d'événements spécifiques doivent être surveillés pour identifier des tentatives de connexion échouées ou des accès non autorisés ? Illustrez votre réponse avec un exemple où un administrateur utilise les journaux d'événements pour repérer une anomalie liée à un problème de configuration VPN.

2. **Changement de protocole VPN pour contourner les restrictions réseau :**  
   Lors de l’implémentation du VPN, l’administrateur rencontre des restrictions sur les réseaux publics qui bloquent le protocole utilisé par le VPN. Expliquez comment changer de protocole VPN (par exemple, basculer de **OpenVPN** à **IKEv2** ou **SSTP**) peut permettre de contourner ces restrictions tout en garantissant la sécurité des communications. Donnez des exemples concrets des avantages et inconvénients de chaque protocole pour aider l’administrateur à choisir celui qui convient le mieux à l’entreprise.

3. **Configuration d’un pare-feu Windows et des règles IPSec :**  
   Décrivez les étapes pour configurer un **pare-feu Windows** avec des règles spécifiques pour sécuriser le trafic réseau de l’entreprise. Discutez de l’importance des pare-feu dans la protection des réseaux et expliquez comment intégrer **IPSec** pour protéger les communications sensibles entre les serveurs. Expliquez également comment IPSec peut être utilisé pour renforcer la sécurité des connexions VPN dans un environnement multi-sites.

4. **Chiffrement des fichiers avec EFS pour protéger les données sensibles :**  
   L'entreprise stocke des informations confidentielles sur des serveurs Windows. Expliquez comment utiliser **EFS (Encrypting File System)** pour chiffrer les fichiers sensibles, en vous concentrant sur la configuration des utilisateurs et des groupes d'accès. Indiquez comment gérer les **agents de récupération** pour garantir que les données chiffrées ne sont pas perdues en cas de problème de clé. Fournissez un exemple pratique où EFS est utilisé dans un environnement avec plusieurs utilisateurs ayant des niveaux d'accès différents.

---

### 5. **Implémentation d'une Solution d'Authentification Centralisée avec RADIUS dans un Contexte de VPN**

#### Mise en situation :
Une entreprise souhaite sécuriser l'accès distant de ses employés en utilisant un **VPN** et en centralisant l'authentification des utilisateurs via **RADIUS**. Le service IT est chargé de configurer un **serveur NPS (Network Policy Server)** pour gérer l'authentification des utilisateurs qui se connectent à distance au réseau via le VPN.

#### Questions :

1. **Mise en place d’un serveur NPS pour gérer les connexions VPN :**  
   Décrivez les étapes pour déployer et configurer un serveur **NPS** dans un environnement **Active Directory** afin de centraliser l’authentification des utilisateurs VPN via **RADIUS**. Expliquez comment intégrer les clients VPN au serveur NPS pour permettre aux utilisateurs de se connecter de manière sécurisée. Quels paramètres spécifiques doivent être définis pour assurer la sécurité des connexions VPN ?

2. **Audit et surveillance des connexions via RADIUS :**  
   Proposez une méthode pour auditer et surveiller les connexions VPN via RADIUS, afin de s’assurer que les utilisateurs distants sont correctement authentifiés. Quels outils ou paramètres utiliseriez-vous pour surveiller et auditer les tentatives de connexion réussies et échouées ? Décrivez également comment identifier des connexions suspectes ou non autorisées à partir des journaux RADIUS et proposez des solutions pour renforcer la sécurité des accès VPN.

---

**Fin de la partie 2**

