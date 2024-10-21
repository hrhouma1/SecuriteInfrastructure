# EFS - Encrypting File System

1. **Quelle version de Windows inclut nativement EFS ?**
   - A) Windows XP Home
   - B) Windows 10 Pro
   - C) Windows Vista Home
   - D) Windows 95

2. **Quel type de chiffrement utilise EFS ?**
   - A) Chiffrement symétrique
   - B) Chiffrement asymétrique
   - C) Hashing
   - D) Compression des fichiers

3. **Quel élément est utilisé pour chiffrer les fichiers avec EFS ?**
   - A) Mot de passe utilisateur
   - B) Clé publique
   - C) Clé privée
   - D) Certificat réseau

4. **Quelle clé est nécessaire pour déchiffrer un fichier EFS ?**
   - A) Clé publique
   - B) Clé privée
   - C) Mot de passe administrateur
   - D) Clé de session

5. **Où sont stockées les clés de chiffrement EFS sur un système Windows ?**
   - A) Base de registre
   - B) BIOS
   - C) TPM (Trusted Platform Module)
   - D) Certificat de l'utilisateur

6. **Quelle action est requise pour qu’un fichier soit chiffré avec EFS ?**
   - A) Copier le fichier sur un disque externe
   - B) Activer l'option de chiffrement dans les propriétés du fichier
   - C) Renommer le fichier
   - D) Lancer un script spécifique

7. **EFS peut-il protéger un fichier contre un accès non autorisé via le réseau ?**
   - A) Oui
   - B) Non
   - C) Seulement sur un réseau local
   - D) Seulement sur les réseaux VPN

8. **Que se passe-t-il si un fichier EFS est déplacé vers un système de fichiers non compatible avec EFS ?**
   - A) Il reste chiffré
   - B) Il est déchiffré automatiquement
   - C) Il est détruit
   - D) Il génère une alerte d'erreur

9. **Quel rôle joue un agent de récupération dans EFS ?**
   - A) Il récupère les fichiers supprimés
   - B) Il permet de déchiffrer les fichiers si la clé est perdue
   - C) Il génère de nouvelles clés de chiffrement
   - D) Il réinitialise le mot de passe

10. **Quel type d'algorithme de chiffrement est utilisé par EFS dans les versions modernes de Windows ?**
   - A) DES
   - B) RSA
   - C) AES
   - D) Blowfish

11. **Qui peut accéder à un fichier chiffré par EFS ?**
   - A) Tout utilisateur ayant un compte sur l'ordinateur
   - B) L'utilisateur qui a chiffré le fichier et les agents de récupération
   - C) L'administrateur du réseau
   - D) Aucun utilisateur ne peut y accéder

12. **Qu'arrive-t-il à un fichier EFS lorsque l'utilisateur exporte la clé privée associée ?**
   - A) Le fichier est automatiquement déchiffré
   - B) La clé privée ne peut pas être exportée
   - C) L'accès au fichier reste le même, mais la clé peut être utilisée ailleurs
   - D) Le fichier est verrouillé

13. **Peut-on chiffrer un dossier entier avec EFS ?**
   - A) Non, EFS ne fonctionne que sur les fichiers
   - B) Oui, mais seulement sur des disques externes
   - C) Oui, EFS peut chiffrer des dossiers
   - D) Oui, mais seulement sur les réseaux partagés

14. **Quel est le rôle de la clé publique dans EFS ?**
   - A) Elle sert à déchiffrer les fichiers
   - B) Elle protège le mot de passe utilisateur
   - C) Elle est utilisée pour chiffrer les fichiers
   - D) Elle remplace la clé privée en cas de perte

15. **Quelle commande est utilisée pour désactiver le chiffrement EFS sur un fichier ?**
   - A) `cipher /d`
   - B) `chkdsk /f`
   - C) `efsremove`
   - D) `netsh efs stop`

16. **Que doit faire un utilisateur s'il oublie sa clé privée EFS ?**
   - A) Contacter l'administrateur système pour récupérer le fichier
   - B) Utiliser une copie de sauvegarde
   - C) Déchiffrer le fichier avec la clé publique
   - D) Rechercher une application tierce pour contourner EFS

17. **Dans quel cas un fichier chiffré par EFS pourrait-il être compromis ?**
   - A) Si la clé privée est partagée
   - B) Si le fichier est renommé
   - C) Si l'ordinateur est éteint brutalement
   - D) Si l'utilisateur supprime le fichier

18. **Que se passe-t-il si un utilisateur chiffré en EFS partage son fichier avec un autre utilisateur sans lui donner la clé privée ?**
   - A) Le fichier est déchiffré pour l'autre utilisateur
   - B) L'autre utilisateur ne peut pas ouvrir le fichier
   - C) L'autre utilisateur peut ouvrir le fichier mais en lecture seule
   - D) Le fichier est modifié pour retirer le chiffrement

19. **Quel est l'impact du chiffrement EFS sur la performance système ?**
   - A) Aucune différence de performance
   - B) Une légère diminution de la vitesse de lecture/écriture
   - C) Un impact majeur sur la vitesse réseau
   - D) Une augmentation de la performance

20. **Comment EFS gère-t-il les fichiers copiés sur une clé USB non formatée en NTFS ?**
   - A) Le fichier reste chiffré
   - B) Le fichier est déchiffré automatiquement lors de la copie
   - C) Le fichier génère une erreur et ne peut pas être copié
   - D) Le fichier est compressé pour pouvoir être copié



# Annexe - question 8


Lorsqu'un fichier EFS est déplacé vers un système de fichiers non compatible avec EFS, comme FAT ou FAT32, la réponse correcte est :

B) Il est déchiffré automatiquement

Cependant, pour prévenir ce déchiffrement automatique et protéger les données sensibles, voici les mesures à mettre en place :

## Mesures préventives

1. Utiliser exclusivement des volumes NTFS pour stocker les fichiers chiffrés EFS[1][4].

2. Configurer les stratégies de groupe pour restreindre le déplacement des fichiers chiffrés vers des systèmes non compatibles.

3. Former les utilisateurs aux risques liés au déplacement de fichiers EFS[1].

4. Implémenter des contrôles d'accès stricts sur les fichiers et dossiers chiffrés[3].

5. Utiliser des méthodes de transfert sécurisées, comme des VPN, pour déplacer des fichiers sensibles si nécessaire.

6. Activer la journalisation des événements pour surveiller les tentatives de déplacement de fichiers chiffrés.

## Nouvelle réponse après implémentation des mesures

Si ces mesures de sécurité sont correctement mises en place, la nouvelle réponse à la question "Que se passe-t-il si un fichier EFS est déplacé vers un système de fichiers non compatible avec EFS ?" serait :

D) Il génère une alerte d'erreur

En effet, avec les restrictions et contrôles mis en place :

1. Le système empêchera le déplacement du fichier vers un système non compatible.
2. Une alerte sera générée pour signaler la tentative non autorisée.
3. Le fichier restera dans son emplacement d'origine, toujours chiffré et sécurisé[2].

Cette approche garantit que les fichiers EFS restent protégés et que toute tentative de compromission de leur sécurité est détectée et empêchée.

# Citations:

[1] https://next.ink/1073/encrypted-file-system-efs-explorons-possibilites-systeme-chiffrement-fichiers-sous-windows/

[2] https://canada.lenovo.com/fr/ca/en/glossary/what-is-efs/

[3] https://learn.microsoft.com/fr-fr/windows/win32/fileio/file-encryption

[4] https://learn.microsoft.com/fr-fr/security-updates/security/20200345
