
## 4.2 Utilisation d'Autoruns

### Introduction :
Autoruns est un outil essentiel pour identifier et gérer les programmes configurés pour démarrer automatiquement avec Windows. Cet exercice vous guidera à travers l'utilisation d'Autoruns pour détecter et supprimer des logiciels indésirables ou malveillants qui s'exécutent au démarrage du système.

### Étapes de l'Exercice :

1. **Téléchargement et Lancement d'Autoruns :**
   - Téléchargez **Autoruns** depuis le site officiel des [Sysinternals](https://docs.microsoft.com/en-us/sysinternals/downloads/autoruns).
   - Exécutez l'application en tant qu'administrateur pour avoir une vue complète de tous les emplacements de démarrage.
   - L'interface affiche plusieurs onglets représentant différents types d'emplacements de démarrage (Logon, Services, Drivers, Scheduled Tasks, etc.).

2. **Examen des Programmes de Démarrage :**
   - Parcourez chaque onglet pour identifier les programmes configurés pour démarrer automatiquement.
   - Recherchez des programmes inconnus ou suspects, surtout ceux dans les emplacements moins connus comme **Services** ou **Drivers**.

3. **Vérification des Entrées :**
   - Faites un clic droit sur une entrée suspecte et sélectionnez **Search Online** pour rechercher des informations sur l'exécutable. Cela peut révéler si le programme est légitime ou potentiellement malveillant.
   - Utilisez la fonctionnalité de **vérification des signatures** pour vous assurer que les programmes de démarrage sont signés par des éditeurs de confiance.

4. **Désactivation ou Suppression d'Entrées :**
   - Décochez la case à côté d'une entrée pour désactiver le programme au démarrage.
   - Pour supprimer définitivement une entrée, faites un clic droit et sélectionnez **Delete**. Assurez-vous que l'entrée est bien indésirable avant de la supprimer.

5. **Sauvegarde Avant Modification :**
   - Avant de faire des modifications majeures, il est recommandé de créer un point de restauration du système. Cela permet de revenir en arrière en cas de problème après la suppression ou la désactivation d'une entrée.

6. **Documentation et Rapport :**
   - Documentez vos actions, y compris les programmes désactivés ou supprimés. Capturez des captures d'écran des entrées suspectes pour les inclure dans un rapport d'incident.

### Conclusion :
En suivant cet exercice pratique, vous apprendrez à utiliser Autoruns pour gérer efficacement les programmes de démarrage sur un système Windows. Cela vous aidera à éliminer les logiciels indésirables ou malveillants qui peuvent compromettre la sécurité ou les performances de votre système.
