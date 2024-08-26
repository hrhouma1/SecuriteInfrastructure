
## 2.1 System Monitor (Sysmon)

### Description :
Sysmon est un utilitaire de la suite Sysinternals (voir ***Annexe 2.1***) qui permet de surveiller les événements système sur une machine Windows. Il enregistre les détails des activités critiques, telles que la création de processus, les connexions réseau et les modifications de fichiers, fournissant ainsi une vue approfondie de ce qui se passe sur le système.

### Utilité de Sysmon :
Sysmon est particulièrement utile pour les professionnels de la sécurité qui doivent analyser les comportements suspects et identifier les activités malveillantes qui pourraient passer inaperçues avec les journaux système standard. En capturant des événements détaillés, Sysmon permet de retracer les actions d'un attaquant ou de comprendre comment un malware a compromis un système.

### Principales Fonctionnalités :
1. **Surveillance des Processus :** Sysmon enregistre chaque création de processus, fournissant des informations telles que le nom du processus, l'identifiant (PID), le chemin d'accès, et l'utilisateur qui l'a initié.
2. **Surveillance des Connexions Réseau :** Il capture les connexions réseau entrantes et sortantes, incluant les adresses IP et les ports utilisés.
3. **Surveillance des Modifications de Fichiers :** Sysmon suit les modifications apportées aux fichiers, y compris la création, la suppression, et la modification de fichiers critiques.

### Avantages de Sysmon :
- **Visibilité Accrue :** Offrant une visibilité détaillée des événements système, Sysmon aide à identifier les comportements suspects qui pourraient signaler une compromission.
- **Personnalisation :** Sysmon permet de configurer des filtres pour capturer uniquement les événements qui sont pertinents pour l'environnement surveillé.
- **Analyse Post-Incident :** En cas d'incident de sécurité, les journaux Sysmon peuvent être utilisés pour analyser l'incident et comprendre comment l'attaque s'est déroulée.

### Limitations de Sysmon :
- **Volume de Données :** Sysmon génère un grand nombre de journaux, ce qui peut compliquer l'analyse si des filtres adéquats ne sont pas appliqués.
- **Configuration Avancée :** Bien que puissant, Sysmon nécessite une configuration fine pour éviter de capturer trop de données non pertinentes, ce qui peut être complexe pour les utilisateurs novices.

# Annexe 2.1 - Sysinternals

Sysinternals est un ensemble d'outils et d'utilitaires avancés développés par Mark Russinovich et distribués par Microsoft, destinés à l'administration, au diagnostic et à la résolution de problèmes sur les systèmes Windows. Ces outils sont très utiles pour les administrateurs systèmes, les ingénieurs en sécurité et les professionnels de l'informatique, car ils permettent d'analyser en profondeur le comportement du système, de surveiller les processus en cours, de gérer les fichiers, de déboguer des applications, et bien plus encore.

Je vous propose quelques-uns des outils les plus populaires inclus dans Sysinternals :

1. **Process Explorer** : Un gestionnaire de tâches avancé qui offre une vue détaillée des processus en cours d'exécution, des fichiers et des clés de registre utilisés par ces processus, ainsi que des informations sur les DLL chargées et les dépendances.

2. **Autoruns** : Un utilitaire qui montre tous les programmes qui se lancent automatiquement au démarrage du système ou lors de la connexion d'un utilisateur. Cela inclut les programmes dans le dossier de démarrage, les services, les pilotes, les tâches planifiées, etc.

3. **PsExec** : Un outil pour exécuter des commandes sur des systèmes distants, très utile pour administrer des serveurs ou des machines sans avoir à s'y connecter physiquement.

4. **TCPView** : Un outil pour surveiller les connexions réseau en temps réel, permettant de voir quelles applications ou processus sont connectés à Internet ou à d'autres ordinateurs.

5. **Process Monitor** : Un utilitaire qui combine les fonctionnalités de Regmon et Filemon, et qui permet de surveiller en temps réel l'activité du système de fichiers, des registres et des processus.

6. **BgInfo** : Un utilitaire qui affiche les informations du système directement sur le fond d'écran, telles que le nom de la machine, l'adresse IP, la version de Windows, etc.

Les outils Sysinternals sont disponibles gratuitement sur le site officiel de Microsoft et peuvent être exécutés sans installation, ce qui les rend extrêmement pratiques pour les interventions rapides et efficaces.
