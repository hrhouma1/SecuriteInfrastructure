
### Troisième partie : Transfert des journaux d’événements (30 pts)

#### Étape 1 : Configurer les machines DC et CLI comme ordinateurs sources d’événements

**Objectif** : Configurer les machines **DC** (contrôleur de domaine) et **CLI** (client Windows 10) pour qu'elles envoient leurs journaux d'événements vers le serveur **SRV** (collecteur d'événements).

1. Connectez-vous à **DC** et **CLI** en tant qu'administrateur de domaine.
2. Sur **DC**, ouvrez **Event Viewer** (Observateur d'événements) via la commande `eventvwr.msc` dans la boîte de dialogue Exécuter.
3. Vérifiez que les journaux d'événements sont générés correctement en consultant les logs dans **Windows Logs** (par exemple, System, Application).
4. Répétez cette étape sur la machine **CLI** pour vous assurer que les journaux d'événements fonctionnent aussi sur cette machine.

#### Étape 2 : Activer WinRM via un GPO pour DC et CLI

**Objectif** : Activer le service **WinRM (Windows Remote Management)** afin de permettre la collecte des événements à distance à partir de **DC** et **CLI**.

1. Sur **SRV**, ouvrez la console **Group Policy Management** via `gpmc.msc`.
2. Créez une **nouvelle GPO** en faisant un clic droit sur l'unité d'organisation qui contient **DC** et **CLI** et sélectionnez **Create a GPO in this domain and Link it here**. Donnez-lui un nom, par exemple **Enable WinRM**.
3. Modifiez cette GPO en faisant un clic droit dessus, puis sélectionnez **Edit**.
4. Allez à **Computer Configuration > Policies > Administrative Templates > Windows Components > Windows Remote Management (WinRM) > WinRM Service**.
5. Double-cliquez sur **Allow remote server management through WinRM** et sélectionnez **Enabled**.
6. Appliquez les changements et fermez la fenêtre d'édition de la GPO.
7. Pour forcer la mise à jour des stratégies de groupe sur **DC** et **CLI**, exécutez la commande suivante dans une fenêtre PowerShell en tant qu'administrateur :
   ```bash
   gpupdate /force
   ```

#### Étape 3 : Ajouter les utilisateurs au groupe Administrateurs locaux via GPO

**Objectif** : Ajouter un utilisateur administrateur (par exemple, **Administrator**) au groupe **Administrateurs locaux** sur **DC** et **CLI** pour faciliter la collecte des événements.

1. Ouvrez la console **Group Policy Management** sur **SRV**.
2. Modifiez la GPO créée précédemment (**Enable WinRM**) et accédez à **Computer Configuration > Preferences > Control Panel Settings > Local Users and Groups**.
3. Faites un clic droit, puis sélectionnez **New > Local Group**.
4. Dans la fenêtre **Action**, sélectionnez **Update**. 
5. Sous **Group name**, sélectionnez **Administrators (Built-in)**.
6. Cliquez sur **Add** sous la section **Members** et ajoutez l'utilisateur **Administrator**.
7. Appliquez et fermez la GPO.

#### Étape 4 : Configurer SRV comme collecteur d’événements

**Objectif** : Configurer **SRV** pour qu’il agisse comme un collecteur d'événements pour les journaux envoyés par **DC** et **CLI**.

1. Sur **SRV**, ouvrez **Event Viewer**.
2. Dans le volet gauche, faites un clic droit sur **Subscriptions** (Abonnements), puis sélectionnez **Create Subscription**.
3. Dans la fenêtre **Subscription Properties**, configurez les options suivantes :
   - **Name** : Donnez un nom à l'abonnement, par exemple **LogLabo1**.
   - **Description** : Optionnelle, mais vous pouvez ajouter une note pour décrire cet abonnement.
   - **Destination Log** : Sélectionnez **Forwarded Events**.
   - **Event Source Computers** : Cliquez sur **Add Computers** et sélectionnez **DC** et **CLI**.
4. Dans l’onglet **Collector initiated** (Collecteur initié), spécifiez les ordinateurs sources.
5. Appliquez et fermez.

#### Étape 5 : Créer et configurer un abonnement d’événements nommé "LogLabo1"

**Objectif** : Créer un abonnement pour collecter les journaux d’événements de **DC** et **CLI**.

1. Dans la configuration de l’abonnement **LogLabo1**, vous avez déjà ajouté **DC** et **CLI** en tant qu'ordinateurs sources.
2. Spécifiez les types de journaux que vous souhaitez collecter :
   - Allez dans **Select Events** et cochez les journaux **System**, **Application**, et d'autres selon votre besoin.
3. Configurez une méthode de collecte par intervalle ou en temps réel.
4. Sauvegardez et fermez.

#### Étape 6 : Activer l’audit de création des utilisateurs sur DC et créer un utilisateur nommé "Bob"

**Objectif** : Activer l’audit pour surveiller la création d'utilisateurs et créer un utilisateur nommé **Bob**.

1. Sur **DC**, ouvrez la **Group Policy Management Console** et modifiez une GPO ou en créez une nouvelle.
2. Allez à **Computer Configuration > Policies > Windows Settings > Security Settings > Advanced Audit Policy Configuration > Audit Policies > Account Management**.
3. Activez **Audit User Account Management** pour capturer les événements de création d’utilisateurs.
4. Ouvrez **Active Directory Users and Computers** et créez un utilisateur nommé **Bob** dans l’OU par défaut des utilisateurs.
5. Vérifiez que cet événement de création est bien capturé dans le journal **Security** de **DC**.

#### Étape 7 : Activer l’audit de l’accès à un répertoire sur CLI

**Objectif** : Surveiller l'accès à un répertoire sur **CLI** et capturer cet événement.

1. Sur **CLI**, créez un répertoire **c:\rep1**.
2. Activez l’audit d’accès pour ce répertoire :
   - Faites un clic droit sur **c:\rep1** > **Propriétés** > **Sécurité** > **Avancé** > **Auditer**.
   - Ajoutez un **Principal** (par exemple, **Everyone**) et sélectionnez **Success** et **Failure** pour les accès au répertoire.
3. Créez un fichier texte nommé **fich.txt** dans ce répertoire.
4. Vous pouvez également créer un sous-dossier **rep2** si nécessaire.
5. Vérifiez que l’événement d'accès et de création de fichier est bien capturé dans le journal **Security** de **CLI**.

#### Étape 8 : Vérifier la présence des événements sur le collecteur SRV

**Objectif** : Vérifier que les événements capturés sur **DC** et **CLI** sont correctement transférés et apparaissent sur **SRV**.

1. Sur **SRV**, ouvrez **Event Viewer**.
2. Allez dans le journal **Forwarded Events**.
3. Vérifiez que les événements suivants sont présents :
   - La création de l'utilisateur **Bob** sur **DC**.
   - L'accès au répertoire **c:\rep1** et la création du fichier **fich.txt** sur **CLI**.
4. Si ces événements sont bien présents, cela confirme que le transfert des journaux d'événements fonctionne correctement.

### Conclusion

### ==> Nous couvrons chaque étape du processus de configuration pour transférer les journaux d'événements des machines **DC** et **CLI** vers **SRV**. 
### ==>  l’importance de la gestion centralisée des journaux pour surveiller l’activité réseau et détecter les incidents de sécurité.



-----
# Annexe : Théorie
-----



Voici une partie théorique exhaustive pour expliquer à vos étudiants ce qu’ils sont en train de faire dans la troisième partie du laboratoire, en couvrant les concepts clés associés au **transfert des journaux d’événements**, à **WinRM**, et à la **gestion des journaux dans Windows Server**.

### Contexte théorique : Transfert des journaux d’événements

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

#### Étapes concrètes en action

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

### Pourquoi cette approche est importante ?
Cette approche vous permet de :
- **Centraliser la surveillance** : Les administrateurs peuvent surveiller les événements de plusieurs machines à partir d'un seul endroit (le collecteur SRV), ce qui simplifie énormément la gestion.
- **Renforcer la sécurité** : La capture d'événements clés comme la création d'utilisateurs ou l'accès à des fichiers sensibles permet de détecter rapidement des actions non autorisées ou des violations de sécurité.
- **Automatiser la gestion** : Avec WinRM et les abonnements d'événements, les journaux d’événements sont collectés et analysés automatiquement, sans intervention humaine continue.

### Conclusion

- Vous devez comprendre que ce laboratoire met en œuvre des concepts essentiels d’administration réseau et de sécurité. 
- En activant des audits sur des événements clés, en centralisant les journaux et en automatisant leur collecte à l’aide de WinRM, vous construisez un système robuste de gestion des événements qui peut être utilisé dans des environnements professionnels pour surveiller l’activité du réseau et détecter des incidents de sécurité.
