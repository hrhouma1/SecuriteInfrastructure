# Configuration et l'utilisation de **EFS (Encrypting File System)** sous **Windows 10 Professionnel**

### Partie 1 : Concepts de base EFS
1. **Qu'est-ce que le système de fichiers EFS sur Windows ?**
   - A) Un système de fichiers pour partager des fichiers en réseau
   - B) Un système permettant de chiffrer des fichiers pour sécuriser les données
   - C) Un système de gestion des fichiers temporaires

2. **Quel est l'objectif principal d'EFS ?**
   - A) Améliorer la vitesse d'accès aux fichiers
   - B) Protéger les fichiers contre l'accès non autorisé par chiffrement
   - C) Sauvegarder automatiquement les fichiers

3. **Quelle commande PowerShell permet de chiffrer un fichier avec EFS ?**
   - A) `Get-Item`
   - B) `Encrypt-File`
   - C) `(Get-Item <path>).Encrypt()`

4. **Quels types de fichiers peuvent être chiffrés avec EFS ?**
   - A) Uniquement les fichiers texte
   - B) Tout type de fichier
   - C) Uniquement les fichiers créés par l’administrateur

5. **Dans quel cas le chiffrement d’un fichier avec EFS est-il automatiquement annulé ?**
   - A) Lors du changement de format de fichier
   - B) Lors du déplacement du fichier vers un système de fichiers non pris en charge
   - C) Lors du changement de propriétaire du fichier

### Partie 2 : Utilisation de PowerShell pour EFS
6. **Quelle commande permet de créer un fichier texte avec PowerShell ?**
   - A) `New-File`
   - B) `Set-Content`
   - C) `Create-File`

7. **Quelle commande PowerShell permet de vérifier si un fichier est chiffré ?**
   - A) `Get-ItemProperty`
   - B) `Test-Path`
   - C) `Get-Item | Select-Object Name, Attributes`

8. **Comment déchiffrer un fichier avec PowerShell ?**
   - A) `(Get-Item <path>).Decrypt()`
   - B) `Remove-Encryption`
   - C) `(Get-Item <path>).Unblock()`

9. **Quelle commande permet de vider le cache des clés EFS ?**
   - A) `Clear-EfsCache`
   - B) `cipher /flushcache`
   - C) `Remove-EfsCache`

10. **Comment exporter un certificat EFS avec PowerShell ?**
    - A) `Export-PfxCertificate`
    - B) `Export-CertificateEFS`
    - C) `Backup-Certificate`

### Partie 3 : Gestion des utilisateurs et des certificats EFS
11. **Quelle commande permet de créer un utilisateur local dans PowerShell ?**
    - A) `Add-User`
    - B) `New-LocalUser`
    - C) `Create-User`

12. **Quelle commande PowerShell permet de lister les certificats EFS dans le magasin personnel ?**
    - A) `Get-Certificate`
    - B) `Get-ChildItem -Path Cert:\CurrentUser\My`
    - C) `List-EfsCertificates`

13. **Que se passe-t-il si le certificat EFS est supprimé après chiffrement d’un fichier ?**
    - A) Le fichier reste accessible mais non sécurisé
    - B) Le fichier devient inaccessible
    - C) Le fichier est automatiquement déchiffré

14. **Comment restaurer un certificat EFS exporté avec PowerShell ?**
    - A) `Import-PfxCertificate`
    - B) `Restore-EfsCertificate`
    - C) `Reinstall-Certificate`

15. **Quelle commande permet de supprimer un certificat EFS du magasin personnel ?**
    - A) `Remove-Item -Path Cert:\CurrentUser\My`
    - B) `Clear-Certificate`
    - C) `Delete-Certificate`

### Partie 4 : Tests de l’accès et sécurité
16. **Que se passe-t-il lorsqu'un utilisateur non autorisé tente d'accéder à un fichier chiffré ?**
    - A) Il peut ouvrir le fichier sans restrictions
    - B) Il reçoit un message d'accès refusé
    - C) Le fichier est déchiffré automatiquement pour cet utilisateur

17. **Quelle commande PowerShell permet de tester l’accès d’un utilisateur à un fichier chiffré ?**
    - A) `Test-Access -File <path>`
    - B) `Get-Content <path>`
    - C) `Check-FileAccess`

18. **Quelle commande PowerShell est utilisée pour créer un dossier chiffré ?**
    - A) `New-EncryptedFolder`
    - B) `cipher /e <path>`
    - C) `New-Item -Type Directory | Encrypt`

19. **Quels sont les attributs typiques d’un fichier chiffré visible via PowerShell ?**
    - A) `A` pour archivé
    - B) `E` pour chiffré
    - C) `S` pour système

20. **Comment un utilisateur peut-il accéder à un fichier chiffré après avoir supprimé son certificat EFS ?**
    - A) En déchiffrant manuellement le fichier
    - B) En restaurant son certificat EFS
    - C) En demandant un nouveau certificat au système

### Partie 5 : Scénarios pratiques EFS
21. **Après avoir exporté un certificat EFS, où doit-on le stocker ?**
    - A) Sur le disque local uniquement
    - B) Sur un stockage sécurisé comme une clé USB
    - C) Il n'est pas nécessaire de le sauvegarder

22. **Comment vérifier si un fichier a été chiffré avec succès via PowerShell ?**
    - A) `Get-Item <path> | Select-Object Attributes`
    - B) `cipher /status <path>`
    - C) `Verify-EncryptionStatus`

23. **Comment tester qu'un autre utilisateur n'a pas accès à un fichier chiffré EFS ?**
    - A) En changeant de session et en tentant d’ouvrir le fichier
    - B) En modifiant le fichier
    - C) En supprimant les attributs de chiffrement du fichier

24. **Quelle commande PowerShell peut être utilisée pour forcer la mise à jour des certificats EFS après une modification de GPO ?**
    - A) `gpupdate /force`
    - B) `update-efs`
    - C) `refresh-certificates`

25. **Quel est le rôle de `certmgr.msc` dans la gestion EFS ?**
    - A) Gérer les politiques de groupe
    - B) Gérer les certificats de chiffrement EFS
    - C) Gérer les utilisateurs locaux

### Partie 6 : Concepts avancés et sécurité
26. **Quel algorithme est généralement utilisé par EFS pour chiffrer les fichiers ?**
    - A) DES
    - B) AES-256
    - C) MD5

27. **Que faut-il faire pour s’assurer qu’un fichier reste chiffré après avoir été déplacé sur un autre volume ?**
    - A) Le déplacer sans action supplémentaire
    - B) Rechiffrer le fichier après le déplacement
    - C) Vérifier que le volume de destination supporte EFS

28. **Quelles sont les conséquences d’une perte définitive du certificat EFS ?**
    - A) Les fichiers chiffrés restent accessibles
    - B) Les fichiers chiffrés deviennent inaccessibles
    - C) Le certificat peut être régénéré sans perte d'accès

29. **Comment s’assurer que les clés de chiffrement EFS ne sont pas stockées en mémoire ?**
    - A) En redémarrant le système après le chiffrement
    - B) En utilisant la commande `cipher /k` pour créer une nouvelle clé
    - C) En supprimant le fichier chiffré

30. **Comment restaurer un fichier chiffré EFS dans un environnement après avoir restauré son certificat ?**
    - A) En lançant une commande `Restore-EfsFile`
    - B) En important le certificat et en réessayant d’accéder au fichier
    - C) En supprimant et en recréant le fichier

