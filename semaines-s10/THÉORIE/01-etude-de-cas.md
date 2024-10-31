### **Étude de Cas : Billy et le Chiffrement de Disque avec BitLocker**

Billy est un étudiant du Collège de Maisonneuve, dont l’adresse réseau est **cmaisonneuve.qc.ca**. Son ordinateur est inscrit dans le domaine Active Directory (AD) de l’école, ce qui permet une gestion centralisée des paramètres et de la sécurité de son appareil. Par mesure de sécurité, il a chiffré l’intégralité de son disque dur à l’aide de BitLocker.

Voici quelques informations supplémentaires sur sa configuration :
1. **Chiffrement BitLocker complet** : Le disque de Billy est intégralement chiffré.
2. **Clé de récupération** : Billy a sauvegardé sa clé de récupération BitLocker de deux manières : une copie dans le **cloud** (Azure AD) et une autre sur son **disque dur**.
3. **Inscription dans le domaine** : Son ordinateur fait partie du domaine **cmaisonneuve.qc.ca**, ce qui permet aux administrateurs du domaine de gérer certains aspects de la sécurité de l’ordinateur.

### **Questions pour les étudiants et réponses détaillées**

#### **Question 1 : Si un autre étudiant utilise l'ordinateur de Billy, pourra-t-il accéder aux données chiffrées ? Pourquoi ou pourquoi pas ?**

**Réponse** :
Non, un autre étudiant ne pourra pas accéder aux données chiffrées de l'ordinateur de Billy. Voici pourquoi :
- BitLocker exige une **authentification** pour accéder aux données chiffrées sur le disque.
- Puisque l’ordinateur est protégé par BitLocker, il nécessitera la **clé de déchiffrement** ou l’accès via un **compte utilisateur autorisé** pour démarrer et accéder aux données.
- Seul Billy ou un administrateur ayant la **clé de récupération** pourrait déverrouiller le disque et accéder aux informations chiffrées.
- Par ailleurs, en étant enregistré dans le domaine, les administrateurs peuvent appliquer des politiques de sécurité pour renforcer la protection contre l'accès non autorisé.

#### **Question 2 : Comment les administrateurs du domaine "cmaisonneuve.qc.ca" peuvent-ils empêcher tout accès non autorisé aux données de Billy, même en cas de vol de l’appareil ?**

**Réponse** :
Les administrateurs du domaine peuvent appliquer plusieurs mesures de sécurité grâce à leur contrôle sur les appareils inscrits dans le domaine **cmaisonneuve.qc.ca** :
- **Gestion des clés de récupération** : Les administrateurs peuvent forcer l’enregistrement des clés de récupération BitLocker dans Active Directory du domaine, leur permettant d'accéder au disque uniquement en cas de besoin, tout en empêchant les utilisateurs non autorisés.
- **Politiques de groupe (GPO)** : Ils peuvent définir des stratégies via les **Group Policy Objects (GPO)** pour restreindre l’accès et exiger une authentification forte pour déverrouiller BitLocker (par exemple, via TPM, un mot de passe ou une clé USB).
- **Désactivation à distance** : En cas de vol de l’appareil, les administrateurs peuvent révoquer l’accès de l’appareil au domaine, limitant ainsi l’accès aux services de domaine.
- **Verrouillage des comptes utilisateurs** : Les administrateurs peuvent verrouiller le compte utilisateur de Billy si l’appareil est perdu, empêchant l’accès aux données sans la clé de récupération.

#### **Question 3 : Si un administrateur a la clé de récupération de Billy dans l'Active Directory de l'école, pourrait-il déverrouiller son disque sans son accord ? Pourquoi ?**

**Réponse** :
Oui, en théorie, un administrateur ayant accès à la clé de récupération de Billy enregistrée dans l'Active Directory de l'école pourrait déverrouiller le disque sans son accord, mais cela dépend des politiques internes de sécurité et de confidentialité :
- **Clé de récupération** : Si l'école a appliqué une politique pour que toutes les clés de récupération BitLocker soient sauvegardées dans l'AD, un administrateur pourrait effectivement accéder aux données en cas de besoin.
- **Respect des procédures** : Cependant, dans la plupart des environnements académiques et professionnels, un administrateur ne peut pas déverrouiller les données d’un étudiant sans suivre des procédures spécifiques, souvent pour des raisons de sécurité ou d’enquête.
- **Confidentialité** : Les établissements mettent en place des règles strictes pour protéger la vie privée des utilisateurs. Toute intervention de ce type doit être justifiée et conforme aux politiques de confidentialité du collège.

