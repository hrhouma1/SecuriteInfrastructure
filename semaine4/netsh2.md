# Configuration et Suppression de Politiques IPsec

---
## Exercice 1 : Sécurisation du Trafic Web
---

### Objectif
Configurer une politique IPsec pour sécuriser le trafic web en chiffrant et authentifiant le trafic HTTPS.

### Étapes

1. **Afficher toutes les politiques de sécurité IPsec existantes**
2. **Afficher toutes les actions de filtrage IPsec configurées**
3. **Ajouter une nouvelle action de filtrage appelée "Encrypt_and_Authenticate"**
4. **Créer une nouvelle politique IPsec appelée "Secure_Web_Traffic"**
5. **Créer une nouvelle liste de filtres appelée "Web_Traffic_FilterList"**
6. **Ajouter un filtre à la liste "Web_Traffic_FilterList" pour capturer le trafic HTTPS (port 443)**
7. **Créer une règle nommée "Secure_HTTPS_Rule" qui lie la politique "Secure_Web_Traffic" à l'action de filtrage "Encrypt_and_Authenticate"**
8. **Attribuer et activer la politique "Secure_Web_Traffic"**
9. **Vérifier que la politique et les actions de filtrage sont bien configurées**

---
## Exercice 2 : Sécurisation du Trafic Interne
---

### Objectif
Configurer une politique IPsec pour sécuriser le trafic interne en utilisant uniquement l'authentification.

### Étapes

1. **Ajouter une nouvelle action de filtrage appelée "Authenticate_Only"**
2. **Créer une nouvelle politique IPsec appelée "Internal_Network_Traffic"**
3. **Créer une nouvelle liste de filtres appelée "Internal_Traffic_FilterList"**
4. **Ajouter un filtre à la liste "Internal_Traffic_FilterList" pour capturer le trafic interne (ex. port 8080)**
5. **Créer une règle nommée "Internal_Traffic_Rule" qui lie la politique "Internal_Network_Traffic" à l'action de filtrage "Authenticate_Only"**
6. **Attribuer et activer la politique "Internal_Network_Traffic"**
7. **Vérifier que la politique et les actions de filtrage pour le trafic interne sont bien configurées**

---
## Exercice 3 : Suppression des Politiques et Actions IPsec
---


Une fois les politiques créées et testées, pratiquez en supprimant chaque élément un par un dans l'ordre inverse de la création.

### Étapes de Suppression

1. **Supprimer l'action de filtrage "Encrypt_and_Authenticate"**
2. **Supprimer la politique "Secure_Web_Traffic"**
3. **Supprimer la liste de filtres "Web_Traffic_FilterList"**
4. **Supprimer la règle "Secure_HTTPS_Rule" de la politique "Secure_Web_Traffic"**
5. **Supprimer l'action de filtrage "Authenticate_Only"**
6. **Supprimer la politique "Internal_Network_Traffic"**
7. **Supprimer la liste de filtres "Internal_Traffic_FilterList"**
8. **Supprimer la règle "Internal_Traffic_Rule" de la politique "Internal_Network_Traffic"**
9. **Vérifier que toutes les politiques et actions ont été supprimées**

---

## Vérification Finale

Après la suppression, assurez-vous qu'il ne reste aucune politique ni action de filtrage active.
