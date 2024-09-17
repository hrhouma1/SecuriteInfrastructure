# Objectif : 
- configurer et gérer IPsec via la ligne de commande sur Windows, vous pouvez utiliser PowerShell ou des outils spécifiques comme `netsh`.
- Voici comment vous pourriez configurer IPsec pour le chiffrement SMB en utilisant PowerShell et `netsh` :

### 1. **Configurer IPsec avec PowerShell**

PowerShell offre des cmdlets robustes pour gérer la sécurité, y compris IPsec. Voici un exemple de base pour configurer une politique IPsec qui pourrait être adapté à vos besoins :

```powershell
# Créer une nouvelle règle de sécurité IPsec
New-NetIPsecRule -DisplayName "SMB Encryption" -Direction Inbound -Action RequireInbound -Protocol TCP -LocalPort 445 -RemotePort 445 -InboundSecurity Require -DynamicPeer 0

# Activer la règle
Enable-NetIPsecRule -DisplayName "SMB Encryption"
```

- **New-NetIPsecRule**: Crée une nouvelle règle IPsec. Ici, elle est configurée pour chiffrer le trafic SMB (port 445) qui est typiquement utilisé pour les communications de fichier.
- **Enable-NetIPsecRule**: Active la règle spécifiée.

### 2. **Configurer IPsec avec `netsh`**

`netsh` est un autre outil puissant pour configurer et diagnostiquer le réseau y compris IPsec. Voici comment on pourrait utiliser `netsh` pour configurer IPsec :

```cmd
# Ajouter une règle de sécurité IPsec pour chiffrer le trafic SMB
netsh advfirewall consec add rule name="SMB Encryption" enable=yes mode=transport action=requirein requestsecurity=in authenticate=no

# Définir des paramètres spécifiques pour la règle
netsh advfirewall set rule group="SMB Encryption" new enable=yes profile=domain
```

- **advfirewall consec add rule**: Ajoute une règle de sécurité dans les configurations de pare-feu avancées pour exiger le chiffrement.
- **set rule**: Configure les règles existantes pour spécifier les profils ou les paramètres.

### Conseils pour la Configuration et le Débogage

- **Testez la Connectivité**: Après avoir configuré IPsec, testez la connectivité entre les hôtes pour vous assurer que le trafic est correctement chiffré et que les communications fonctionnent comme prévu. Utilisez `ping`, `telnet`, ou des outils plus avancés comme Wireshark pour analyser le trafic.
- **Logs et Diagnostic**: Utilisez les logs de Windows et des outils comme l'Observateur d'événements pour surveiller et diagnostiquer les activités IPsec. Des commandes comme `Get-EventLog` ou des outils graphiques peuvent aider à identifier des problèmes de configuration ou de réseau.
- **Documentation**: Assurez-vous de documenter chaque commande et configuration appliquée pour faciliter les audits de sécurité et le dépannage futur.

Ces lignes de commande vous permettront de configurer et de gérer IPsec pour sécuriser le trafic SMB ou d'autres communications dans votre infrastructure Windows.
