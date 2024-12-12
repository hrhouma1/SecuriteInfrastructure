# **Examen Pratique : Infrastructure Réseau et Sécurité**

---

**Durée :** 3 heures  
**Points totaux :** 100 points  

---



# Consignes générales

### Préalable : Configuration initiale

Avant de commencer cet examen, assurez-vous que la configuration réseau partagée il y a deux semaines (01-examen-final-pratique-partie-01.md) est **opérationnelle**. 


#### **Objectif de l’examen**
Vous serez évalué.e.s sur votre capacité à :  
1. Configurer un environnement réseau sécurisé.  
2. Implémenter et tester un cluster NLB (Network Load Balancing).  
3. Configurer des audits de sécurité et analyser les événements.  
4. Utiliser des outils d’analyse réseau pour capturer et interpréter le trafic.  

# **Consignes obligatoires**
1. Chaque exercice doit être **accompagné d’une justification**. Indiquez :  
   - **Statut :** "Fait" ou "Pas fait".  
   - **Commentaires** sur les résultats et les éventuelles erreurs.  
2. Fournissez **des captures d’écran claires et lisibles** pour chaque tâche demandée.  
3. Les scripts ou fichiers CSV doivent être inclus dans le rapport final en annexe.  
4. Une **rédaction structurée et soignée est obligatoire**. Des fautes graves ou un manque de clarté entraîneront une pénalité de **-10%**.
5. Chaque exercice doit être **accompagné d’une justification précise et rigoureuse**, comprenant :  
- **Statut :** Mentionnez clairement "Fait" si la tâche est accomplie, ou "Pas fait" si elle ne l’est pas.  
- En cas d’échec, **détaillez votre raisonnement** en expliquant pourquoi, selon vous, cela ne fonctionne pas. 
- *Attention : Votre réflexion et vos hypothèses doivent être argumentées.*


---

# **2. Structure du rapport**

#### **Page de garde**
- **Titre :** Examen Pratique : Infrastructure Réseau et Sécurité.  
- **Informations personnelles :**  
  - Nom et prénom.  
  - Classe.  
  - Date.  

---

# **Partie 1 : Configuration et Audit de Sécurité (30 points)**

---

## **Exercice 1.1 - Préparation de l’Environnement (10 points)**

**Objectif :**  
Configurer un dossier sécurisé accessible uniquement en lecture par les membres du groupe `Domain Users`.

**Instructions détaillées :**  
1. Créez un dossier `C:\Sensitive` sur SRV1 et SRV2.  
2. Placez un fichier `test.txt` contenant la phrase `"Données confidentielles"`.  
3. Partagez ce dossier sous le nom masqué `Sensitive$` avec des permissions en lecture seule pour `Domain Users`.  
4. Vérifiez que les permissions sont correctement configurées dans l’onglet "Sécurité".  

**Livrables attendus :**  
- Captures d’écran :  
  1. Création du dossier et du fichier sur SRV1 et SRV2.  
  2. Configuration des permissions de partage.  
  3. Accès réussi via `\\SRV1\Sensitive$` et `\\SRV2\Sensitive$`.
  4. Commentaires expliquant pourquoi les résultats confirment ou non la configuration correcte.  
---

## **Exercice 1.2 - Configuration de l’Audit (10 points)**

**Objectif :**  
Configurer un GPO pour auditer les accès aux objets et connexions utilisateur.

**Instructions détaillées :**  
1. Créez un GPO nommé `SecurityAudit_Policy` sur le DC.  
2. Configurez dans `Paramètres Windows > Paramètres de sécurité > Stratégies locales > Audit des objets` :  
   - **Audit des accès aux objets :** Succès et échecs.  
   - **Audit des connexions :** Succès et échecs.  
3. Définissez la taille maximale du journal de sécurité à 1 GB dans les paramètres d’audit locaux.  
4. Appliquez ce GPO aux serveurs SRV1 et SRV2.  

**Livrables attendus :**  
- Captures d’écran :  
  1. Paramètres configurés dans le GPO.  
  2. Résultat de l’application du GPO (`gpresult /h`).
  3. Commentaires expliquant pourquoi les résultats confirment ou non la configuration correcte.  

---

## **Exercice 1.3 - Tests et Validation (10 points)**

**Objectif :**  
Vérifier que les permissions et les audits fonctionnent correctement.

**Instructions détaillées :**  
1. Connectez-vous au CLIENT avec un compte utilisateur standard appartenant au groupe `Domain Users`.  
2. Accédez aux partages `\\SRV1\Sensitive$` et `\\SRV2\Sensitive$`.  
3. Essayez de modifier le fichier `test.txt`. Cette opération doit être refusée.  

**Livrables attendus :**  
- Captures d’écran :  
  1. Accès en lecture au fichier.  
  2. Refus de modification du fichier.
  3. Commentaires expliquant pourquoi les résultats confirment ou non la configuration correcte.  

---

### **Partie 2 : Configuration NLB (40 points)**

---

#### **Exercice 2.1 - Installation des Services (15 points)**

**Objectif :**  
Installer IIS et la fonctionnalité NLB sur SRV1 et SRV2.

