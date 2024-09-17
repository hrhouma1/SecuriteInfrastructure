Si vous préférez configurer IPsec sans utiliser PowerShell, vous pouvez opter pour l'utilisation de l'interface graphique Windows 
ou des outils de configuration réseau traditionnels tels que `netsh`. 

# Démo : comment procéder avec ces méthodes ?

### Utilisation de l'interface graphique Windows pour IPsec

**Configurer IPsec via la console de gestion des stratégies de sécurité (secpol.msc) :**

1. **Ouvrir la console de gestion de politique de sécurité**:
   - Tapez `secpol.msc` dans la barre de recherche Windows et ouvrez l'outil.

2. **Naviguer vers les règles de sécurité IPsec**:
   - Dans la console, allez dans `Stratégies de sécurité IPsec` sous `Stratégies locales`.

3. **Créer une nouvelle politique IPsec**:
   - Cliquez droit sur `Stratégies de sécurité IPsec`, puis sélectionnez `Créer une politique IPsec...`.
   - Donnez un nom à votre politique et configurez les paramètres selon vos besoins, comme le choix des méthodes de chiffrement et d'authentification.

4. **Ajouter des règles à votre politique IPsec**:
   - Une fois la politique créée, vous pouvez ajouter des règles spécifiques pour gérer le trafic SMB ou autre.
   - Configurez les filtres de trafic pour spécifier quel trafic doit être sécurisé (par exemple, le trafic sur le port TCP 445 pour SMB).

5. **Activer la politique**:
   - Après avoir configuré les règles, activez la politique pour qu'elle prenne effet immédiatement ou selon votre calendrier prévu.

### Utilisation de `netsh` pour configurer IPsec

**Configurer IPsec avec `netsh` pour le trafic SMB** :

1. **Ouvrir l'invite de commande en tant qu'administrateur** :
   - Recherchez `cmd`, cliquez droit et sélectionnez `Exécuter en tant qu'administrateur`.

2. **Ajouter une règle de sécurité IPsec** :
   ```cmd
   netsh ipsec static add policy name="SMB Security Policy" description="Encrypt SMB Traffic"
   netsh ipsec static add filterlist name="SMB Filter List"
   netsh ipsec static add filter filterlist="SMB Filter List" srcaddr=any dstaddr=me dstport=445 protocol=TCP
   netsh ipsec static add filteraction name="Request Security" action=request
   netsh ipsec static add rule name="Encrypt SMB Traffic" policy="SMB Security Policy" filterlist="SMB Filter List" filteraction="Request Security"
   ```

3. **Activer la politique IPsec** :
   ```cmd
   netsh ipsec static set policy name="SMB Security Policy" assign=y
   ```

- **Ces commandes créent une politique, une liste de filtres, une action de filtre, et une règle pour sécuriser le trafic SMB (port 445), en demandant la sécurité (chiffrement).**

### Conseils pour le dépannage et la vérification

- **Vérifiez la configuration** :
   - Vous pouvez vérifier l'état de la politique IPsec et des règles associées via `netsh ipsec static show policy` et d'autres commandes similaires pour voir les détails de configuration.
- **Tester la connectivité** :
   - Utilisez des outils comme `telnet` ou `Test-NetConnection` pour tester la connectivité au port 445 et vous assurer que le trafic est sécurisé comme prévu.
- **Journalisation et surveillance** :
   - Assurez-vous que la journalisation est activée pour IPsec pour aider au dépannage. Utilisez l'Observateur d'événements Windows pour voir les logs liés à IPsec.

En utilisant ces méthodes, vous pouvez configurer et gérer IPsec sans PowerShell, tout en bénéficiant d'une interface utilisateur graphique ou de commandes classiques pour une intégration transparente dans vos processus de gestion de la sécurité réseau.
