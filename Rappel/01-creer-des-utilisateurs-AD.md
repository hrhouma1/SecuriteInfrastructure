# Gérer les utilisateurs, groupes, et OUs (Organizational Units) dans Active Directory

Pour créer des utilisateurs et des groupes dans Active Directory (AD) Users and Computers, voici les étapes à suivre sous Windows Server 2019 ou une autre version similaire de Windows Server qui utilise Active Directory :

### 1. **Ouvrir l'outil "Active Directory Users and Computers" :**
   - Connectez-vous à votre serveur Windows.
   - Ouvrez l'outil **"Active Directory Users and Computers"** en le cherchant dans le menu Démarrer ou en exécutant la commande `dsa.msc`.

### 2. **Créer une Organisation Unit (OU) :**
   - Dans la fenêtre principale, cliquez avec le bouton droit sur l'élément "Domain" ou sur une autre unité organisationnelle où vous voulez ajouter un utilisateur.
   - Sélectionnez **"New"** puis **"Organizational Unit" (OU)**.
   - Donnez un nom à cette unité (par exemple, "Helpdesk" ou "SysAdmins") comme montré dans l'image.

### 3. **Créer un utilisateur :**
   - Sélectionnez l'OU que vous avez créée ou une autre où vous voulez ajouter un utilisateur.
   - Cliquez avec le bouton droit sur cette OU, puis sélectionnez **"New"** et ensuite **"User"**.
   - Remplissez les champs comme le nom, le prénom, le nom d’utilisateur (logon), puis cliquez sur **"Next"**.
   - Définissez un mot de passe et configurez les options comme "User must change password at next logon" ou d'autres selon vos besoins.
   - Cliquez sur **"Finish"** pour finaliser la création.

### 4. **Créer un groupe :**
   - Comme pour la création d'un utilisateur, sélectionnez l'OU où vous souhaitez ajouter un groupe.
   - Faites un clic droit, sélectionnez **"New"**, puis **"Group"**.
   - Donnez un nom au groupe, choisissez le **type de groupe** (Security ou Distribution) et le **scope** (Global, Domain Local, ou Universal).
   - Cliquez sur **"OK"**.

### 5. **Ajouter des utilisateurs à des groupes :**
   - Sélectionnez un utilisateur dans l'OU où il a été créé.
   - Faites un clic droit sur cet utilisateur et sélectionnez **"Add to a group"**.
   - Tapez le nom du groupe dans lequel vous souhaitez ajouter l'utilisateur (par exemple, "Helpdesk" ou "SysAdmins") et cliquez sur **"OK"**.

### 6. **Configurer les descriptions et attributs :**
   - Vous pouvez cliquer avec le bouton droit sur chaque utilisateur ou groupe pour modifier des paramètres supplémentaires, comme la **description** (visible dans la colonne "Description") ou d'autres attributs spécifiques dans l'onglet **"Attributes"**.

Cela vous permet de gérer les utilisateurs, groupes, et OUs (Organizational Units) dans Active Directory. Pour des utilisateurs plus avancés, vous pouvez aussi automatiser cela avec PowerShell.
