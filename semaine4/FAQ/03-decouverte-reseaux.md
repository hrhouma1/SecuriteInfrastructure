## Utilisation de PowerShell

Ouvrez une invite PowerShell en tant qu'administrateur et exécutez la commande suivante :

```powershell
Set-NetFirewallRule -DisplayGroup "Découverte du réseau" -Enabled True -Profile Private
```

Cette commande active les règles de pare-feu pour la découverte réseau sur le profil de réseau privé.

## Utilisation de l'Invite de commandes

Ouvrez une Invite de commandes en tant qu'administrateur et exécutez la commande suivante :

```cmd
netsh advfirewall firewall set rule group="Découverte du réseau" new enable=Yes
```

Cette commande active les règles de pare-feu pour la découverte réseau.

## Étapes supplémentaires

Si la découverte réseau ne fonctionne toujours pas après avoir exécuté ces commandes, vous pouvez essayer les étapes suivantes :

1. Activer les services requis :
   Ouvrez une Invite de commandes ou PowerShell en tant qu'administrateur et exécutez les commandes suivantes :

   ```cmd
   sc config FDResPub start= auto
   sc start FDResPub
   sc config SSDPSRV start= auto
   sc start SSDPSRV
   sc config upnphost start= auto
   sc start upnphost
   sc config DNSCache start= auto
   sc start DNSCache
   ```

2. Réinitialiser les paramètres réseau :
   Si le problème persiste, essayez de réinitialiser les paramètres réseau avec PowerShell :

   ```powershell
   Get-NetAdapter | Restart-NetAdapter
   ```

3. Activer la découverte réseau dans les paramètres de partage avancés :
   Si les méthodes en ligne de commande ne fonctionnent pas, vous devrez peut-être activer manuellement la découverte réseau via l'interface graphique :

   - Ouvrez le Panneau de configuration
   - Allez dans Centre Réseau et partage > Paramètres de partage avancés
   - Développez le profil "Privé"
   - Sélectionnez "Activer la découverte réseau" et "Activer le partage de fichiers et d'imprimantes"
   - Cliquez sur "Enregistrer les modifications"

En suivant ces étapes, vous devriez pouvoir activer la découverte réseau sur votre système Windows Server 2019. Si vous continuez à rencontrer des problèmes, il peut être nécessaire d'examiner d'autres causes potentielles telles que les paramètres du pare-feu ou la configuration du réseau.
