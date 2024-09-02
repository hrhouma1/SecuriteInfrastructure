
---

# 📝 Résumé de la Semaine #2 : Sécurité et Analyse avec Sysinternals

----------

# ⚠️ **IMPORTANT - À LIRE** ⚠️

--------

Pour aller à l'essentiel, veuillez consulter le dossier `📂 resume`, qui contient les éléments théoriques et pratiques nécessaires à votre apprentissage.

# Contenu du dossier `resume`

---

# 📚 **Théorie** 💡

----

Pour bien comprendre les concepts de la semaine 2, il est essentiel de lire les documents suivants dans l'ordre :

1. **[🛡️ Introduction aux Attaques et à la Sécurité.md](https://github.com/hrhouma1/SecuriteInfrastructure/blob/main/semaine2/resume/1.%20Introduction%20aux%20Attaques%20et%20%C3%A0%20la%20S%C3%A9curite.md)**  
2. **[🔍 Détection des Violations de Sécurité.md](https://github.com/hrhouma1/SecuriteInfrastructure/blob/main/semaine2/resume/2.%20D%C3%A9tection%20des%20Violations%20de%20S%C3%A9curit%C3%A9.md)**  
3. **[🛠️ Examen de l'Activité avec les Outils Sysinternals.md](https://github.com/hrhouma1/SecuriteInfrastructure/blob/main/semaine2/resume/3.%20Examen%20de%20l'Activit%C3%A9%20avec%20les%20Outils%20Sysinternals.md)**  
4. **[🚨 Stratégies de Réponse aux Incidents.md]([./resume/Stratégies%20de%20Réponse%20aux%20Incidents.md](https://github.com/hrhouma1/SecuriteInfrastructure/blob/main/semaine2/resume/4.%20Strat%C3%A9gies%20de%20R%C3%A9ponse%20aux%20Incidents.md))**  

---

# 🧪 **Pratique** 🔧

---

Pour la partie pratique, suivez les étapes ci-dessous :

1. **[⚙️ 5 - Ateliers-pratiques.md](https://github.com/hrhouma1/SecuriteInfrastructure/blob/main/semaine2/resume/5.%20ateliers-pratiques.md)** (exercez-vous uniquement sur les exercices 1 et 2)  
   - Ces exercices vous permettent de mettre en pratique les concepts théoriques appris dans les sections précédentes. Ignorez les autres exercices dans ce document.

2. **[🎯 Exercice-pratique-3.md](https://github.com/hrhouma1/SecuriteInfrastructure/blob/main/semaine2/resume/5.%20exercice-pratique-3.md)**  
   - Passez ensuite directement à cet exercice, qui constitue la dernière étape de la semaine 2. Ce document complète votre apprentissage avec un exercice final important.

---

-------
# Annexe - Table des Matières
--------

1. 📖 **[Introduction aux Attaques et à la Sécurité](1. Introduction aux Attaques et à la Sécurité.md)**
   - Aperçu des menaces courantes telles que le phishing, les attaques DDoS, et les ransomwares.
   - Importance de la surveillance continue pour la protection des infrastructures informatiques.

2. 🔍 **[Détection des Violations de Sécurité](2. Détection des Violations de Sécurité.md)**
   - Configuration et utilisation de System Monitor (Sysmon) et Process Monitor.
   - Détection proactive des comportements suspects sur un système Windows.

3. 🛠️ **[Examen de l'Activité avec les Outils Sysinternals](3. Examen de l'Activité avec les Outils Sysinternals.md)**
   - Utilisation de Process Explorer et Autoruns pour analyser en détail les processus en cours et les programmes de démarrage.
   - Identification des anomalies et des signes d'activités malveillantes.

4. 🚀 **[Partie Pratique : Sysinternals](4. Partie-pratique-Sysinternals.md)**
   - Ateliers pratiques pour appliquer les concepts théoriques avec les outils Sysinternals.
   - Exemples concrets de détection d'activités suspectes et d'analyse de systèmes compromis.

5. 🛡️ **[Stratégies de Réponse aux Incidents](5. Stratégies de Réponse aux Incidents.md)**
   - Scénarios d'incidents et discussion interactive sur les meilleures pratiques de réponse.
   - Élaboration d'un plan de réponse aux incidents efficace et structuré.

6. 🎯 **[Ateliers Pratiques](5. ateliers-pratiques.md)**
   - Exercices détaillés pour configurer Sysmon, utiliser Process Monitor, et rédiger des rapports d'incidents.
   - Approfondissement des compétences en gestion des menaces et en analyse de la sécurité.


7. **[🎯 Exercice-pratique-3.md](https://github.com/hrhouma1/SecuriteInfrastructure/blob/main/semaine2/resume/5.%20exercice-pratique-3.md)**  
   - Passez ensuite directement à cet exercice, qui constitue la dernière étape de la semaine 2. Ce document complète votre apprentissage avec un exercice final important.





---------
# Vue d'ensemble
--------

### *Partie Théorique*

| **Session** | **Sujet** | **Contenu** | **Objectifs** |
|-------------|------------|-------------|---------------|
| **1**       | Introduction aux Attaques et à la Sécurité | Présentation des types d'attaques courantes, leur impact, et l'importance de la surveillance. | Comprendre les bases des attaques et pourquoi il est essentiel de surveiller les infrastructures. |
| **2**       | Types d'Attaques Courantes | Exploration des méthodes d'attaque telles que le phishing, les attaques DDoS, et les ransomwares. | Identifier et comprendre les principales attaques auxquelles les infrastructures sont exposées. |
| **3**       | Détection des Violations de Sécurité | Introduction à l'utilisation de Sysmon et Process Monitor pour surveiller et détecter les violations de sécurité. | Comprendre l'importance de la surveillance proactive des événements système. |
| **4**       | Configuration de Sysmon | Guide pour le téléchargement, l'installation et la configuration de Sysmon sur une machine virtuelle. | Savoir comment installer et configurer Sysmon pour capturer les événements critiques. |
| **5**       | Surveillance des Événements avec Sysmon | Utilisation de Sysmon pour surveiller les événements et identifier les comportements suspects. | Apprendre à analyser les journaux Sysmon pour détecter des activités anormales. |
| **6**       | Process Monitor | Introduction à Process Monitor pour surveiller les activités système en temps réel et analyser les comportements suspects. | Comprendre comment Process Monitor peut être utilisé pour identifier et analyser les événements système critiques. |
| **7**       | Examen de l'Activité avec les Outils Sysinternals | Utilisation de Process Explorer et Autoruns pour analyser les processus en cours et les programmes de démarrage. | Découvrir d'autres outils pour une analyse approfondie des processus et des programmes. |
| **8**       | Stratégies de Réponse aux Incidents | Élaboration de stratégies pour répondre aux incidents de sécurité, en utilisant les outils Sysinternals. | Apprendre à développer une réponse structurée et efficace aux incidents de sécurité. |

### *Partie Pratique*

| **Étape**   | **Tâche** | **Contenu** | **Objectifs** |
|-------------|------------|-------------|---------------|
| **1**       | Installation de Sysmon | Téléchargement, installation, et configuration de Sysmon. | Configurer Sysmon pour capturer et analyser les événements critiques. |
| **2**       | Exercice 1 : Configurer Sysmon | Configuration de Sysmon sur une machine virtuelle pour détecter une activité anormale. | Apprendre à utiliser Sysmon pour surveiller et détecter des activités suspectes. |
| **3**       | Exercice 2 : Utiliser Process Monitor | Surveillance d'un processus malveillant simulé avec Process Monitor. | Utiliser Process Monitor pour analyser les modifications système en temps réel. |
| **4**       | Exercice 3 : Analyser une Machine Infectée | Analyse d'une machine infectée en utilisant Process Explorer et Autoruns, et rédaction d'un rapport d'incident. | Apprendre à identifier, analyser, et documenter les menaces de sécurité. |



