![image](https://github.com/user-attachments/assets/69b185a4-cd9a-4de2-9736-602ae6dd12f5)


# solution 01


### Configuration de base

1. **Vérifier que WinRM est activé :**
   - Ouvrez PowerShell en tant qu'administrateur.
   - Exécutez la commande suivante pour configurer et démarrer WinRM :
     ```bash
     winrm quickconfig
     ```

2. **Configurer le pare-feu pour HTTP (port 5985) :**
   - Ajoutez une règle pour autoriser les connexions entrantes sur le port 5985 :
     ```bash
     netsh advfirewall firewall add rule name="WinRM-HTTP" dir=in action=allow protocol=TCP localport=5985
     ```

3. **Configurer le pare-feu pour HTTPS (port 5986) :**
   - Ajoutez une règle pour autoriser les connexions entrantes sur le port 5986 :
     ```bash
     netsh advfirewall firewall add rule name="WinRM-HTTPS" dir=in action=allow protocol=TCP localport=5986
     ```

### Configuration HTTPS

4. **Configurer WinRM pour HTTPS :**
   - Assurez-vous d'avoir un certificat SSL valide installé.
   - Exécutez la commande suivante en remplaçant `<NomDeVotreMachine>` et `<EmpreinteDuCertificat>` par les valeurs appropriées :
     ```bash
     winrm create winrm/config/Listener?Address=*+Transport=HTTPS '@{Hostname="<NomDeVotreMachine>";CertificateThumbprint="<EmpreinteDuCertificat>"}'
     ```

### Vérifications supplémentaires

5. **Vérifier l'accessibilité réseau :**
   - Assurez-vous que la machine cible est accessible sur le réseau et que le nom d'hôte est correct.

6. **Vérifier les paramètres de sécurité :**
   - Assurez-vous que les configurations de sécurité pour l'authentification sont correctement définies.
   - Activez l'authentification basique si nécessaire pour des tests (non recommandé en production) :
     ```bash
     winrm set winrm/config/service/auth '@{Basic="true"}'
     ```

En suivant ces étapes, vous devriez pouvoir résoudre le problème de connexion avec le service WinRM. Assurez-vous également que toutes les machines impliquées sont correctement configurées pour accepter les connexions à distance.





# solution 02

![image](https://github.com/user-attachments/assets/4c090769-8394-4158-8b8a-39874e9200f0)


- Il s'agit d'un problème lors de la connexion à un service Windows Remote Management (WinRM) sur un domaine appelé `DC.Contoso.net`. 
- L'erreur indiquée dans le second message d'erreur vous suggère de vérifier si le service est en cours d'exécution et d'accepter les demandes sur la destination spécifiée.

Pour résoudre ce problème, vous pouvez suivre ces étapes:

1. **Vérifier que WinRM est activé et configuré:**
   - Ouvrez une invite de commandes en tant qu'administrateur.
   - Exécutez la commande suivante pour configurer et activer WinRM:
     ```
     winrm quickconfig
     ```
   - Répondez 'Oui' aux prompts pour permettre les modifications nécessaires. Cette commande configure le service WinRM pour démarrer automatiquement et configure le pare-feu pour autoriser les messages de gestion à passer.

2. **Vérifier l'état du service WinRM:**
   - Toujours dans l'invite de commandes administateur, exécutez:
     ```
     winrm e winrm/config/listener
     ```
   - Cela vous montre les configurations actuelles du listener WinRM. Assurez-vous qu'il écoute les adresses IP appropriées ou sur toutes les adresses si c'est votre configuration.

3. **Vérifier les paramètres de pare-feu:**
   - Assurez-vous que le pare-feu autorise les connexions au service WinRM. Cela peut nécessiter de vérifier les règles du pare-feu pour les ports 5985 (HTTP) ou 5986 (HTTPS).

4. **Assurer que l'ordinateur cible est accessible:**
   - Si vous êtes dans un environnement réseau, assurez-vous qu'il n'y a pas de règles de pare-feu réseau ou de groupes de sécurité bloquant les connexions entre votre machine et `DC.Contoso.net`.

5. **Tester la connectivité réseau:**
   - Utilisez la commande `ping` ou `tracert` pour vérifier que la machine cible est réseau accessible.

6. **Journal des événements:**
   - Consultez le journal des événements Windows pour plus de détails sur les erreurs liées à WinRM ou les tentatives de connexion.

Si après ces vérifications, le problème persiste, il peut être utile de revoir toute configuration spécifique de sécurité ou de domaine qui pourrait influencer la gestion des services ou les communications réseau dans votre environnement.
