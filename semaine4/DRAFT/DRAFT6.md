# README : Configuration et Suppression de Politiques IPsec

## Exercice 1 : Sécurisation du Trafic Web

### Objectif
Configurer une politique IPsec pour sécuriser le trafic web en chiffrant et authentifiant le trafic HTTPS.

### Étapes

1. Afficher toutes les politiques de sécurité IPsec existantes :
   ```bash
   netsh ipsec static show policy all
   ```

2. Afficher toutes les actions de filtrage IPsec configurées :
   ```bash
   netsh ipsec static show filteraction all
   ```

3. Ajouter une nouvelle action de filtrage appelée "Encrypt_and_Authenticate" :
   ```bash
   netsh ipsec static add filteraction name="Encrypt_and_Authenticate" action=negotiate
   ```

4. Créer une nouvelle politique IPsec appelée "Secure_Web_Traffic" :
   ```bash
   netsh ipsec static add policy name="Secure_Web_Traffic"
   ```

5. Créer une nouvelle liste de filtres appelée "Web_Traffic_FilterList" :
   ```bash
   netsh ipsec static add filterlist name="Web_Traffic_FilterList"
   ```

6. Ajouter un filtre à la liste "Web_Traffic_FilterList" pour capturer le trafic HTTPS (port 443) :
   ```bash
   netsh ipsec static add filter filterlist="Web_Traffic_FilterList" srcaddr=any dstaddr=any protocol=TCP dstport=443
   ```

7. Créer une règle nommée "Secure_HTTPS_Rule" qui lie la politique "Secure_Web_Traffic" à l'action de filtrage "Encrypt_and_Authenticate" :
   ```bash
   netsh ipsec static add rule name="Secure_HTTPS_Rule" policy="Secure_Web_Traffic" filterlist="Web_Traffic_FilterList" filteraction="Encrypt_and_Authenticate"
   ```

8. Attribuer et activer la politique "Secure_Web_Traffic" :
   ```bash
   netsh ipsec static set policy name="Secure_Web_Traffic" assign=y
   ```

9. Vérifier que la politique et les actions de filtrage sont bien configurées :
   ```bash
   netsh ipsec static show policy all
   netsh ipsec static show filteraction all
   ```

---

## Exercice 2 : Sécurisation du Trafic Interne

### Objectif
Configurer une politique IPsec pour sécuriser le trafic interne avec une authentification seule.

### Étapes

1. Ajouter une action de filtrage appelée "Authenticate_Only" pour authentifier le trafic interne :
   ```bash
   netsh ipsec static add filteraction name="Authenticate_Only" action=negotiate
   ```

2. Créer une nouvelle politique IPsec appelée "Internal_Network_Traffic" :
   ```bash
   netsh ipsec static add policy name="Internal_Network_Traffic"
   ```

3. Créer une nouvelle liste de filtres appelée "Internal_Traffic_FilterList" :
   ```bash
   netsh ipsec static add filterlist name="Internal_Traffic_FilterList"
   ```

4. Ajouter un filtre à la liste "Internal_Traffic_FilterList" pour capturer le trafic interne (port 8080) :
   ```bash
   netsh ipsec static add filter filterlist="Internal_Traffic_FilterList" srcaddr=any dstaddr=any protocol=TCP dstport=8080
   ```

5. Créer une règle nommée "Internal_Traffic_Rule" qui lie la politique "Internal_Network_Traffic" à l'action de filtrage "Authenticate_Only" :
   ```bash
   netsh ipsec static add rule name="Internal_Traffic_Rule" policy="Internal_Network_Traffic" filterlist="Internal_Traffic_FilterList" filteraction="Authenticate_Only"
   ```

6. Attribuer et activer la politique "Internal_Network_Traffic" :
   ```bash
   netsh ipsec static set policy name="Internal_Network_Traffic" assign=y
   ```

7. Vérifier que la politique et les actions de filtrage sont bien configurées :
   ```bash
   netsh ipsec static show policy all
   netsh ipsec static show filteraction all
   ```

---

## Pratique : Suppression des Politiques et Actions IPsec

Ensuite, vous allez pratiquer la suppression de chaque élément IPsec un par un.

### Étapes de Suppression

1. Supprimer l'action de filtrage "Encrypt_and_Authenticate" :
   ```bash
   netsh ipsec static delete filteraction name="Encrypt_and_Authenticate"
   ```

2. Supprimer la politique "Secure_Web_Traffic" :
   ```bash
   netsh ipsec static delete policy name="Secure_Web_Traffic"
   ```

3. Supprimer la liste de filtres "Web_Traffic_FilterList" :
   ```bash
   netsh ipsec static delete filterlist name="Web_Traffic_FilterList"
   ```

4. Supprimer la règle "Secure_HTTPS_Rule" :
   ```bash
   netsh ipsec static delete rule name="Secure_HTTPS_Rule" policy="Secure_Web_Traffic"
   ```

5. Supprimer l'action de filtrage "Authenticate_Only" :
   ```bash
   netsh ipsec static delete filteraction name="Authenticate_Only"
   ```

6. Supprimer la politique "Internal_Network_Traffic" :
   ```bash
   netsh ipsec static delete policy name="Internal_Network_Traffic"
   ```

7. Supprimer la liste de filtres "Internal_Traffic_FilterList" :
   ```bash
   netsh ipsec static delete filterlist name="Internal_Traffic_FilterList"
   ```

8. Supprimer la règle "Internal_Traffic_Rule" :
   ```bash
   netsh ipsec static delete rule name="Internal_Traffic_Rule" policy="Internal_Network_Traffic"
   ```

9. Vérifier que toutes les politiques et actions de filtrage ont bien été supprimées :
   ```bash
   netsh ipsec static show policy all
   netsh ipsec static show filteraction all
   ```

---

