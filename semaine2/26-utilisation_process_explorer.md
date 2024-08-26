
## 4.1 Utilisation de Process Explorer

### Introduction :
Process Explorer est un outil de diagnostic puissant qui permet de surveiller et de gérer les processus en cours sur un système Windows. Cet exercice pratique vous guidera à travers l'utilisation de Process Explorer pour identifier, analyser, et résoudre des problèmes liés à des processus suspects ou malveillants.

### Étapes de l'Exercice :

1. **Téléchargement et Lancement de Process Explorer :**
   - Téléchargez **Process Explorer** depuis le site officiel des [Sysinternals](https://docs.microsoft.com/en-us/sysinternals/downloads/process-explorer).
   - Exécutez l'application en tant qu'administrateur pour accéder à toutes les fonctionnalités.
   - Vous verrez une interface qui affiche tous les processus en cours d'exécution, organisés sous forme d'arborescence pour montrer leurs relations hiérarchiques.

2. **Identification des Processus Suspects :**
   - Parcourez la liste des processus et cherchez ceux qui utilisent une quantité anormale de ressources CPU ou mémoire.
   - Recherchez des processus non signés ou ayant des noms ou des chemins d'accès inhabituels.
   - Cliquez sur un processus suspect pour voir ses propriétés détaillées, telles que son chemin d'accès, ses DLL chargées, et ses connexions réseau.

3. **Analyse des Détails du Processus :**
   - Dans les propriétés du processus, examinez les onglets **Image**, **Strings**, et **TCP/IP** pour détecter des comportements anormaux.
   - Utilisez l'onglet **Handles** pour voir quels fichiers et clés de registre sont ouverts par le processus.

4. **Suspension et Terminaison de Processus :**
   - Si un processus est identifié comme malveillant ou non nécessaire, vous pouvez le suspendre temporairement en cliquant sur **Suspend**.
   - Pour le terminer définitivement, utilisez l'option **Kill Process**, mais soyez prudent, car la suppression d'un processus système critique peut entraîner l'instabilité du système.

5. **Documentation et Rapport :**
   - Notez vos observations, y compris les processus suspects identifiés et les actions prises. Capturez des captures d'écran des propriétés du processus pour les inclure dans un rapport d'incident.

### Conclusion :
Cet exercice pratique vous permet de vous familiariser avec l'utilisation de Process Explorer pour surveiller et gérer les processus sur un système Windows. En maîtrisant cet outil, vous serez mieux préparé à identifier et à répondre aux menaces potentielles, ainsi qu'à optimiser les performances de votre système.
