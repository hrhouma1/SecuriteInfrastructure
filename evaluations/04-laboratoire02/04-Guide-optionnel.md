# Guide détaillé pour la configuration d'un troisième nœud dans le cluster NLB (sans utilisation de Windows Server Core)

---

#### Contexte
L'objectif de ce guide est d'ajouter un troisième nœud à un cluster NLB existant nommé `ClusterWeb.test.local`. Pour cela, nous utiliserons une machine Windows Server 2016/2019 avec interface graphique pour faciliter la gestion, en particulier pour les étudiants débutants. Les trois serveurs participant au cluster doivent avoir une configuration réseau et applicative identique, incluant le serveur Web IIS.

---

# A. Installation et Configuration (20 points)

**1. Préparation de la machine Windows Server 2016/2019**

   - Installez une machine Windows Server 2016/2019 avec les caractéristiques suivantes :
     - **Nom d’hôte** : WinND3 (à remplacer par WinCoreND3 si les instructions évoluent vers un usage de Server Core).
     - **Configuration réseau** :
       - **Première interface réseau (Gestion)** :  
         - **IP** : `192.168.0.3`
         - **Masque de sous-réseau** : `255.255.255.0` (/24)
       - **Deuxième interface réseau (Trafic NLB)** :  
         - **IP** : `192.168.0.33`
         - **Masque de sous-réseau** : `255.255.255.0` (/24)

**Étapes pour configurer les interfaces réseau :**

   - Accédez à **Panneau de configuration > Réseau et Internet > Connexions réseau**.
   - Cliquez droit sur chaque interface pour configurer les adresses IP selon les spécifications ci-dessus.

**2. Joindre WinND3 au domaine `test.local` :**

   - **Via l'interface graphique :**
     - Accédez à **Système** et cliquez sur **Modifier les paramètres** pour rejoindre le domaine.
     - Renseignez le nom du domaine : `test.local`.
     - Entrez les informations d’authentification du domaine pour effectuer l’opération.

**3. Installation du service IIS :**

   - Accédez au **Gestionnaire de serveur** et sélectionnez **Ajouter des rôles et fonctionnalités**.
   - Cochez **IIS (Internet Information Services)** et procédez à l’installation.
   - Modifiez la page d’accueil d’IIS pour afficher votre nom et prénom.
     - Accédez au répertoire `C:\inetpub\wwwroot\` et ouvrez le fichier `iisstart.htm` avec un éditeur de texte.
     - Ajoutez votre nom et prénom dans le contenu HTML de la page.

# B. Installation de la fonctionnalité de répartition de la charge réseau (20 points)

**4. Installer les fonctionnalités NLB et RSAT-NLB avec PowerShell sur WinND3 :**

   - Ouvrez PowerShell en tant qu’administrateur.
   - Exécutez les commandes suivantes :
     ```powershell
     Install-WindowsFeature -Name NLB
     Install-WindowsFeature -Name RSAT-NLB
     ```

**5. Vérification de l’installation des fonctionnalités :**

   - Depuis le **Contrôleur de domaine**, ouvrez le **Gestionnaire de serveur** et vérifiez que WinND3 a bien les fonctionnalités NLB et RSAT-NLB installées.

# C. Gestion du cluster NLB (10 points)

**6. Ajouter WinND3 au cluster NLB existant (`ClusterWeb.test.local`) :**

   - Depuis PowerShell, exécutez les commandes suivantes sur WinND3 :
     ```powershell
     Import-Module NetworkLoadBalancingClusters
     Add-NlbClusterNode -HostName WinND3 -InterfaceName "NomInterfaceNLB"
     ```
   - Remplacez `"NomInterfaceNLB"` par le nom exact de l’interface réseau dédiée au trafic NLB.

# D. Test du cluster NLB (40 points)

**7. Configurer les enregistrements DNS pour `ClusterWeb.test.local` :**

   - Sur le **serveur DNS** du domaine `test.local` :
     - Ajoutez un enregistrement de type **A** pour `ClusterWeb.test.local` avec l’adresse IP virtuelle du cluster.
     - Ajoutez un enregistrement **CNAME** pour `www` pointant vers `ClusterWeb.test.local`.

**8. Test du cluster depuis une machine cliente :**

   - Depuis un **navigateur web** sur la machine cliente (par exemple WinCLI), entrez l’URL `http://www.test.local`.
   - Vérifiez que la page d’accueil IIS s’affiche correctement, confirmant l’accès au cluster NLB.

**9. Vérification de la résilience du cluster NLB :**

   - Éteignez les deux premiers nœuds du cluster (WinND1 et WinND2).
   - Réessayez d’accéder à `http://www.test.local` depuis le navigateur. Le cluster doit rediriger la requête vers WinND3, confirmant que la répartition de charge fonctionne correctement.

**10. Automatisation des tests avec PowerShell :**

   - Créez un script PowerShell pour tester la disponibilité du cluster :
     ```powershell
     $nodes = @("192.168.0.1", "192.168.0.2", "192.168.0.3")
     foreach ($node in $nodes) {
         if (Test-Connection -ComputerName $node -Count 1 -Quiet) {
             Write-Output "$node est en ligne."
         } else {
             Write-Output "$node est hors ligne."
         }
     }
     ```

---

# Récapitulatif de la configuration

| Étape                         | Description                                         | Points |
|-------------------------------|-----------------------------------------------------|--------|
| **A. Installation et configuration**       | Configuration de la machine et ajout au domaine | 20     |
| **B. Fonctionnalité NLB**                 | Installation des services NLB et vérification   | 20     |
| **C. Gestion du cluster NLB**             | Ajout de la machine au cluster existant         | 10     |
| **D. Tests du cluster NLB**               | Configuration DNS et tests de basculement       | 40     |

---

# Remarques importantes pour la livraison de votre travail

1. **Clarté et structuration** : Séparez chaque section et étape avec des titres clairs.
2. **Détails et exhaustivité** : Incluez toutes les étapes, sans abréviations. Assurez-vous que chaque instruction est formulée de manière complète pour faciliter la reproduction du travail.
3. **Évitez les captures d’écran isolées** : Les captures d’écran ne suffisent pas. Il est essentiel de fournir des explications pour chaque action effectuée.
4. **Table de figures et annexes** : Encouragez l’usage d’une table de figures et des titres distincts pour les sections pour organiser le rapport. Une table des matières peut également être ajoutée pour la navigation.
5. **Rédaction professionnelle** : Utilisez des phrases claires et soignées, en veillant à éviter les erreurs d’orthographe et de grammaire.

