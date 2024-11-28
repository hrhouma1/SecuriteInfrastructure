### **1.1 Les Méthodes de Détection**

Pour protéger efficacement un environnement informatique, Microsoft ATA (Advanced Threat Analytics) s'appuie sur trois méthodologies principales de détection : la détection basée sur les signatures, l'analyse comportementale et l'agrégation contextuelle. Ces approches complémentaires permettent de couvrir un large éventail de scénarios d'attaque tout en minimisant les faux positifs.

---

#### **1. Détection Basée sur les Signatures**

La détection basée sur les signatures est l’une des techniques traditionnelles utilisées en cybersécurité. Elle repose sur la comparaison d’événements ou d’activités observées avec des modèles ou des signatures préalablement connus, correspondant à des menaces identifiées.

**Comment cela fonctionne-t-il dans ATA ?**
- ATA contient une base de données de modèles d'attaques connues, comme les attaques "Pass-the-Hash" ou "Pass-the-Ticket".
- Lorsqu'une activité correspond à ces modèles, une alerte est immédiatement générée.

**Exemples :**
1. **Attaque "Pass-the-Hash" :**
   - Cette attaque exploite des hachages de mots de passe (hashes) capturés sur un réseau pour accéder à des ressources protégées sans connaître le mot de passe en clair.
   - ATA détecte cette attaque en surveillant les tentatives suspectes d'accès réseau utilisant un hachage valide, mais provenant d'une machine ou d'un utilisateur inhabituels.

2. **Attaque "Pass-the-Ticket" :**
   - Cette méthode utilise des tickets Kerberos volés pour accéder aux ressources réseau sans authentification légitime.
   - ATA identifie les incohérences dans l’utilisation des tickets, par exemple si un ticket est utilisé par un utilisateur à un moment ou à un endroit inattendu.

**Avantages :**
- Détection rapide et précise pour les menaces bien documentées.
- Faible taux de faux positifs pour les attaques courantes.

**Limites :**
- Inefficace contre les menaces inconnues ou inédites (exploits "zero-day").
- Nécessite des mises à jour régulières des signatures pour rester efficace face aux nouvelles menaces.

---

#### **2. Analyse Comportementale**

L'analyse comportementale est l'une des caractéristiques les plus avancées d'ATA. Contrairement à la détection basée sur les signatures, cette méthode repose sur l’apprentissage automatique pour identifier les écarts par rapport à des comportements "normaux".

**Comment cela fonctionne-t-il dans ATA ?**
- ATA surveille en continu les actions des utilisateurs, des ordinateurs et d’autres entités dans Active Directory.
- L’outil établit une base de référence pour le comportement normal de chaque entité (par exemple, les horaires de connexion habituels, les ressources fréquemment accédées, les interactions réseau typiques).
- Toute activité qui s'écarte significativement de cette base de référence est signalée comme suspecte.

**Exemples :**
1. **Connexion inhabituelle d’un utilisateur :**
   - Un utilisateur accède soudainement à un serveur critique en dehors de ses heures habituelles.
   - ATA détecte cette anomalie et génère une alerte.

2. **Augmentation soudaine d'activités :**
   - Un compte d’utilisateur commence à copier un grand volume de données à partir d’un serveur partagé, ce qui est inhabituel par rapport à son historique.
   - ATA identifie ce comportement comme potentiellement malveillant (exfiltration de données).

**Avantages :**
- Permet de détecter des attaques inconnues ou des comportements malveillants émergents.
- Offre une approche proactive en détectant des anomalies avant qu'elles ne causent des dommages.

**Limites :**
- Nécessite une période d'apprentissage pour établir des modèles de comportement normal (généralement 3 à 4 semaines).
- Peut générer des faux positifs dans des environnements avec des comportements très dynamiques ou changeants.

---

#### **3. Agrégation Contextuelle**

L'agrégation contextuelle est une méthode qui combine plusieurs événements ou anomalies distincts pour former une vision globale et cohérente d'une menace potentielle. Elle joue un rôle clé dans la réduction des faux positifs et l’amélioration de la pertinence des alertes.

**Comment cela fonctionne-t-il dans ATA ?**
- ATA collecte et analyse des données provenant de diverses sources, comme les événements Windows, les journaux réseau et les interactions AD.
- Les événements isolés (qui pourraient ne pas sembler problématiques individuellement) sont regroupés pour identifier des schémas plus complexes.
- Par exemple, une seule tentative de connexion échouée peut ne pas être significative. Cependant, plusieurs tentatives échouées suivies d’une connexion réussie depuis une machine inhabituelle peuvent indiquer une attaque par force brute.

**Exemples :**
1. **Tentative de mouvement latéral :**
   - Un attaquant utilise des identifiants volés pour accéder à différentes machines dans le réseau. Chaque connexion individuelle pourrait sembler légitime, mais en les regroupant, ATA détecte un comportement suspect de mouvement latéral.

2. **Chaîne d'attaques coordonnées :**
   - Un attaquant commence par exécuter une attaque "Pass-the-Hash", puis accède à un contrôleur de domaine. ATA regroupe ces activités pour identifier une attaque complète.

**Avantages :**
- Réduit considérablement les faux positifs en fournissant des alertes contextuelles.
- Simplifie l'analyse pour les équipes de sécurité grâce à des chronologies visuelles et logiques.

**Limites :**
- Dépend de la capacité de collecte et de traitement des données.
- Nécessite une infrastructure réseau bien configurée pour capturer les événements critiques.

---

#### **Synthèse des Méthodes de Détection**

| **Méthode**                | **Avantages**                            | **Limites**                                | **Cas d’usage**                                 |
|----------------------------|------------------------------------------|-------------------------------------------|------------------------------------------------|
| Détection basée sur les signatures | Rapide, précise pour les menaces connues | Inefficace contre les nouvelles menaces   | Attaques Pass-the-Hash, Pass-the-Ticket        |
| Analyse comportementale    | Détecte des menaces inconnues            | Nécessite une période d’apprentissage     | Activités anormales, mouvements latéraux       |
| Agrégation contextuelle    | Réduction des faux positifs              | Dépend de la qualité des données          | Analyse des schémas d’attaques complexes       |

Grâce à ces trois approches complémentaires, Microsoft ATA offre une protection robuste contre un large éventail de menaces, des attaques connues aux comportements malveillants émergents. Ces méthodes placent ATA au centre des stratégies de sécurité modernes.
