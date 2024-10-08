
# Troisième partie : Transfert des journaux d’événements (30 pts)

# Étape 1 : Configurer les machines DC et CLI comme ordinateurs sources d’événements

**Objectif** : Configurer les machines **DC** (contrôleur de domaine) et **CLI** (client Windows 10) pour qu'elles envoient leurs journaux d'événements vers le serveur **SRV** (collecteur d'événements).

1. Connectez-vous à **DC** et **CLI** en tant qu'administrateur de domaine.
2. Sur **DC**, ouvrez **Event Viewer** (Observateur d'événements) via la commande `eventvwr.msc` dans la boîte de dialogue Exécuter.
3. Vérifiez que les journaux d'événements sont générés correctement en consultant les logs dans **Windows Logs** (par exemple, System, Application).
4. Répétez cette étape sur la machine **CLI** pour vous assurer que les journaux d'événements fonctionnent aussi sur cette machine.

# Étape 2 : Activer WinRM via un GPO pour DC et CLI

**Objectif** : Activer le service **WinRM (Windows Remote Management)** afin de permettre la collecte des événements à distance à partir de **DC** et **CLI**.

1. Sur **DC**, ouvrez la console **Group Policy Management** via `gpmc.msc`.
2. Créez une **nouvelle GPO** en faisant un clic droit sur l'unité d'organisation qui contient **DC** et **CLI** et sélectionnez **Create a GPO in this domain and Link it here**. Donnez-lui un nom, par exemple **Enable WinRM**.
3. Modifiez cette GPO en faisant un clic droit dessus, puis sélectionnez **Edit**.
4. Allez à **Computer Configuration > Policies > Administrative Templates > Windows Components > Windows Remote Management (WinRM) > WinRM Service**.
5. Double-cliquez sur **Allow remote server management through WinRM** et sélectionnez **Enabled**.
6. Appliquez les changements et fermez la fenêtre d'édition de la GPO.
7. Pour forcer la mise à jour des stratégies de groupe sur **DC** et **CLI**, exécutez la commande suivante dans une fenêtre PowerShell en tant qu'administrateur :
   ```bash
   gpupdate /force
   ```

# Étape 3 : Ajouter les utilisateurs au groupe Administrateurs locaux via GPO

**Objectif** : Ajouter un utilisateur administrateur (par exemple, **Administrator**) au groupe **Administrateurs locaux** sur **DC** et **CLI** pour faciliter la collecte des événements.

1. Ouvrez la console **Group Policy Management** sur **SRV**.
2. Modifiez la GPO créée précédemment (**Enable WinRM**) et accédez à **Computer Configuration > Preferences > Control Panel Settings > Local Users and Groups**.
3. Faites un clic droit, puis sélectionnez **New > Local Group**.
4. Dans la fenêtre **Action**, sélectionnez **Update**. 
5. Sous **Group name**, sélectionnez **Administrators (Built-in)**.
6. Cliquez sur **Add** sous la section **Members** et ajoutez l'utilisateur **Administrator**.
7. Appliquez et fermez la GPO.

# Étape 4 : Configurer SRV comme collecteur d’événements

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

# Étape 5 : Créer et configurer un abonnement d’événements nommé "LogLabo1"

**Objectif** : Créer un abonnement pour collecter les journaux d’événements de **DC** et **CLI**.

1. Dans la configuration de l’abonnement **LogLabo1**, vous avez déjà ajouté **DC** et **CLI** en tant qu'ordinateurs sources.
2. Spécifiez les types de journaux que vous souhaitez collecter :
   - Allez dans **Select Events** et cochez les journaux **System**, **Application**, et d'autres selon votre besoin.
3. Configurez une méthode de collecte par intervalle ou en temps réel.
4. Sauvegardez et fermez.

# Étape 6 : Activer l’audit de création des utilisateurs sur DC et créer un utilisateur nommé "Bob"

**Objectif** : Activer l’audit pour surveiller la création d'utilisateurs et créer un utilisateur nommé **Bob**.

1. Sur **DC**, ouvrez la **Group Policy Management Console** et modifiez une GPO ou en créez une nouvelle.
2. Allez à **Computer Configuration > Policies > Windows Settings > Security Settings > Advanced Audit Policy Configuration > Audit Policies > Account Management**.
3. Activez **Audit User Account Management** pour capturer les événements de création d’utilisateurs.
4. Ouvrez **Active Directory Users and Computers** et créez un utilisateur nommé **Bob** dans l’OU par défaut des utilisateurs.
5. Vérifiez que cet événement de création est bien capturé dans le journal **Security** de **DC**.

# Étape 7 : Activer l’audit de l’accès à un répertoire sur CLI

**Objectif** : Surveiller l'accès à un répertoire sur **CLI** et capturer cet événement.

1. Sur **CLI**, créez un répertoire **c:\rep1**.
2. Activez l’audit d’accès pour ce répertoire :
   - Faites un clic droit sur **c:\rep1** > **Propriétés** > **Sécurité** > **Avancé** > **Auditer**.
   - Ajoutez un **Principal** (par exemple, **Everyone**) et sélectionnez **Success** et **Failure** pour les accès au répertoire.
3. Créez un fichier texte nommé **fich.txt** dans ce répertoire.
4. Vous pouvez également créer un sous-dossier **rep2** si nécessaire.
5. Vérifiez que l’événement d'accès et de création de fichier est bien capturé dans le journal **Security** de **CLI**.

# Étape 8 : Vérifier la présence des événements sur le collecteur SRV

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

