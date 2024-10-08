
-----
# Théorie et explications reliées à la troisième partie du laboratoire, en couvrant les concepts clés associés au **transfert des journaux d’événements**, à **WinRM**, et à la **gestion des journaux dans Windows Server**.
-----



# Contexte théorique : Transfert des journaux d’événements

#### Qu’est-ce qu’un journal d’événements ?
Un journal d’événements est un registre des activités d'un système ou d'une application. Chaque fois qu’une action importante se produit (comme un démarrage, une connexion utilisateur, ou une erreur système), une entrée est ajoutée au journal d'événements. Les journaux d’événements permettent aux administrateurs de surveiller, diagnostiquer et corriger les problèmes dans un environnement réseau.

Dans Windows, les journaux d'événements sont principalement répartis en plusieurs catégories :
- **Application** : Enregistre les événements liés aux applications installées sur le système.
- **Système** : Enregistre les événements du système d'exploitation.
- **Sécurité** : Suit les événements liés à la sécurité, comme les connexions utilisateurs et les modifications de permissions.

#### Pourquoi centraliser les journaux d'événements ?
Dans un environnement où plusieurs machines sont présentes (comme un serveur, un contrôleur de domaine, des clients), il est souvent nécessaire de centraliser les journaux d’événements pour :
1. **Simplifier la surveillance** : Au lieu de se connecter individuellement à chaque machine pour consulter les journaux, les administrateurs peuvent surveiller tous les journaux à partir d'une seule machine, ici **SRV**.
2. **Faciliter le diagnostic des problèmes** : Les journaux centralisés permettent de corréler les événements à travers plusieurs machines. Cela aide à comprendre l'origine des problèmes dans des environnements complexes.
3. **Améliorer la sécurité** : Centraliser les journaux permet de détecter plus rapidement les attaques ou les anomalies en suivant les actions sur l’ensemble du réseau.

#### Qu’est-ce que WinRM ?
**WinRM (Windows Remote Management)** est un service de Windows qui permet de gérer les machines à distance. Il est utilisé ici pour permettre à une machine (SRV) de collecter les journaux d’événements d’autres machines (DC et CLI) sans avoir besoin de se connecter directement à ces machines.

**Pourquoi utiliser WinRM pour la collecte des événements ?**
- **Automatisation** : Grâce à WinRM, il est possible de configurer des collecteurs d’événements qui reçoivent automatiquement des informations depuis plusieurs machines sans interaction manuelle.
- **Sécurité** : WinRM utilise le chiffrement pour s'assurer que les informations transmises à distance sont protégées contre toute interception.

#### Qu’est-ce qu’un abonnement d'événement ?
Un **abonnement d’événement** permet à une machine (ici **SRV**) de recueillir automatiquement des événements d'autres ordinateurs (comme **DC** et **CLI**) et de les stocker dans un journal spécifique, comme **Forwarded Events**.

Voici comment cela fonctionne :
1. **Source d'événements** : Ce sont les machines qui envoient leurs journaux d'événements (ici **DC** et **CLI**).
2. **Collecteur** : C'est la machine qui reçoit et stocke les événements envoyés par les sources (ici **SRV**).
3. **Abonnement** : C’est la configuration qui indique quels types d'événements doivent être envoyés par les sources et comment ils doivent être collectés.

#### Pourquoi activer l’audit des actions ?
L’**audit** est un mécanisme qui permet d’enregistrer des événements spécifiques (comme la création d’un utilisateur ou l'accès à un fichier) dans les journaux d'événements.

1. **Audit de la création d’utilisateurs** :
   - Lorsque l’audit est activé pour les actions de gestion des comptes (par exemple, la création ou la suppression d’utilisateurs), chaque événement lié à la gestion des utilisateurs est enregistré dans le journal de sécurité. Cela permet aux administrateurs de suivre qui crée des comptes, et quand.
   - Dans ce laboratoire, l’audit de la création de l’utilisateur **Bob** sur **DC** permet de capturer cet événement et de le transmettre à **SRV**.

2. **Audit de l'accès aux fichiers** :
   - L’audit des accès à un fichier ou un dossier enregistre chaque fois qu’un utilisateur accède à une ressource surveillée, comme un dossier ou un fichier.
   - Ici, en activant l'audit pour le répertoire **c:\rep1** sur **CLI**, chaque fois qu'un utilisateur accède à ce répertoire ou y crée un fichier (comme **fich.txt**), cet événement est enregistré.

# Étapes concrètes en action

1. **Configurer les machines sources** : 
   - **DC** et **CLI** sont configurées pour être des sources d'événements, c'est-à-dire qu'elles vont envoyer leurs journaux vers **SRV**.
   
2. **Activer WinRM** :
   - Cela permet aux journaux d’être envoyés de manière sécurisée et automatique vers le collecteur **SRV**, sans qu'il soit nécessaire d'interagir avec les machines sources.

3. **Configurer SRV comme collecteur d’événements** :
   - **SRV** est configuré pour recevoir et stocker les événements provenant de **DC** et **CLI**. L’abonnement **LogLabo1** est une configuration qui détermine quels événements doivent être collectés.

4. **Activer l’audit de la création d’utilisateur** :
   - Sur **DC**, l’audit permet d’enregistrer des événements liés à la gestion des utilisateurs. La création de l'utilisateur **Bob** est un exemple concret de cet audit.

5. **Activer l’audit des accès à un répertoire** :
   - Sur **CLI**, vous activez l’audit pour surveiller l'accès à un répertoire particulier (**c:\rep1**) et enregistrez les actions comme la création de fichiers dans ce dossier.

6. **Vérification des événements collectés** :
   - Enfin, vous vérifiez que les événements d'accès au répertoire et de création d'utilisateur sont bien transférés à **SRV** et apparaissent dans le journal **Forwarded Events**.

# Pourquoi cette approche est importante ?
Cette approche vous permet de :
- **Centraliser la surveillance** : Les administrateurs peuvent surveiller les événements de plusieurs machines à partir d'un seul endroit (le collecteur SRV), ce qui simplifie énormément la gestion.
- **Renforcer la sécurité** : La capture d'événements clés comme la création d'utilisateurs ou l'accès à des fichiers sensibles permet de détecter rapidement des actions non autorisées ou des violations de sécurité.
- **Automatiser la gestion** : Avec WinRM et les abonnements d'événements, les journaux d’événements sont collectés et analysés automatiquement, sans intervention humaine continue.

# Conclusion

- Vous devez comprendre que ce laboratoire met en œuvre des concepts essentiels d’administration réseau et de sécurité. 
- En activant des audits sur des événements clés, en centralisant les journaux et en automatisant leur collecte à l’aide de WinRM, vous construisez un système robuste de gestion des événements qui peut être utilisé dans des environnements professionnels pour surveiller l’activité du réseau et détecter des incidents de sécurité.
