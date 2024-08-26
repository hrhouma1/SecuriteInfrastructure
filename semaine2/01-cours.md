# **Sécurité et Réponse aux Incidents**

1. [**Introduction aux Attaques et à la Sécurité**](#introduction-aux-attaques-et-à-la-sécurité)
   - [Objectif](#objectif)
   - [Types d'Attaques Courantes](#types-dattaques-courantes)
   - [Phishing](#phishing)
   - [DDoS (Denial of Service Distribué)](#ddos-denial-of-service-distribué)
   - [Ransomware](#ransomware)
   - [Pourquoi Surveiller les Activités au Sein d'une Infrastructure ?](#pourquoi-surveiller-les-activités-au-sein-dune-infrastructure)
   - [Exemples Réels d'Attaques et Leur Impact](#exemples-réels-dattaques-et-leur-impact)

2. [**Détection des Violations de Sécurité**](#détection-des-violations-de-sécurité)
   - [System Monitor (Sysmon)](#system-monitor-sysmon)
   - [Téléchargement et Installation de Sysmon](#téléchargement-et-installation-de-sysmon)
   - [Configuration de Sysmon](#configuration-de-sysmon)
   - [Surveillance des Événements avec Sysmon](#surveillance-des-événements-avec-sysmon)
   - [Exemple Pratique](#exemple-pratique)
   - [Process Monitor](#process-monitor)
   - [Téléchargement et Lancement de Process Monitor](#téléchargement-et-lancement-de-process-monitor)
   - [Filtrage des Activités](#filtrage-des-activités)
   - [Surveillance en Temps Réel](#surveillance-en-temps-réel)
   - [Analyse des Résultats](#analyse-des-résultats)
   - [Exemple Pratique](#exemple-pratique-1)

3. [**Examen de l'Activité avec les Outils Sysinternals**](#examen-de-lactivité-avec-les-outils-sysinternals)
   - [Présentation des Outils Sysinternals](#présentation-des-outils-sysinternals)
   - [Process Explorer](#process-explorer)
   - [Autoruns](#autoruns)
   - [Stratégies d'Investigation avec Sysinternals](#stratégies-dinvestigation-avec-sysinternals)
   - [Approche Globale](#approche-globale)
   - [Précautions et Meilleures Pratiques](#précautions-et-meilleures-pratiques)
   - [Conclusion avant la Démonstration Pratique](#conclusion-avant-la-démonstration-pratique)

4. [**Stratégies de Réponse aux Incidents**](#stratégies-de-réponse-aux-incidents)
   - [Scénario d'Entreprise Attaquée](#scénario-dentreprise-attaquée)
   - [Discussion Interactive](#discussion-interactive)
   - [Plan de Réponse aux Incidents](#plan-de-réponse-aux-incidents)
   - [Isolation des Systèmes Compromis](#isolation-des-systèmes-compromis)
   - [Communication de Crise](#communication-de-crise)
   - [Restauration des Systèmes](#restauration-des-systèmes)
   - [Post-Mortem et Amélioration Continue](#post-mortem-et-amélioration-continue)

5. [**Atelier Pratique**](#atelier-pratique)
   - [Préambule : Choix du Système d'Exploitation](#préambule--choix-du-système-dexploitation)
   - [Exercice 1 : Configurer Sysmon sur une Machine Virtuelle et Détecter une Activité Anormale](#exercice-1--configurer-sysmon-sur-une-machine-virtuelle-et-détecter-une-activité-anormale)
   - [Exercice 2 : Utiliser Process Monitor pour Suivre l'Activité d'un Processus Malveillant Simulé](#exercice-2--utiliser-process-monitor-pour-suivre-lactivité-dun-processus-malveillant-simulé)
   - [Exercice 3 : Analyser une Machine Infectée avec les Outils Sysinternals et Rédiger un Rapport d'Incident](#exercice-3--analyser-une-machine-infectée-avec-les-outils-sysinternals-et-rédiger-un-rapport-dincident)

6. [**Conclusion et Q&A**](#conclusion-et-qa)
   - [Synthèse](#synthèse)
   - [Questions et Réponses](#questions-et-réponses)

---

### Introduction aux Attaques et à la Sécurité

- [Objectif](#objectif) : L'objectif de cette section est de sensibiliser les participants aux différents types d'attaques courantes qui menacent les infrastructures informatiques modernes, et pourquoi il est crucial de surveiller les activités au sein d'une infrastructure.
- [Types d'Attaques Courantes](#types-dattaques-courantes)
  - [Phishing](#phishing)
  - [DDoS (Denial of Service Distribué)](#ddos-denial-of-service-distribué)
  - [Ransomware](#ransomware)
- [Pourquoi Surveiller les Activités au Sein d'une Infrastructure ?](#pourquoi-surveiller-les-activités-au-sein-dune-infrastructure)
- [Exemples Réels d'Attaques et Leur Impact](#exemples-réels-dattaques-et-leur-impact)

[Revenir en haut](#sécurité-et-réponse-aux-incidents)

---

### Détection des Violations de Sécurité

- [System Monitor (Sysmon)](#system-monitor-sysmon)
  - [Téléchargement et Installation de Sysmon](#téléchargement-et-installation-de-sysmon)
  - [Configuration de Sysmon](#configuration-de-sysmon)
  - [Surveillance des Événements avec Sysmon](#surveillance-des-événements-avec-sysmon)
  - [Exemple Pratique](#exemple-pratique)
- [Process Monitor](#process-monitor)
  - [Téléchargement et Lancement de Process Monitor](#téléchargement-et-lancement-de-process-monitor)
  - [Filtrage des Activités](#filtrage-des-activités)
  - [Surveillance en Temps Réel](#surveillance-en-temps-réel)
  - [Analyse des Résultats](#analyse-des-résultats)
  - [Exemple Pratique](#exemple-pratique-1)

[Revenir en haut](#sécurité-et-réponse-aux-incidents)

---

### Examen de l'Activité avec les Outils Sysinternals

- [Présentation des Outils Sysinternals](#présentation-des-outils-sysinternals)
  - [Process Explorer](#process-explorer)
  - [Autoruns](#autoruns)
- [Stratégies d'Investigation avec Sysinternals](#stratégies-dinvestigation-avec-sysinternals)
  - [Approche Globale](#approche-globale)
  - [Précautions et Meilleures Pratiques](#précautions-et-meilleures-pratiques)
- [Conclusion avant la Démonstration Pratique](#conclusion-avant-la-démonstration-pratique)

[Revenir en haut](#sécurité-et-réponse-aux-incidents)

---

### Stratégies de Réponse aux Incidents

- [Scénario d'Entreprise Attaquée](#scénario-dentreprise-attaquée)
- [Discussion Interactive](#discussion-interactive)
- [Plan de Réponse aux Incidents](#plan-de-réponse-aux-incidents)
  - [Isolation des Systèmes Compromis](#isolation-des-systèmes-compromis)
  - [Communication de Crise](#communication-de-crise)
  - [Restauration des Systèmes](#restauration-des-systèmes)
  - [Post-Mortem et Amélioration Continue](#post-mortem-et-amélioration-continue)

[Revenir en haut](#sécurité-et-réponse-aux-incidents)

---

### Atelier Pratique

- [Préambule : Choix du Système d'Exploitation](#préambule--choix-du-système-dexploitation)
- [Exercice 1 : Configurer Sysmon sur une Machine Virtuelle et Détecter une Activité Anormale](#exercice-1--configurer-sysmon-sur-une-machine-virtuelle-et-détecter-une-activité-anormale)
- [Exercice 2 : Utiliser Process Monitor pour Suivre l'Activité d'un Processus Malveillant Simulé](#exercice-2--utiliser-process-monitor-pour-suivre-lactivité-dun-processus-malveillant-simulé)
- [Exercice 3 : Analyser une Machine Infectée avec les Outils Sysinternals et Rédiger un Rapport d'Incident](#exercice-3--analyser-une-machine-infectée-avec-les-outils-sysinternals-et-rédiger-un-rapport-dincident)

[Revenir en haut](

#sécurité-et-réponse-aux-incidents)

---

### Conclusion et Q&A

- [Synthèse](#synthèse)
- [Questions et Réponses](#questions-et-réponses)

[Revenir en haut](#sécurité-et-réponse-aux-incidents)

---

Ce plan de cours guide les participants à travers les concepts théoriques et pratiques essentiels pour comprendre et répondre aux incidents de sécurité. Il est structuré pour permettre un apprentissage progressif, avec des exemples pratiques et des ateliers qui renforcent les compétences acquises.