#### **Question 4 : Si Billy ne se rappelle pas de son mot de passe ou que son appareil est verrouillé, comment pourrait-il récupérer l’accès à ses données ?**

**Réponse** :
Si Billy oublie son mot de passe ou rencontre un problème de déverrouillage, il peut récupérer l'accès à ses données grâce aux options suivantes :
- **Utilisation de la clé de récupération** : Billy peut utiliser la clé de récupération qu'il a sauvegardée sur son compte Azure AD ou sur son disque dur.
- **Assistance de l’administrateur** : Puisque son appareil est inscrit dans le domaine **cmaisonneuve.qc.ca**, un administrateur peut récupérer la clé de récupération stockée dans l'AD de l'école (si la politique de groupe a été configurée pour cela).
- **Azure AD** : Étant donné que Billy a également sauvegardé la clé dans Azure AD, il peut se connecter à son compte Azure pour accéder à la clé de récupération s'il ne peut pas accéder à celle sur son disque dur.

#### **Question 5 : Quels sont les risques de sauvegarder une clé de récupération sur le disque dur, et comment aurait-il pu mieux sécuriser son appareil ?**

**Réponse** :
Il est risqué pour Billy de stocker la clé de récupération BitLocker directement sur le disque dur, car :
- **Accès facilité en cas de vol** : Si quelqu’un accède physiquement à l'ordinateur, il pourrait trouver et utiliser la clé de récupération pour déverrouiller le disque chiffré.
- **Perte de la clé en cas de panne** : Si le disque dur est endommagé, la clé de récupération pourrait être perdue avec les données.

Pour mieux sécuriser son appareil, Billy aurait pu :
- **Stocker uniquement la clé de récupération dans Azure AD** : Cela lui permettrait de récupérer la clé en ligne, tout en réduisant les risques d’accès non autorisé localement.
- **Ne pas stocker la clé de récupération sur le disque dur** : Les experts recommandent de sauvegarder la clé uniquement sur des emplacements sécurisés et distants, comme un autre appareil ou dans un compte cloud sécurisé.

### Conclusion

En résumé, BitLocker offre une sécurité robuste pour protéger les données de Billy contre tout accès non autorisé. Les administrateurs du domaine **cmaisonneuve.qc.ca** peuvent également gérer et sécuriser l’accès grâce à des stratégies centralisées, tout en suivant les règles de confidentialité du collège.



---------------

# Partie 02- Hypothèse - Joseph, un autre étudiant, a réussi à se connecter au compte de Billy !

Si Joseph, un autre étudiant, a réussi à se connecter au compte de Billy ou à accéder aux données chiffrées sur le même ordinateur, plusieurs facteurs doivent être examinés pour comprendre comment cela est possible. Voici une analyse de la situation et des questions pour mieux cerner les problèmes de configuration et de sécurité potentiels.

---

### Analyse du Scénario

Dans un environnement BitLocker, plusieurs configurations et paramètres peuvent influencer l’accès aux données, même sur un disque chiffré. Voici les principales hypothèses expliquant pourquoi Joseph peut accéder aux données chiffrées de Billy :

1. **Absence de verrouillage complet de BitLocker** : Si BitLocker est configuré pour déverrouiller le disque lors de la connexion de n’importe quel utilisateur AD, alors les utilisateurs enregistrés sur le domaine (comme Joseph) peuvent se connecter et accéder aux données sur l’ordinateur.
2. **Utilisation de sessions partagées** : Il est possible que Billy et Joseph utilisent des comptes avec des sessions partagées ou aient accès à un compte administrateur commun, ce qui leur permet de voir les données de chacun.
3. **Clé de récupération ou authentification simplifiée** : Si BitLocker a été configuré sans authentification forte (comme une clé USB ou un code PIN), l’ordinateur pourrait déverrouiller automatiquement le disque au démarrage sans exiger une authentification individuelle pour chaque utilisateur.
4. **Stratégie de sécurité de domaine (AD)** : Les administrateurs du domaine pourraient avoir configuré des paramètres d’authentification permissifs, ce qui facilite l’accès entre utilisateurs du même domaine.

### Questions pour les étudiants et réponses détaillées

#### **Question 1 : Pourquoi Joseph peut-il accéder aux données de Billy malgré le chiffrement BitLocker ?**

**Réponse** :
Plusieurs configurations de BitLocker et Active Directory peuvent permettre l’accès de Joseph aux données de Billy :