**Instructions détaillées :**  
1. Sur SRV1 et SRV2, ajoutez les rôles et fonctionnalités nécessaires via le Gestionnaire de serveur :  
   - IIS (services Web par défaut).  
   - Fonctionnalité NLB.  
2. Créez un fichier `index.html` personnalisé dans le répertoire IIS sur chaque serveur :  
   - SRV1 : "Bienvenue sur Serveur 1".  
   - SRV2 : "Bienvenue sur Serveur 2".  
3. Vérifiez que les pages web sont accessibles localement.  

**Livrables attendus :**  
- Captures d’écran :  
  1. Pages web accessibles localement sur SRV1 et SRV2.  
  2. Fonctionnalité NLB installée.
  3. Commentaires expliquant pourquoi les résultats confirment ou non la configuration correcte.   

---

#### **Exercice 2.2 - Configuration du Cluster NLB (25 points)**

**Objectif :**  
Configurer un cluster NLB pour équilibrer le trafic entre SRV1 et SRV2.

**Instructions détaillées :**  
1. Configurez un cluster NLB sur SRV1 avec :  
   - IP virtuelle (VIP) : 192.168.2.100.  
   - Mode : Multicast.  
   - Distribution : Égale.  
   - Règle pour le port 80 (HTTP).  
2. Joignez SRV2 au cluster existant.  
3. Testez la convergence du cluster.  
4. Depuis le CLIENT, accédez à `http://192.168.2.100` et vérifiez que les requêtes sont réparties entre SRV1 et SRV2.  

**Livrables attendus :**  
- Captures d’écran :  
  1. Configuration du cluster sur SRV1.  
  2. Ajout de SRV2 au cluster.  
  3. Accès réussi à la VIP confirmant la répartition de charge.
  4. Commentaires expliquant pourquoi les résultats confirment ou non la configuration correcte.    

---

### **Partie 3 : Surveillance et Analyse (30 points)**

---

#### **Exercice 3.1 - Surveillance des Événements (15 points)**

**Objectif :**  
Créer un script PowerShell pour extraire et analyser les événements liés aux partages.

**Instructions détaillées :**  
1. Créez un script PowerShell qui :  
   - Extrait les événements liés aux accès au dossier `Sensitive`.  
   - Filtre les tentatives réussies et échouées.  
   - Exporte les résultats dans un fichier CSV.  
2. Testez le script sur le DC.  

**Livrables attendus :**  
- Script PowerShell complet (inclus dans le rapport ou joint).  
- Export CSV des événements pertinents.  
- Commentaires expliquant les résultats.  

---

#### **Exercice 3.2 - Analyse du Trafic (15 points)**

**Objectif :**  
Analyser le trafic réseau pour vérifier le fonctionnement du cluster NLB.

**Instructions détaillées :**  
1. Installez Wireshark sur le CLIENT.  
2. Capturez le trafic réseau pendant les scénarios suivants :  
   - Accès à `http://192.168.2.100`.  
   - Test de basculement (arrêt de SRV1).  
3. Filtrez et identifiez les paquets NLB.  

**Livrables attendus :**  
- Fichier de capture Wireshark.  
- Capture d’écran des paquets NLB identifiés.  
- Commentaires expliquant les résultats du basculement.  

---

### **3. Grille d’évaluation**

| **Critères**                         | **Points** | **Détails**                                                                                                    |
|--------------------------------------|------------|----------------------------------------------------------------------------------------------------------------|
| **Partie 1 : Configuration et Audit de Sécurité** | 30         | Configuration correcte, livrables complets, commentaires pertinents.                                           |
| Préparation de l’environnement       | 10         | Permissions et partages correctement configurés.                                                              |
| Configuration de l’audit             | 10         | Paramètres d’audit appliqués avec succès, GPO fonctionnel.                                                    |
| Tests et validation                  | 10         | Tests réussis et résultats correctement interprétés.                                                          |
| **Partie 2 : Configuration NLB**     | 40         | Configuration NLB complète, testée et fonctionnelle.                                                          |
| Installation des services            | 15         | IIS et NLB correctement installés, pages web accessibles.                                                     |
| Configuration du cluster             | 25         | Cluster fonctionnel avec répartition et basculement vérifiés.                                                 |
| **Partie 3 : Surveillance et Analyse** | 30         | Surveillance et analyse réseau réalisées avec précision.                                                      |
| Surveillance des événements          | 15         | Script PowerShell fonctionnel, export CSV correct.                                                            |
| Analyse du trafic                    | 15         | Capture et analyse des paquets NLB, résultats interprétés correctement.                                       |
| **Clarté et qualité rédactionnelle** | -10% max   | Structure désorganisée, absence d’interprétations ou rédaction confuse entraînent une pénalité.                |

---

**Rappel :**  
Pour chaque étape, fournir des explications claires et détaillées est obligatoire. Les commentaires ne sont pas un bonus, mais une condition essentielle pour obtenir les points complets. Par exemple, un cluster NLB fonctionnel ne sera pas évalué à 25/25 si les commentaires et explications de la configuration et des tests sont absents. Les captures d’écran, les justifications et une analyse rigoureuse sont indispensables pour valider votre travail, avoir vos points et maximiser votre note.
