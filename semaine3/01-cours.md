# Plan
# *Analyse des activités systèmes et réseaux*
## **Table des matières**

1. [Présentation de l’audit](#presentation-de-laudit)
2. [Audit avancé et PowerShell](#audit-avance-et-powershell)
3. [Analyse des journaux des événements](#analyse-des-journaux-des-evenements)
4. [Examen du trafic réseau avec Microsoft Message Analyzer](#examen-du-trafic-reseau-avec-microsoft-message-analyzer)
5. [Trafic SMB : Sécurisation et analyse](#trafic-smb-securisation-et-analyse)



---

# **1. Présentation de l’audit** <a id="presentation-de-laudit"></a>

## **1.1 Objectifs**

L'objectif de cette section est de vous introduire aux bases de l’audit dans le contexte des systèmes informatiques, en particulier dans une infrastructure Microsoft. Nous allons expliquer ce qu’est un audit, pourquoi il est important, et quels sont les outils de base que vous pouvez utiliser pour effectuer un audit.

**Sous-objectifs :**
- **Comprendre ce qu’est un audit** : Vous saurez ce que signifie "auditer" un système, avec des exemples simples.
- **Comprendre pourquoi l’audit est important** : Vous verrez pourquoi il est essentiel de surveiller et de vérifier ce qui se passe sur vos systèmes informatiques.
- **Connaître les outils d’audit de base dans Windows** : Vous découvrirez quelques outils que Windows met à votre disposition pour réaliser des audits.

## **1.2 Introduction aux concepts de l’audit**

**Qu’est-ce qu’un audit ?**

L’audit informatique est un processus systématique de collecte, d’évaluation et de vérification d'informations provenant d’un système informatique. L'objectif est de déterminer si les contrôles, les politiques et les procédures d'une entreprise sont respectés, efficaces et contribuent à la sécurité de l'environnement informatique.

Imaginez que votre système informatique est une maison. Un audit, c’est un peu comme faire un tour complet de votre maison pour vérifier que toutes les portes et fenêtres sont bien fermées, que tout fonctionne correctement, et qu’il n’y a pas de problèmes cachés.

Dans le monde informatique, auditer un système signifie vérifier et analyser ce qui se passe dans vos ordinateurs et réseaux. Par exemple, vous pouvez auditer pour voir qui s'est connecté, quelles modifications ont été faites, ou si quelqu'un a essayé d'accéder à des informations sensibles sans autorisation.

**Pourquoi est-ce important ?**

Si vous ne vérifiez jamais votre maison, vous ne saurez pas si une porte est restée ouverte ou si quelque chose a été volé. De la même manière, si vous ne faites jamais d’audit de votre système informatique, vous pourriez passer à côté de problèmes sérieux, comme des failles de sécurité ou des erreurs dans la configuration.

Faire un audit vous aide à :
- **Protéger vos données** : En vous assurant que seules les bonnes personnes y ont accès.
- **Respecter la loi** : Certaines lois exigent que vous gardiez une trace de ce qui se passe dans vos systèmes.
- **Améliorer les performances** : En identifiant ce qui ne fonctionne pas bien et en le corrigeant.



## **1.3 Comprendre l’importance de l’audit dans une infrastructure Microsoft**

Microsoft est l’un des fournisseurs les plus courants pour les systèmes informatiques des entreprises. Si vous utilisez des systèmes Windows, il est crucial de savoir comment auditer ces systèmes pour garantir leur sécurité et leur bon fonctionnement.

**Exemple concret :**
- Imaginez que vous travaillez dans une entreprise où les employés doivent se connecter à des ordinateurs pour faire leur travail. Sans audit, vous ne sauriez pas si quelqu'un essaie de se connecter en utilisant les informations d'un autre employé ou si un logiciel malveillant tente de modifier des fichiers importants.
  
En faisant des audits réguliers, vous pouvez :
- **Surveiller qui fait quoi sur le système** : Par exemple, vous saurez si quelqu'un a essayé de se connecter à un fichier confidentiel sans autorisation.
- **Repérer les anomalies** : Comme des tentatives de connexion à des heures inhabituelles, ou des erreurs fréquentes dans certaines applications.
- **Prévenir les incidents** : En détectant les problèmes avant qu’ils ne deviennent graves, vous pouvez éviter des pannes ou des failles de sécurité.

# RÉSUMÉ:

Dans une infrastructure Microsoft, l’audit joue un rôle essentiel pour garantir la sécurité et la conformité. Les systèmes Windows offrent plusieurs fonctionnalités et outils qui facilitent la mise en place d’un processus d’audit rigoureux. 

**Raisons spécifiques de l’audit dans un environnement Microsoft :**
- **Contrôle des accès** : S’assurer que seuls les utilisateurs autorisés accèdent aux ressources critiques.
- **Surveillance des actions** : Suivre les actions des utilisateurs et des administrateurs pour prévenir les abus et identifier les comportements suspects.
- **Gestion des incidents** : Permet de reconstituer les événements en cas de problème pour comprendre la cause et améliorer les défenses.


## **1.4 Contenu**

Dans cette section, nous allons explorer les bases de l’audit et les outils simples que vous pouvez utiliser dans Windows pour effectuer des audits.

## **1.4.1 Les bases de l’audit**



**Les types d’audit :**

- **Audit de Sécurité** : C’est comme vérifier si toutes les portes et fenêtres de votre maison sont bien fermées. Vous regardez si les données de votre système sont bien protégées et si les personnes non autorisées ne peuvent pas y accéder.
- **Audit de Conformité** : Imaginez que vous avez des règles strictes dans votre maison, comme ne jamais laisser de nourriture sur la table. Un audit de conformité vérifierait si ces règles sont bien respectées. Dans un système informatique, cela signifie vérifier que les politiques et les lois sont suivies.
- **Audit Opérationnel** : Ici, on regarde si tout fonctionne correctement. C’est comme vérifier que tous les appareils de votre maison (comme le four ou la télé) fonctionnent bien. Dans un système informatique, cela revient à s'assurer que les processus sont efficaces et que le système fonctionne bien.

**Pour résumé, les concepts clés à retenir pour les types d'audit sont :**
- **Audit de Sécurité** : Processus visant à identifier les vulnérabilités et à évaluer la posture de sécurité d'un système informatique.
- **Audit de Conformité** : Vérification de l'alignement des pratiques avec les lois, réglementations, normes et politiques internes.
- **Audit Opérationnel** : Évaluation de l'efficacité et de l'efficience des processus opérationnels, souvent utilisé pour optimiser les performances du système.



**Le processus d’audit :**

1. **Planification** : Décider quoi vérifier, par exemple, les tentatives de connexion au système.
2. **Collecte des données** : Rassembler les informations sur ce qui se passe dans le système, comme les journaux de connexion.
3. **Analyse** : Regarder ces données pour repérer des anomalies, comme des connexions à des heures bizarres.
4. **Rapport** : Écrire un rapport sur ce que vous avez trouvé, par exemple, que quelqu’un a essayé de se connecter à 3h du matin.
5. **Suivi des actions correctives** : Corriger les problèmes détectés, comme bloquer l’accès à quelqu’un qui a tenté de se connecter sans autorisation.

## **1.4.2 Outils d’audit disponibles dans Windows**

**Quelques outils simples pour commencer :**

- **Event Viewer (Observateur d'événements)** : C’est un peu comme une caméra de surveillance qui enregistre tout ce qui se passe sur votre système. Vous pouvez y voir qui s’est connecté, quelles erreurs sont survenues, etc.
- **Group Policy Audit Settings (Paramètres d’audit des stratégies de groupe)** : Vous pouvez configurer ces paramètres pour dire au système ce que vous voulez surveiller, comme les connexions aux fichiers importants.
- **PowerShell** : C’est un outil puissant que vous pouvez utiliser pour automatiser des tâches, y compris les audits. Par exemple, vous pouvez écrire un petit programme qui vérifie chaque jour les nouvelles connexions au système.
- **Microsoft Advanced Threat Analytics (ATA)** : Cet outil analyse les comportements sur le réseau pour détecter des menaces potentielles, comme si quelqu’un essaie de voler des données.

# RÉSUMÉ:

Windows propose une variété d'outils pour réaliser des audits complets, parmi lesquels :

- **Event Viewer (Observateur d'événements)** : Un outil intégré qui permet de consulter et d’analyser les journaux des événements pour détecter des anomalies ou des tentatives d’intrusion.
- **Group Policy Audit Settings** : Les paramètres de stratégie de groupe peuvent être configurés pour auditer divers types d’événements, tels que les tentatives de connexion, les accès aux fichiers, etc.
- **PowerShell** : PowerShell permet d’automatiser la collecte de données d’audit et d’effectuer des audits avancés grâce à des scripts personnalisés.
- **Microsoft Advanced Threat Analytics (ATA)** : Un outil pour détecter les menaces en analysant les activités réseau et les comportements des utilisateurs.

[Retour en haut](#plan)



---
---
---

## **2. Audit avancé et PowerShell** <a id="audit-avance-et-powershell"></a>

### Objectifs
- Approfondir les techniques d’audit avec PowerShell
- Automatisation des processus d’audit

### Contenu
- Scripts PowerShell pour l’audit
- Exemples pratiques d’audit avancé

[Retour en haut](#plan)


---
---
---

## **3. Analyse des journaux des événements** <a id="analyse-des-journaux-des-evenements"></a>

### Objectifs
- Comprendre l’analyse des journaux des événements
- Apprendre à identifier les anomalies

### Contenu
- Accès aux journaux des événements
- Analyse et interprétation des données des journaux

[Retour en haut](#plan)


---
---
---

## **4. Examen du trafic réseau avec Microsoft Message Analyzer** <a id="examen-du-trafic-reseau-avec-microsoft-message-analyzer"></a>

### Objectifs
- Analyser le trafic réseau à l’aide de Microsoft Message Analyzer
- Identifier et résoudre les problèmes de réseau

### Contenu
- Introduction à Microsoft Message Analyzer
- Études de cas d’analyse de trafic réseau

[Retour en haut](#plan)


---
---
---

## **5. Trafic SMB : Sécurisation et analyse** <a id="trafic-smb-securisation-et-analyse"></a>

### Objectifs
- Comprendre les vulnérabilités du trafic SMB
- Apprendre à sécuriser le trafic SMB

### Contenu
- Introduction au protocole SMB
- Techniques de sécurisation du SMB
- Analyse des attaques courantes sur SMB

[Retour en haut](#plan)

