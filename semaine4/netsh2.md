# Configuration et Suppression de Politiques IPsec

---
## Exercice 1 : Sécurisation du Trafic Web
---

### Objectif
Configurer une politique IPsec pour sécuriser le trafic web en chiffrant et authentifiant le trafic HTTPS.

### Étapes

1. **Afficher toutes les politiques de sécurité IPsec existantes**
   - Balise : `Show_Security_Policies`

2. **Afficher toutes les actions de filtrage IPsec configurées**
   - Balise : `Show_Filter_Actions`

3. **Ajouter une nouvelle action de filtrage appelée "Encrypt_and_Authenticate"**
   - Balise : `Add_Encrypt_And_Authenticate_FilterAction`

4. **Créer une nouvelle politique IPsec appelée "Secure_Web_Traffic"**
   - Balise : `Create_Secure_Web_Traffic_Policy`

5. **Créer une nouvelle liste de filtres appelée "Web_Traffic_FilterList"**
   - Balise : `Create_Web_Traffic_FilterList`

6. **Ajouter un filtre à la liste "Web_Traffic_FilterList" pour capturer le trafic HTTPS (port 443)**
   - Balise : `Add_HTTPS_Filter_To_FilterList`

7. **Créer une règle nommée "Secure_HTTPS_Rule" qui lie la politique "Secure_Web_Traffic" à l'action de filtrage "Encrypt_and_Authenticate"**
   - Balise : `Create_Secure_HTTPS_Rule`

8. **Attribuer et activer la politique "Secure_Web_Traffic"**
   - Balise : `Assign_Secure_Web_Traffic_Policy`

9. **Vérifier que la politique et les actions de filtrage sont bien configurées**
   - Balises : `Verify_Security_Policies` et `Verify_Filter_Actions`

---
## Exercice 2 : Sécurisation du Trafic Interne
---

### Objectif
Configurer une politique IPsec pour sécuriser le trafic interne en utilisant uniquement l'authentification.

### Étapes

1. **Ajouter une nouvelle action de filtrage appelée "Authenticate_Only"**
   - Balise : `Add_Authenticate_Only_FilterAction`

2. **Créer une nouvelle politique IPsec appelée "Internal_Network_Traffic"**
   - Balise : `Create_Internal_Network_Traffic_Policy`

3. **Créer une nouvelle liste de filtres appelée "Internal_Traffic_FilterList"**
   - Balise : `Create_Internal_Traffic_FilterList`

4. **Ajouter un filtre à la liste "Internal_Traffic_FilterList" pour capturer le trafic interne (ex. port 8080)**
   - Balise : `Add_Internal_Traffic_Filter`

5. **Créer une règle nommée "Internal_Traffic_Rule" qui lie la politique "Internal_Network_Traffic" à l'action de filtrage "Authenticate_Only"**
   - Balise : `Create_Internal_Traffic_Rule`

6. **Attribuer et activer la politique "Internal_Network_Traffic"**
   - Balise : `Assign_Internal_Network_Traffic_Policy`

7. **Vérifier que la politique et les actions de filtrage pour le trafic interne sont bien configurées**
   - Balises : `Verify_Security_Policies` et `Verify_Filter_Actions`

---
## Exercice 3 : Suppression des Politiques et Actions IPsec
---


Une fois les politiques créées et testées, pratiquez en supprimant chaque élément un par un dans l'ordre inverse de la création.

### Étapes de Suppression

1. **Supprimer l'action de filtrage "Encrypt_and_Authenticate"**
   - Balise : `Delete_Encrypt_And_Authenticate_FilterAction`

2. **Supprimer la politique "Secure_Web_Traffic"**
   - Balise : `Delete_Secure_Web_Traffic_Policy`

3. **Supprimer la liste de filtres "Web_Traffic_FilterList"**
   - Balise : `Delete_Web_Traffic_FilterList`

4. **Supprimer la règle "Secure_HTTPS_Rule" de la politique "Secure_Web_Traffic"**
   - Balise : `Delete_Secure_HTTPS_Rule`

5. **Supprimer l'action de filtrage "Authenticate_Only"**
   - Balise : `Delete_Authenticate_Only_FilterAction`

6. **Supprimer la politique "Internal_Network_Traffic"**
   - Balise : `Delete_Internal_Network_Traffic_Policy`

7. **Supprimer la liste de filtres "Internal_Traffic_FilterList"**
   - Balise : `Delete_Internal_Traffic_FilterList`

8. **Supprimer la règle "Internal_Traffic_Rule" de la politique "Internal_Network_Traffic"**
   - Balise : `Delete_Internal_Traffic_Rule`

9. **Vérifier que toutes les politiques et actions ont été supprimées**
   - Balises : `Verify_Security_Policies` et `Verify_Filter_Actions`

---

## Vérification Finale

Après la suppression, assurez-vous qu'il ne reste aucune politique ni action de filtrage active.
