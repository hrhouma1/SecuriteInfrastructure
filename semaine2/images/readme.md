---
# Clés de registre :
---

| **Clé de registre**                                                             | **Description**                                                                                                          | **Application**                         |
|---------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------|------------------------------------------|
| **HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Run**             | Spécifie les programmes à exécuter automatiquement au démarrage de la session utilisateur actuellement connectée.         | Utilisateur actuel seulement             |
| **HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\RunOnce**         | Exécute une seule fois les programmes spécifiés après la connexion de l'utilisateur, puis supprime la valeur du registre. | Utilisateur actuel seulement             |
| **HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Run**            | Spécifie les programmes à exécuter automatiquement pour tous les utilisateurs lors de la connexion.                       | Tous les utilisateurs du système         |
| **HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\RunOnce**        | Exécute une seule fois les programmes spécifiés au démarrage du système pour tous les utilisateurs, puis supprime la valeur du registre. | Tous les utilisateurs du système         |

---
# Explications détaillées :
---
Les emplacements mentionnés dans la table ci-haut sont des clés de registre dans Windows utilisées pour configurer l'exécution automatique de programmes au démarrage du système ou lors de la connexion d'un utilisateur. Voici une définition technique pour chaque clé :

1. **HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Run** :
   - Cette clé de registre contient les valeurs qui spécifient les programmes devant être exécutés automatiquement au démarrage de la session utilisateur actuellement connectée. Les programmes définis ici sont spécifiques à l'utilisateur actuel.

2. **HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\RunOnce** :
   - Semblable à la clé précédente, mais les programmes spécifiés dans cette clé sont exécutés une seule fois après la connexion de l'utilisateur, puis la valeur correspondante est supprimée du registre.

3. **HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Run** :
   - Cette clé est utilisée pour les programmes devant être exécutés automatiquement pour **tous** les utilisateurs lors de la connexion. Les programmes définis dans cette clé sont exécutés indépendamment de l'utilisateur connecté, s'appliquant à l'ensemble du système.

4. **HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\RunOnce** :
   - Comme la clé `RunOnce` sous `HKEY_CURRENT_USER`, cette clé exécute les programmes spécifiés une seule fois au démarrage du système. Après exécution, les entrées correspondantes sont supprimées du registre. Cela s'applique à tous les utilisateurs.

Ces clés sont utilisées pour configurer des tâches d'initialisation, des programmes de démarrage automatique ou des opérations post-installation qui doivent s'exécuter soit à chaque démarrage, soit une seule fois, en fonction de leur emplacement.
