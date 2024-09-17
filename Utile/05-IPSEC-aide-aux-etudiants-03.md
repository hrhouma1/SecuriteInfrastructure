# Activation et la configuration d'IPsec dans les sections pertinentes concernant la sécurisation du trafic entre vos machines,
##  entre le serveur SRV et les autres machines du réseau

### Partie 2 : Analyse du trafic lié au partage SMB

Après la section sur l'installation et la configuration de l'outil d'analyse de réseau (Wireshark ou un autre outil en remplacement de Microsoft Message Analyzer), 
vous pouvez ajouter une sous-section spécifique à IPsec :

#### Configuration d'IPsec pour sécuriser le partage SMB

**Activez IPsec pour chiffrer le trafic SMB entre le serveur SRV et les autres machines du réseau**:

1. **Sur le Domain Controller (DC)**, configurez les politiques de sécurité pour utiliser IPsec dans les communications SMB. Cela garantira que toutes les données transitant entre les serveurs sont chiffrées et sécurisées.

   ```powershell
   # Créer et configurer une nouvelle règle IPsec pour le chiffrement SMB
   New-NetIPsecRule -DisplayName "SMB Encryption" -PolicyStore "Contoso.net" -InboundSecurity Require -OutboundSecurity Require -Protocol TCP -LocalPort 445 -RemotePort 445 -Action Encrypt
   ```

   🔍 **Explication**:
   - Cette commande crée une règle IPsec qui exige un chiffrement pour le trafic SMB (port 445) pour les communications entrantes et sortantes, sécurisant ainsi les données échangées sur le réseau.

2. **Appliquez et mettez à jour les politiques sur toutes les machines concernées**:
   ```cmd
   gpupdate /force
   ```
   Cette commande force la mise à jour des politiques sur chaque machine, assurant que les nouvelles configurations d'IPsec sont prises en compte immédiatement.

3. **Testez la communication SMB chiffrée**:
   - Assurez-vous que le trafic SMB entre SRV et les autres machines est maintenant chiffré. Vous pouvez utiliser Wireshark pour observer que les paquets SMB sont encapsulés dans ESP (Encapsulating Security Payload), indiquant que le trafic est chiffré par IPsec.

### Conseils d'Utilisation et Bonnes Pratiques

- **Vérification de la sécurité**: Testez régulièrement la sécurité des communications SMB avec IPsec pour vous assurer qu'il n'y a pas de failles ou de fuites de données.
- **Documentation**: Documentez toutes les configurations et modifications apportées pour le déploiement d'IPsec, fournissant une référence claire pour l'audit et la maintenance du système de sécurité.

