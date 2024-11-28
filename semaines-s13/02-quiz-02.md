# **Mises en situations réelles basées sur Microsoft ATA**

- Ces mises en situation montrent comment Microsoft ATA s’intègre dans un contexte professionnel réel pour détecter, analyser et neutraliser des menaces avancées. 
- Elles permettent également de sensibiliser les équipes à l'importance d’une surveillance proactive et d'une réponse rapide face aux anomalies détectées.


#### **Mise en situation 1 : Détection d’un "Pass-the-Hash" dans une grande entreprise**

**Contexte :**
Une entreprise multinationale a récemment subi plusieurs tentatives d'accès non autorisées à ses serveurs critiques. Le service informatique a déployé **Microsoft ATA** pour renforcer la surveillance des activités liées à Active Directory. Une alerte est déclenchée par ATA signalant une activité suspecte sur un serveur distant.

**Scénario :**
- Un attaquant externe a réussi à compromettre un poste utilisateur standard en exploitant une vulnérabilité logicielle et a capturé le hachage du mot de passe de cet utilisateur.
- Le hachage est utilisé pour tenter de se connecter au serveur de fichiers contenant des données sensibles.
- ATA détecte que cet utilisateur, qui n'accède normalement qu'à un sous-répertoire spécifique, tente de se connecter à des ressources auxquelles il n’a jamais eu accès auparavant.
- L’équipe de sécurité est immédiatement alertée avec des informations contextuelles :
  - Heure de l’attaque.
  - Ressources ciblées.
  - Identifiant et machine de l’utilisateur compromis.

**Résolution :**
- L’équipe de sécurité bloque immédiatement les connexions en provenance de l’adresse IP suspecte.
- Les comptes compromis sont désactivés.
- Une analyse post-incident est effectuée pour identifier et corriger la vulnérabilité initiale.

---

#### **Mise en situation 2 : Compromission d’un compte administrateur privilégié**

**Contexte :**
Lors d’un audit de sécurité, ATA détecte qu’un compte administrateur tente d’accéder à plusieurs contrôleurs de domaine à des heures inhabituelles (3h du matin). Ce comportement dévie complètement des habitudes de l’administrateur concerné.

**Scénario :**
- Un attaquant a récupéré les informations d’identification d’un administrateur via un phishing ciblé.
- En exploitant ces identifiants, l’attaquant effectue des requêtes sur Active Directory pour cartographier les ressources réseau et identifier les comptes à privilèges.
- ATA génère une alerte de haute gravité, car le compte administrateur n’avait jamais accédé à ces ressources auparavant.
- Les informations collectées incluent :
  - La machine source de la connexion.
  - Les comptes et ressources ciblés.
  - Une chronologie détaillée des activités suspectes.

**Résolution :**
- Le compte administrateur est désactivé immédiatement.
- Une enquête est lancée pour analyser les journaux ATA et déterminer si d'autres comptes ont été compromis.
- Une campagne de sensibilisation sur le phishing est organisée pour réduire les risques futurs.

---

#### **Mise en situation 3 : Mouvement latéral dans un réseau bancaire**

**Contexte :**
Dans une institution financière, ATA détecte qu’un compte utilisateur standard tente d’accéder à plusieurs serveurs de bases de données, une activité considérée comme inhabituelle. Cette alerte est classée comme un mouvement latéral potentiel.

**Scénario :**
- Un attaquant interne ou un employé mécontent utilise un compte standard pour collecter des informations sur le réseau.
- Il utilise des outils de pentest pour scanner les ressources réseau accessibles.
- ATA détecte cette activité en agrégeant plusieurs événements :
  - Plusieurs tentatives de connexion échouées suivies de réussites sur différentes machines.
  - Une activité réseau inhabituelle (consultation de grandes quantités de données).
  - Une machine qui accède à des bases de données sans justification.

**Résolution :**
- L’activité suspecte est immédiatement bloquée.
- Une vérification approfondie est effectuée sur les bases de données concernées pour détecter des modifications ou des copies de données.
- Le compte utilisateur est suspendu, et l’employé concerné est interrogé.

---

#### **Mise en situation 4 : Détection d’un Golden Ticket**

**Contexte :**
Une grande organisation gouvernementale reçoit une alerte d’ATA indiquant une tentative de manipulation de tickets Kerberos. ATA signale un "Golden Ticket" utilisé pour accéder à des contrôleurs de domaine.

**Scénario :**
- Un attaquant avancé a compromis le fichier NTDS.dit contenant des informations critiques sur Active Directory.
- L’attaquant a créé un ticket Kerberos falsifié pour obtenir un accès complet au réseau.
- ATA détecte des incohérences dans les tickets :
  - Le ticket utilisé n’a jamais été émis par le contrôleur de domaine.
  - L’accès est tenté depuis une machine inconnue.

**Résolution :**
- Les contrôleurs de domaine sont immédiatement isolés du réseau.
- Les tickets Kerberos sont révoqués, et une nouvelle clé Kerberos (KRBTGT) est régénérée.
- Les systèmes compromis sont réinstallés, et une enquête est lancée pour comprendre l’origine de l’attaque.

---

#### **Mise en situation 5 : Exfiltration de données sensibles**

**Contexte :**
Dans une entreprise technologique, ATA détecte qu’un utilisateur télécharge un grand volume de données depuis un serveur partagé. Cette activité est inhabituelle pour ce compte.

**Scénario :**
- Un employé mécontent décide de quitter l’entreprise et tente de copier des projets confidentiels sur un périphérique USB.
- ATA détecte une augmentation inhabituelle de l’activité sur ce compte, notamment des requêtes réseau volumineuses vers des serveurs contenant des informations sensibles.
- Une alerte est générée, indiquant :
  - Le volume de données transférées.
  - Les ressources spécifiques ciblées.
  - L’heure et l’emplacement du transfert.

**Résolution :**
- Le transfert est immédiatement interrompu.
- L’employé est confronté, et une enquête est menée pour évaluer les intentions et l’étendue des données copiées.
- Les politiques de sécurité de l’entreprise sont mises à jour pour restreindre les transferts non autorisés.

---

#### **Mise en situation 6 : Intégration avec un SIEM**

**Contexte :**
Une organisation utilise Microsoft ATA avec un SIEM comme Splunk pour centraliser toutes ses alertes. Une alerte ATA concernant un déplacement latéral est corrélée avec des journaux d’autres outils de sécurité, comme les pare-feu.

**Scénario :**
- ATA détecte un mouvement latéral suspect, mais l’origine de l’activité reste incertaine.
- Le SIEM corrèle les alertes ATA avec :
  - Des journaux de connexion suspecte à partir d’un VPN externe.
  - Des activités anormales captées par l’IDS (Intrusion Detection System).
- Le SIEM confirme qu’un attaquant a utilisé un VPN pour entrer dans le réseau avant de se déplacer latéralement.

**Résolution :**
- Le compte VPN est immédiatement désactivé.
- Le pare-feu bloque l’adresse IP externe de l’attaquant.
- L’ensemble des alertes est analysé pour combler les failles exploitées.


