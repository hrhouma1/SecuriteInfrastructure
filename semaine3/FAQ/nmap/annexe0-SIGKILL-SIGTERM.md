Les signaux `-9` (SIGKILL) et `-15` (SIGTERM) sont utilisés pour arrêter des processus sous Unix/Linux, mais ils se comportent différemment :

### `SIGTERM` (Signal 15)

- **Description** : Signal de terminaison.
- **Comportement** : Demande au processus de se terminer proprement. Le processus peut intercepter ce signal, effectuer des opérations de nettoyage (comme fermer des fichiers, libérer des ressources, etc.) avant de se terminer.
- **Utilisation** : Utilisé pour arrêter les processus de manière élégante, permettant aux processus de se terminer proprement.
- **Commande** : 
  ```sh
  pkill -15 apache2
  # ou simplement
  pkill apache2
  ```

### `SIGKILL` (Signal 9)

- **Description** : Signal de kill.
- **Comportement** : Force immédiatement l'arrêt du processus sans lui donner la possibilité d'effectuer des opérations de nettoyage. Le processus ne peut pas intercepter ce signal.
- **Utilisation** : Utilisé en dernier recours lorsque le processus ne répond pas au signal SIGTERM. Cela garantit l'arrêt immédiat du processus, mais peut entraîner des pertes de données ou des états incohérents.
- **Commande** : 
  ```sh
  pkill -9 apache2
  ```

### Comparaison

- **SIGTERM (-15)** :
  - Donne au processus la possibilité de se terminer proprement.
  - Le processus peut intercepter et gérer ce signal.
  - Utilisé pour des arrêts ordinaires et contrôlés.

- **SIGKILL (-9)** :
  - Force l'arrêt immédiat du processus.
  - Le processus ne peut pas intercepter ni gérer ce signal.
  - Utilisé en cas d'urgence lorsque le processus ne répond pas.

### Recommandation

Il est généralement recommandé de tenter d'arrêter un processus avec `SIGTERM` d'abord (en utilisant `pkill apache2` ou `pkill -15 apache2`), puis d'utiliser `SIGKILL` (`pkill -9 apache2`) seulement si le processus ne répond pas, afin de minimiser les risques de perte de données ou d'incohérence système.
