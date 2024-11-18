
**Question : Configuration des Zones Inversées dans Bind9**

- Dans le cadre de la configuration DNS pour notre infrastructure réseau, vous avez déjà créé une zone directe permettant de résoudre les noms de domaine vers des adresses IP. 
- Cependant, pour assurer une configuration complète et fonctionnelle, notamment pour le serveur mail et les diagnostics réseau, il est demandé d'ajouter des zones inversées.

1. Pourquoi est-il important de configurer une zone inversée (reverse DNS) dans notre contexte ?
2. En utilisant Bind9, proposez une configuration pour les zones inversées des réseaux suivants :
   - **Réseau interne** : 20.21.22.0/24
   - **Réseau DMZ** : 10.11.12.0/24
   - **Réseau backend** : 192.168.100.0/24
3. Montrez comment tester la configuration de ces zones inversées en utilisant `dig` ou `nslookup`.

---

**Indications :**
- Assurez-vous que chaque zone inversée associe correctement les adresses IP aux noms d’hôtes spécifiés dans la zone directe.
- Pensez à vérifier vos fichiers de configuration avec `named-checkzone` et à redémarrer le service Bind9 après modification.

Bonne chance !

--------
--------
----------

# Réponse : 


- Concernant la zone inversée : **oui**, dans ce contexte, il est nécessaire de configurer une zone inversée (reverse DNS zone).
- La zone inversée permet de résoudre une adresse IP vers un nom de domaine, ce qui est souvent requis dans des environnements de production, notamment pour les services comme le mail ou pour des configurations avancées.

---

## **Pourquoi une zone inversée est-elle nécessaire ?**
1. **Postfix (serveur mail)** :
   - Le serveur de messagerie peut nécessiter une résolution inversée pour valider les connexions SMTP.
   - Sans zone inversée correctement configurée, certains serveurs de destination pourraient refuser vos emails.

2. **Diagnostic réseau** :
   - Lors des tests avec `ping` ou d’autres outils, une zone inversée simplifie la correspondance entre IP et noms d’hôtes.

3. **Conformité DNS** :
   - La configuration complète d’un DNS avec zones directes et inversées est une bonne pratique, même si toutes les applications ne l’exigent pas.

---

## **Configuration de la zone inversée**

### **Étape 1 : Ajouter la zone inversée dans Bind9**
Dans le fichier `/etc/bind/named.conf.local`, ajoutez les entrées suivantes pour les zones inversées :

```text
zone "22.21.20.in-addr.arpa" {
    type master;
    file "/etc/bind/db.22.21.20";
};

zone "12.11.10.in-addr.arpa" {
    type master;
    file "/etc/bind/db.12.11.10";
};

zone "100.168.192.in-addr.arpa" {
    type master;
    file "/etc/bind/db.100.168.192";
};
```

- **22.21.20.in-addr.arpa** : Réseau 20.21.22.0/24 (réseau interne).
- **12.11.10.in-addr.arpa** : Réseau 10.11.12.0/24 (réseau DMZ).
- **100.168.192.in-addr.arpa** : Réseau 192.168.100.0/24 (réseau backend).

---

### **Étape 2 : Créer les fichiers de zones inversées**

#### **Exemple pour le réseau interne (20.21.22.0/24)** :
Créez le fichier `/etc/bind/db.22.21.20` :
```bash
sudo nano /etc/bind/db.22.21.20
```

Contenu :
```text
$TTL    604800
@       IN      SOA     test.local. root.test.local. (
                      2         ; Serial
                      604800    ; Refresh
                      86400     ; Retry
                      2419200   ; Expire
                      604800 )  ; Negative Cache TTL
;
@       IN      NS      test.local.
10      IN      PTR     mail.test.local.
254     IN      PTR     router.test.local.
```

---

#### **Exemple pour le réseau DMZ (10.11.12.0/24)** :
Créez le fichier `/etc/bind/db.12.11.10` :
```bash
sudo nano /etc/bind/db.12.11.10
```

Contenu :
```text
$TTL    604800
@       IN      SOA     test.local. root.test.local. (
                      2         ; Serial
                      604800    ; Refresh
                      86400     ; Retry
                      2419200   ; Expire
                      604800 )  ; Negative Cache TTL
;
@       IN      NS      test.local.
1       IN      PTR     master.test.local.
2       IN      PTR     backup.test.local.
254     IN      PTR     router.test.local.
```

---

#### **Exemple pour le réseau backend (192.168.100.0/24)** :
Créez le fichier `/etc/bind/db.100.168.192` :
```bash
sudo nano /etc/bind/db.100.168.192
```

Contenu :
```text
$TTL    604800
@       IN      SOA     test.local. root.test.local. (
                      2         ; Serial
                      604800    ; Refresh
                      86400     ; Retry
                      2419200   ; Expire
                      604800 )  ; Negative Cache TTL
;
@       IN      NS      test.local.
1       IN      PTR     master.test.local.
2       IN      PTR     backup.test.local.
10      IN      PTR     noeud1.test.local.
20      IN      PTR     noeud2.test.local.
```

---

### **Étape 3 : Vérifier la configuration et relancer Bind9**

1. **Vérifiez la syntaxe des fichiers de configuration DNS** :
   ```bash
   sudo named-checkconf
   ```
   Vérifiez également chaque fichier de zone :
   ```bash
   sudo named-checkzone 22.21.20.in-addr.arpa /etc/bind/db.22.21.20
   sudo named-checkzone 12.11.10.in-addr.arpa /etc/bind/db.12.11.10
   sudo named-checkzone 100.168.192.in-addr.arpa /etc/bind/db.100.168.192
   ```

2. **Redémarrez Bind9** :
   ```bash
   sudo systemctl restart bind9
   ```

---

### **Étape 4 : Tester les zones inversées**

#### **Depuis une machine cliente** :
Utilisez la commande `dig` ou `nslookup` pour vérifier la résolution inversée :

1. **Tester une IP du réseau DMZ** :
   ```bash
   dig -x 10.11.12.1
   ```

   Résultat attendu :
   ```text
   1.12.11.10.in-addr.arpa. 604800 IN PTR master.test.local.
   ```

2. **Tester une IP du réseau backend** :
   ```bash
   dig -x 192.168.100.10
   ```

   Résultat attendu :
   ```text
   10.100.168.192.in-addr.arpa. 604800 IN PTR noeud1.test.local.
   ```

---

### **Résumé**
- Oui, une zone inversée est nécessaire ici pour un DNS correctement configuré.
- Chaque sous-réseau doit avoir sa propre zone inversée pour permettre une résolution IP → Nom d’hôte.
- Les zones inversées facilitent la gestion et garantissent la compatibilité avec certains services comme Postfix ou les outils de diagnostic réseau.