- **Authentification simplifiée** : BitLocker peut être configuré pour ne demander l’authentification qu’au démarrage. Si Billy a laissé son ordinateur allumé, tout utilisateur ayant un compte sur l’ordinateur peut se connecter et accéder aux données chiffrées.
- **Absence de verrouillage par session** : Si Billy n’a pas verrouillé sa session ou que l’ordinateur n’exige pas de nouvelle authentification après la mise en veille, alors tout utilisateur peut reprendre la session de Billy sans restriction.
- **Stratégie de domaine permissive** : Les paramètres de sécurité du domaine peuvent permettre aux utilisateurs du même domaine (cmaisonneuve.qc.ca) d’accéder aux données chiffrées dès que le volume BitLocker est déverrouillé.

#### **Question 2 : Comment les administrateurs du domaine peuvent-ils restreindre cet accès pour qu’un seul utilisateur puisse accéder aux données déverrouillées ?**

**Réponse** :
Les administrateurs peuvent mettre en place plusieurs stratégies pour restreindre l’accès :

- **Stratégie d’authentification renforcée avec BitLocker** : Les administrateurs peuvent imposer des méthodes d'authentification supplémentaires (ex. code PIN, clé USB) avant de déverrouiller BitLocker. Cela limiterait l’accès uniquement à ceux qui possèdent ces informations de déverrouillage spécifiques.
- **Gestion des sessions utilisateur** : Ils peuvent configurer des politiques pour verrouiller automatiquement les sessions lorsque l’ordinateur est en veille ou après un délai d'inactivité.
- **Isoler les utilisateurs** : Les administrateurs peuvent définir des règles de sécurité qui empêchent les utilisateurs de partager les mêmes sessions ou d’accéder aux fichiers d’autres utilisateurs sans une nouvelle authentification.

#### **Question 3 : Si BitLocker est déverrouillé lors de la connexion, est-ce que le chiffrement est toujours utile pour protéger les données de Billy ?**

**Réponse** :
Le chiffrement BitLocker reste utile pour protéger les données de Billy **en cas de vol ou de perte** de l’appareil, car le disque serait inaccessible sans la clé de récupération ou l’authentification initiale. Cependant :

- **Protection contre les autres utilisateurs** : Si le volume est déverrouillé lors de la connexion, tout utilisateur légitime du domaine pourrait accéder aux données une fois l’ordinateur allumé et déverrouillé, ce qui limite l’utilité de BitLocker dans un contexte multi-utilisateur.
- **Sécurisation des données personnelles** : Pour une meilleure protection des données personnelles, des paramètres de sécurité supplémentaires devraient être appliqués pour exiger une authentification pour chaque utilisateur ou session.

#### **Question 4 : Que pourrait faire Billy pour s’assurer que seul lui peut accéder à ses données chiffrées ?**

**Réponse** :
Billy peut prendre plusieurs mesures pour renforcer la sécurité de ses données :

- **Vérifier les options de déverrouillage BitLocker** : Billy pourrait demander à ce que l’authentification BitLocker soit configurée pour lui seul (ex. un code PIN personnel ou une clé USB que seul lui possède).
- **Utiliser le verrouillage automatique de session** : Il peut activer l'option de verrouillage de session lorsqu'il quitte son ordinateur ou configure la mise en veille avec un verrouillage automatique, de sorte qu’un autre utilisateur ne puisse pas reprendre sa session.
- **Déconnexion ou extinction de l’ordinateur** : En quittant sa session ou en éteignant l’ordinateur, Billy s’assure que le volume chiffré est verrouillé jusqu’à la prochaine authentification BitLocker.

#### **Question 5 : Si les administrateurs souhaitent qu'un seul utilisateur accède aux données d’un disque déverrouillé avec BitLocker, quelles recommandations devraient-ils suivre ?**

**Réponse** :
Les administrateurs peuvent suivre ces recommandations pour limiter l'accès BitLocker à un utilisateur unique :

- **Configuration des stratégies de session individuelle** : Ils peuvent définir des stratégies d'authentification qui exigent que chaque utilisateur s’identifie de manière unique avant d'accéder aux données déverrouillées.
- **Appliquer une GPO pour exiger l’authentification BitLocker à chaque ouverture de session** : Cela forcerait une vérification supplémentaire de la clé BitLocker, empêchant ainsi les autres utilisateurs d'accéder aux données de Billy.
- **Audit des sessions et configuration de journaux** : Cela permettrait de suivre les utilisateurs accédant aux sessions partagées et de détecter des tentatives d’accès non autorisées.

---

### Conclusion

