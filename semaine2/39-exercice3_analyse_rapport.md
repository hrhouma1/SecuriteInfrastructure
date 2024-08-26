
## 6.4 Exercice 3 : Analyser une Machine Infectée avec les Outils Sysinternals et Rédiger un Rapport d'Incident

### Objectif :
Cet exercice vise à vous familiariser avec l'utilisation des outils Sysinternals pour analyser une machine virtuelle simulant une infection par un logiciel malveillant. L'exercice se termine par la rédaction d'un rapport d'incident complet.

### Étapes de l'Exercice :

1. **Préparation de la Machine Virtuelle :**
   - **Simulation de l'infection :**
     - Téléchargez et exécutez un fichier de démonstration de malware ou configurez manuellement une machine virtuelle pour inclure des signes d'infection, tels que des processus inconnus au démarrage.
     - **Note importante :** Utilisez uniquement des échantillons de malware sécurisés et contrôlés, ou créez des scénarios manuellement pour éviter tout dommage accidentel à votre système.

2. **Analyse avec Process Explorer :**
   - **Lancer Process Explorer :**
     - Exécutez **Process Explorer** en tant qu'administrateur.
   - **Examiner les processus actifs :**
     - Recherchez des processus qui utilisent une quantité anormale de ressources CPU ou mémoire.
     - **Vérification des signatures numériques :** Faites un clic droit sur un processus suspect et sélectionnez **Properties**, puis vérifiez si le fichier exécutable est signé par un éditeur de confiance.

3. **Analyse avec Autoruns :**
   - **Examen des programmes de démarrage :**
     - Exécutez **Autoruns** en tant qu'administrateur.
     - Parcourez les différents onglets (Logon, Services, Scheduled Tasks) pour identifier les programmes qui se lancent automatiquement.
     - **Désactivation des entrées suspectes :** Décochez toute entrée inconnue ou suspecte liée à l'infection présumée.

4. **Évaluation des Modifications Système avec Process Monitor :**
   - **Suivi des activités suspectes :**
     - Utilisez **Process Monitor** pour capturer les modifications en temps réel apportées par les processus malveillants, tels que l'écriture de fichiers ou la modification du registre.
   - **Capture des événements :** Filtrez les événements pour ne visualiser que ceux liés aux actions malveillantes suspectées.

5. **Rédaction du Rapport d'Incident :**
   - **Résumé de l'Incident :**
     - Décrivez le scénario d'infection, y compris les symptômes observés et les impacts sur la machine.
   - **Outils Utilisés :**
     - Listez les outils Sysinternals utilisés pour l'analyse et expliquez comment chaque outil a contribué à l'investigation.
   - **Résultats :**
     - Détaillez les processus suspects identifiés, les modifications système détectées, et les étapes de correction prises.
   - **Recommandations :**
     - Fournissez des conseils pour prévenir de futures infections et améliorer la sécurité du système.

### Conclusion :
Cet exercice vous a permis de pratiquer l'analyse d'un système infecté en utilisant les outils Sysinternals et de documenter vos découvertes dans un rapport d'incident complet. Cette approche vous aide à mieux comprendre le processus d'investigation des incidents de sécurité et à formuler des recommandations pour renforcer la sécurité des systèmes.
