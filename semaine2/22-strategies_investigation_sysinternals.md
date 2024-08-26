
## 3.2 Stratégies d'Investigation avec Sysinternals

### Introduction :
L'utilisation des outils Sysinternals, tels que Process Explorer et Autoruns, est cruciale pour mener des investigations approfondies sur un système Windows. Ces outils permettent de détecter des activités suspectes, de diagnostiquer des problèmes de performance, et de sécuriser le système en identifiant les logiciels malveillants et les configurations indésirables.

### Approche Globale :
1. **Collecte d'Informations :**
   - Commencez par collecter un maximum d'informations sur l'état actuel du système en utilisant Process Explorer pour examiner les processus en cours et Autoruns pour répertorier les programmes de démarrage. Identifiez tout élément qui semble inhabituel ou suspect.

2. **Analyse des Processus :**
   - Utilisez Process Explorer pour examiner en détail les processus actifs. Recherchez des processus non signés ou ayant des chemins d'accès inhabituels. Analysez les handles et les DLLs associés pour détecter toute activité malveillante.

3. **Vérification des Points de Démarrage :**
   - Avec Autoruns, passez en revue tous les points de démarrage pour identifier les programmes qui se lancent automatiquement. Désactivez ou supprimez les éléments qui ne sont pas nécessaires ou qui sont potentiellement dangereux.

4. **Corrélation des Résultats :**
   - Corrélez les informations obtenues avec d'autres sources, telles que les journaux d'événements Windows ou les journaux Sysmon, pour obtenir une vue d'ensemble de la situation. Cela permet de confirmer ou d'infirmer les soupçons de compromission.

### Précautions et Meilleures Pratiques :
1. **Vérification des Signatures :**
   - Utilisez toujours la vérification des signatures numériques dans Process Explorer pour vous assurer que les processus sont légitimes. Les processus non signés ou mal signés doivent être examinés attentivement.

2. **Surveillance Continue :**
   - Intégrez l'utilisation des outils Sysinternals dans une stratégie de surveillance continue pour détecter les anomalies dès qu'elles surviennent. Cela peut inclure l'exécution régulière d'Autoruns et la surveillance des processus critiques avec Process Explorer.

3. **Documentation :**
   - Documentez chaque étape de votre investigation, y compris les captures d'écran des outils Sysinternals et les actions prises. Cette documentation sera précieuse pour les rapports post-mortem et pour améliorer les stratégies de réponse aux incidents.

4. **Isolation et Remédiation :**
   - En cas de détection d'un processus malveillant ou d'une configuration suspecte, isolez immédiatement la machine compromise du réseau pour limiter les dommages. Ensuite, procédez à la remédiation en supprimant les processus et les programmes suspects.

### Conclusion :
L'utilisation stratégique des outils Sysinternals est essentielle pour toute investigation de sécurité sur un système Windows. Leur capacité à fournir des informations détaillées et à détecter des anomalies en fait des outils indispensables pour les professionnels de la sécurité et les administrateurs système.