Ce cas montre qu’un chiffrement BitLocker peut protéger efficacement un disque entier, mais certaines configurations permettent des accès non souhaités dans les environnements multi-utilisateurs. Avec des stratégies de gestion centralisée et des pratiques de sécurité renforcées, les administrateurs peuvent restreindre l’accès aux seuls utilisateurs autorisés et garantir une meilleure confidentialité des données.


-------------------

# PARTIE 03 - Chaque étudiant a sa propre session !

Dans ce cas, où chaque étudiant (Billy et Joseph) a sa propre session sur l'ordinateur et que Billy a activé BitLocker, voici ce qu'il se passe et comment cela influence l'accès aux données :

### Contexte du Chiffrement BitLocker pour un Ordinateur Multi-Utilisateurs
BitLocker, en tant que solution de **chiffrement de disque complet**, protège les données au niveau du disque physique, pas au niveau des comptes utilisateurs individuels. Voici ce qui se produit dans un environnement multi-utilisateurs comme celui de Billy et Joseph :

1. **Déverrouillage du Disque** : 
   - Lorsque Billy configure BitLocker sur son disque, BitLocker chiffre l'ensemble du disque, y compris les fichiers système, les données personnelles et les fichiers des autres utilisateurs.
   - Pour que le système d’exploitation démarre, BitLocker doit déverrouiller le volume chiffré lors du démarrage de l’ordinateur. Cela se fait généralement via **TPM**, un mot de passe ou une clé USB au moment du démarrage.

2. **Accès après le Déverrouillage Initial** :
   - Une fois que BitLocker déverrouille le disque au démarrage, l’ensemble du disque est accessible au niveau du système, ce qui signifie que tous les utilisateurs avec des comptes valides peuvent se connecter à leurs sessions respectives.
   - Autrement dit, BitLocker ne re-chiffre pas le disque lorsqu’un utilisateur passe d’une session à une autre, car le déchiffrement est appliqué à l’ensemble du disque dès le démarrage.

### Pourquoi Joseph Peut Accéder à Sa Session

- **Isolation des Sessions Utilisateurs** : Dans Windows, les sessions utilisateurs (comme celles de Billy et Joseph) sont isolées au niveau du système d’exploitation, mais **BitLocker ne gère pas les sessions individuelles** ; il protège simplement les données au niveau du disque.
- **BitLocker n’intervient pas dans les sessions utilisateurs** : Une fois le disque déverrouillé, chaque utilisateur peut accéder à ses propres fichiers grâce aux contrôles d'accès de Windows, mais BitLocker n’a plus de rôle dans cette gestion d’accès.
  
### Précisions Techniques

- **Accès Limité aux Données Individuelles** : Bien que le disque soit déverrouillé, les permissions des fichiers et des dossiers au niveau du système d'exploitation Windows empêchent Joseph d’accéder aux fichiers de Billy, et vice-versa. Windows utilise des droits d’accès spécifiques à chaque utilisateur pour protéger les données de chaque session.
  
- **Cas de l'Éteignage et du Redémarrage** : Lorsque l’ordinateur est redémarré ou éteint, BitLocker nécessite à nouveau une authentification (via TPM, clé USB ou mot de passe) pour déverrouiller le disque. Sans cette étape, ni Billy ni Joseph ne pourront accéder aux données stockées sur le disque.

### Pourquoi BitLocker est Toujours Utile dans Ce Cas
BitLocker offre une protection contre les accès non autorisés **si le disque est retiré** ou **si l’ordinateur est volé**. Voici comment :

1. **Protection en Cas de Vol** : Si l'ordinateur de Billy est volé, un tiers ne pourra pas accéder aux données de l’ordinateur sans déverrouiller BitLocker, même s'il tente de connecter le disque sur un autre système.
   
2. **Protection à l’Arrêt** : Lorsque l’ordinateur est éteint, BitLocker relance le chiffrement complet. À chaque redémarrage, il est nécessaire de fournir l’authentification BitLocker pour accéder aux données, offrant une protection robuste au niveau de l'appareil.

### Conclusion
Dans cet environnement, BitLocker protège le disque entier mais fonctionne indépendamment des sessions utilisateurs. Billy et Joseph peuvent tous deux accéder à leurs propres sessions tant que l’ordinateur est allumé et que le volume chiffré est déverrouillé. Cependant, en cas d'arrêt ou de redémarrage, BitLocker redemandera la clé de déverrouillage pour accéder à l'ordinateur, garantissant une sécurité supplémentaire en cas de perte ou de vol.
