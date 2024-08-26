
## 2.1 System Monitor (Sysmon)

### Description :
Sysmon est un utilitaire de la suite Sysinternals qui permet de surveiller les événements système sur une machine Windows. Il enregistre les détails des activités critiques, telles que la création de processus, les connexions réseau et les modifications de fichiers, fournissant ainsi une vue approfondie de ce qui se passe sur le système.

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
