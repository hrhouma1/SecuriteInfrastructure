# **Examen Pratique : Infrastructure Réseau et Sécurité (Variante pour Groupe 2)**

---

**Durée :** 3 heures  
**Points totaux :** 100 points  

---

# **1. Consignes générales**

#### **Préalable : Configuration initiale**
Avant de commencer cet examen, assurez-vous que la configuration réseau de base, introduite il y a deux semaines (01-examen-final-pratique-partie-01.md), est **opérationnelle**. Cela inclut les réseaux configurés, les serveurs et le contrôleur de domaine.  

#### **Objectif de l’examen**
Cet examen évalue votre capacité à :  
1. Configurer et sécuriser des ressources réseau.  
2. Implémenter une configuration avancée d’un cluster NLB.  
3. Mettre en œuvre des audits détaillés sur les ressources partagées.  
4. Analyser le trafic réseau pour valider les configurations.  

---

# **2. Consignes obligatoires**
1. **Documentation exigée :**  
   - Indiquez pour chaque exercice **"Fait" ou "Pas fait"**, accompagné d’une explication.  
   - En cas d’échec, décrivez précisément les raisons et proposez des hypothèses ou solutions.  
2. **Captures d’écran lisibles :** Obligatoires pour chaque étape.  
3. **Scripts ou fichiers CSV :** À inclure en annexe ou dans le rapport final.  
4. **Rédaction structurée et soignée :** Toute négligence (fautes graves, manque de clarté) entraînera une pénalité de **-10 %** sur la note globale.  
5. Chaque résultat doit être **justifié et interprété**. Une simple capture d’écran ne suffira pas à valider les points.  

---

# **3. Structure du rapport**

#### **Page de garde**
- **Titre :** Examen Pratique : Infrastructure Réseau et Sécurité (Groupe 2).  
- **Informations personnelles :**  
  - Nom et prénom.  
  - Classe.  
  - Date.  

#### **Organisation des sections**
- Présentez chaque exercice séparément avec :  
  1. **Statut :** "Fait" ou "Pas fait".  
  2. **Description des étapes réalisées.**  
  3. **Captures d’écran et commentaires.**  

---

# **Partie 1 : Sécurisation et Audit des Ressources (30 points)**

---

#### **Exercice 1.1 - Configuration d’un partage sécurisé avec restrictions avancées (10 points)**

**Objectif :**  
Configurer un dossier partagé avec des restrictions avancées d’accès.

**Instructions :**  
1. Créez un dossier `C:\Audit` sur SRV1 et SRV2.  
2. Placez un fichier `access.log` contenant : `"Log d'accès sécurisé."`.  
3. Partagez ce dossier sous le nom masqué `Audit$`, avec des permissions suivantes :  
   - Lecture seule pour `Domain Admins`.  
   - Accès interdit pour tous les autres groupes.  
4. Activez la journalisation avancée des accès dans l’onglet "Sécurité" du dossier.  

**Livrables :**  
- Captures d’écran montrant :  
  1. Création du dossier et du fichier sur SRV1 et SRV2.  
  2. Configuration avancée des permissions.  
  3. Refus d’accès pour un utilisateur standard.  

---

#### **Exercice 1.2 - Audit des connexions utilisateurs (10 points)**

**Objectif :**  
Auditer toutes les connexions utilisateur et les accès au dossier partagé `Audit$`.

**Instructions :**  
1. Créez un GPO nommé `UserConnectionAudit_Policy` sur le DC.  
2. Configurez dans `Paramètres Windows > Paramètres de sécurité > Stratégies locales > Audit des connexions` :  
   - Succès uniquement.  
3. Activez les journaux d’accès échoués au niveau des paramètres locaux de SRV1 et SRV2.  
4. Appliquez ce GPO aux deux serveurs.  

**Livrables :**  
- Captures d’écran montrant :  
  1. Paramètres configurés dans le GPO.  
  2. Résultats des logs confirmant les connexions.  
  3. Explication des résultats obtenus.  

---

#### **Exercice 1.3 - Validation des permissions et des audits (10 points)**

**Objectif :**  
Vérifier que les permissions et les audits fonctionnent correctement.

**Instructions :**  
1. Connectez-vous au CLIENT en tant qu’utilisateur "standard".  
2. Essayez d’accéder à `\\SRV1\Audit$` et `\\SRV2\Audit$`. Ces tentatives doivent échouer.  
3. Connectez-vous avec un compte administrateur et vérifiez l’accès en lecture au fichier `access.log`.  
4. Consultez les logs d’audit pour valider les événements générés.  

