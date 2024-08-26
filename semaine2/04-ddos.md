
## 2. DDoS (Denial of Service Distribué) :

### Description :
Les attaques DDoS visent à rendre un service ou un site web indisponible en le submergeant de requêtes simultanées provenant de multiples systèmes compromis (appelés botnets). Ces attaques exploitent la capacité limitée d'un serveur à traiter un nombre important de requêtes, le saturant jusqu'à ce qu'il devienne incapable de répondre aux requêtes légitimes.

### Types d'Attaques DDoS :
1. **Attaques Volumétriques :** Ces attaques saturent la bande passante du réseau avec un flux massif de données, rendant le service indisponible.
2. **Attaques par Epuisement des Ressources :** Elles visent à consommer les ressources système, telles que la mémoire ou le CPU, en submergeant le serveur de requêtes complexes.
3. **Attaques sur la Couche Applicative (Layer 7) :** Ces attaques ciblent des services ou des fonctionnalités spécifiques d'une application web pour les rendre indisponibles.

### Signes à Surveiller :
- **Latence Élevée :** Les temps de réponse du serveur augmentent de manière significative.
- **Inaccessibilité du Site :** Le site web ou le service devient lent ou totalement inaccessible pour les utilisateurs.
- **Augmentation du Trafic :** Un pic inhabituel du trafic réseau, particulièrement venant de sources multiples et dispersées géographiquement.

### Exemples Réels :
- **Dyn (2016) :** Une attaque DDoS massive a rendu plusieurs services majeurs (comme Twitter, Netflix, et Reddit) indisponibles en ciblant le fournisseur de DNS Dyn avec un trafic provenant de milliers d'appareils IoT compromis.
- **GitHub (2018) :** GitHub a subi une attaque DDoS atteignant 1,35 Tbps, la plus grande à ce jour, qui a temporairement interrompu ses services.

### Comment se Protéger :
- **Utilisation de Réseaux de Distribution de Contenu (CDN) :** Les CDN peuvent absorber et redistribuer le trafic pour éviter la surcharge des serveurs.
- **Détection et Mitigation Automatique :** Utiliser des services de protection contre les DDoS qui détectent automatiquement les attaques et appliquent des mesures pour les atténuer.
- **Redondance et Répartition de la Charge :** Configurer plusieurs serveurs et points d'accès pour répartir la charge et éviter les points uniques de défaillance.