**Livrables :**  
- Captures d’écran montrant :  
  1. Refus d’accès pour un utilisateur standard.  
  2. Accès réussi pour un administrateur.  
  3. Logs d’audit confirmant les événements.  

---

# **Partie 2 : Configuration avancée du cluster NLB (40 points)**

---

#### **Exercice 2.1 - Installation d’IIS et de NLB avec pages dynamiques (15 points)**

**Objectif :**  
Installer IIS et NLB sur SRV1 et SRV2, avec des pages web générées dynamiquement.

**Instructions :**  
1. Installez IIS et la fonctionnalité NLB sur SRV1 et SRV2 via le Gestionnaire de serveur.  
2. Configurez une page `status.asp` avec le contenu dynamique suivant :  
   - SRV1 : `"Serveur 1 actif - Statut dynamique"`.  
   - SRV2 : `"Serveur 2 actif - Statut dynamique"`.  
3. Testez l’accès aux pages dynamiques localement sur chaque serveur (`http://localhost/status.asp`).  

**Livrables :**  
- Captures d’écran montrant :  
  1. Installation des rôles IIS et NLB.  
  2. Pages dynamiques accessibles localement sur chaque serveur.  

---

#### **Exercice 2.2 - Configuration d’un cluster NLB avec pondération (25 points)**

**Objectif :**  
Configurer un cluster NLB pour répartir le trafic de manière asymétrique entre SRV1 et SRV2.

**Instructions :**  
1. Configurez un cluster NLB sur SRV1 avec :  
   - IP virtuelle (VIP) : 192.168.2.120.  
   - Mode : Unicast.  
   - Règle : Port 80 (HTTP).  
   - Pondération : 70 % pour SRV1 et 30 % pour SRV2.  
2. Ajoutez SRV2 au cluster.  
3. Testez l’accès à la VIP depuis le CLIENT et validez que la pondération est respectée.  

**Livrables :**  
- Captures d’écran montrant :  
  1. Configuration avancée du cluster avec pondération.  
  2. Accès à la VIP confirmant la répartition asymétrique.  

---

# **Partie 3 : Analyse et validation des événements (30 points)**

---

#### **Exercice 3.1 - Script PowerShell pour analyser les logs d’accès (15 points)**

**Objectif :**  
Créer un script PowerShell pour extraire les logs liés au dossier partagé `Audit$`.

**Instructions :**  
1. Créez un script PowerShell qui :  
   - Extrait les événements associés au dossier partagé.  
   - Filtre les événements réussis et échoués par utilisateur.  
   - Exporte les résultats dans un fichier CSV.  

**Livrables :**  
- Script PowerShell fonctionnel.  
- Fichier CSV contenant les résultats filtrés.  
- Captures d’écran montrant l’exécution du script.  

---

#### **Exercice 3.2 - Capture et analyse du trafic réseau NLB (15 points)**

**Objectif :**  
Analyser le trafic réseau pour valider le fonctionnement du cluster NLB.

**Instructions :**  
1. Installez Wireshark sur le CLIENT.  
2. Capturez le trafic réseau pendant l’accès à `http://192.168.2.120`.  
3. Simulez un basculement en arrêtant SRV1 et observez que SRV2 prend le relais.  

**Livrables :**  
- Fichier de capture Wireshark annoté.  
- Captures d’écran montrant les paquets capturés.  
- Analyse détaillée confirmant le basculement.  

---

# **4. Grille d’évaluation**

| **Critères**                         | **Points** | **Détails**                                                                                                    |
|--------------------------------------|------------|----------------------------------------------------------------------------------------------------------------|
| **Partie 1 : Sécurisation et Audit** | 30         | Configuration avancée, tests fonctionnels et explications fournies.                                           |
| **Partie 2 : Configuration NLB**     | 40         | Cluster NLB configuré avec pondération et validé par tests.                                                   |
| **Partie 3 : Analyse des événements**| 30         | Script et capture réseau complets, résultats correctement interprétés.                                         |
| **Qualité rédactionnelle**           | -10% max   | Une rédaction confuse ou incomplète entraîne une pénalité sur chaque section concernée.                        |

---

**Note importante :** La réflexion, les justifications et les explications sont essentielles pour obtenir les points. Un résultat correct sans analyse peut être pénalisé. 
