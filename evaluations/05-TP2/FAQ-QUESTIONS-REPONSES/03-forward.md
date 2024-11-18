
### **Question  :**
"Lors de la configuration de Bind9 sur une machine avec trois interfaces réseau (NIC), faut-il déclarer les trois interfaces dans la configuration DNS (zones forward et reverse) ? Si oui, comment ? Si non, comment gérer la résolution DNS dans un contexte multi-réseaux ?"

---

### **Réponse : Configurer Bind9 avec plusieurs interfaces réseau (NIC)**

Ce tutoriel vous guide pas à pas pour configurer Bind9 sur une machine avec **3 interfaces réseau** (NIC) dans un contexte multi-réseaux.

---

## **Contexte**
Vous avez une machine avec 3 NIC :
- **NIC1** : Connectée à Internet (via NAT).
- **NIC2** : Réseau interne (IP 20.21.22.254/24).
- **NIC3** : Réseau DMZ (IP 10.11.12.254/24).
- **NIC4** : Réseau backend (IP 192.168.100.254/24).

L'objectif est :
1. **Configurer Bind9 pour gérer les zones DNS** (zones forward et reverse) associées à ces interfaces.
2. **Permettre la résolution DNS (nom ↔ IP) sur chaque réseau.**
3. **Faciliter le routage et la communication entre les réseaux.**

---

## **Étape 1 : Préparer Bind9**

### 1. **Installer Bind9**
Sur votre machine Ubuntu Server ou Debian :
```bash
sudo apt update
sudo apt install bind9 bind9utils
```

### 2. **Configurer Bind9 pour écouter sur plusieurs NIC**
Par défaut, Bind9 écoute sur **toutes les interfaces réseau**. Vous pouvez limiter cela pour les 3 NIC spécifiques. Modifiez le fichier `/etc/bind/named.conf.options` :
```bash
sudo nano /etc/bind/named.conf.options
```

Ajoutez :
```text
options {
    directory "/var/cache/bind";
    
    // Indiquez les interfaces spécifiques
    listen-on { 20.21.22.254; 10.11.12.254; 192.168.100.254; };

    // Désactivez IPv6 si non utilisé
    listen-on-v6 { none; };

    // Ajoutez des forwarders si nécessaire (par exemple, Google DNS)
    forwarders {
        8.8.8.8;
        8.8.4.4;
    };
    forward only;
};
```

Enregistrez et fermez.

### 3. **Redémarrez Bind9**
Appliquez les modifications :
```bash
sudo systemctl restart bind9
```

---

## **Étape 2 : Configurer les zones DNS**

Bind9 doit gérer deux types de zones :
1. **Zones forward (nom → IP).**
2. **Zones reverse (IP → nom).**

### 1. **Déclarez les zones dans `/etc/bind/named.conf.local`**
Ouvrez le fichier :
```bash
sudo nano /etc/bind/named.conf.local
```

Ajoutez les déclarations suivantes :
```text
// Zone forward (nom → IP)
zone "test.local" {
    type master;
    file "/etc/bind/db.test.local";
};

// Zone reverse pour le réseau interne (20.21.22.0/24)
zone "22.21.20.in-addr.arpa" {
    type master;
    file "/etc/bind/db.22.21.20";
};

// Zone reverse pour le réseau DMZ (10.11.12.0/24)
zone "12.11.10.in-addr.arpa" {
    type master;
    file "/etc/bind/db.12.11.10";
};

// Zone reverse pour le réseau backend (192.168.100.0/24)
zone "100.168.192.in-addr.arpa" {
    type master;
    file "/etc/bind/db.100.168.192";
};
```

Enregistrez et fermez.

---

## **Étape 3 : Créer les fichiers de zones**

### 1. **Zone forward : `/etc/bind/db.test.local`**
Créez le fichier pour la zone directe :
```bash
sudo nano /etc/bind/db.test.local
```

Ajoutez :
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
router  IN      A       10.11.12.254
mail    IN      A       20.21.22.10
master  IN      A       10.11.12.1
backup  IN      A       10.11.12.2
noeud1  IN      A       192.168.100.10
noeud2  IN      A       192.168.100.20
```

---

### 2. **Zone reverse : `/etc/bind/db.22.21.20` (réseau interne)**
Créez le fichier :
```bash
sudo nano /etc/bind/db.22.21.20
```

Ajoutez :
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

### 3. **Zone reverse : `/etc/bind/db.12.11.10` (réseau DMZ)**
Créez le fichier :
```bash
sudo nano /etc/bind/db.12.11.10
```

Ajoutez :
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

### 4. **Zone reverse : `/etc/bind/db.100.168.192` (réseau backend)**
Créez le fichier :
```bash
sudo nano /etc/bind/db.100.168.192
```

Ajoutez :
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
10      IN      PTR     noeud1.test.local.
20      IN      PTR     noeud2.test.local.
```

---

## **Étape 4 : Vérifier et redémarrer Bind9**

### 1. **Vérifiez la syntaxe des fichiers**
```bash
sudo named-checkconf
sudo named-checkzone test.local /etc/bind/db.test.local
sudo named-checkzone 22.21.20.in-addr.arpa /etc/bind/db.22.21.20
sudo named-checkzone 12.11.10.in-addr.arpa /etc/bind/db.12.11.10
sudo named-checkzone 100.168.192.in-addr.arpa /etc/bind/db.100.168.192
```

### 2. **Redémarrez Bind9**
```bash
sudo systemctl restart bind9
```

---

## **Étape 5 : Tester la configuration**

### 1. **Test de résolution directe (nom → IP)**
Depuis une autre machine, utilisez :
```bash
dig mail.test.local
```

Résultat attendu :
```text
mail.test.local.      604800  IN      A       20.21.22.10
```

### 2. **Test de résolution inverse (IP → nom)**
Pour vérifier les zones inversées :
```bash
dig -x 20.21.22.10
```

Résultat attendu :
```text
10.22.21.20.in-addr.arpa. 604800 IN PTR mail.test.local.
```

---

### **Résumé**
- **Les zones DNS (forward et reverse) ne dépendent pas directement des NIC**, mais de leurs sous-réseaux.
- Configurez une zone forward pour les noms de domaine et une zone reverse pour chaque sous-réseau correspondant à une NIC.
- Bind9 peut écouter sur toutes les interfaces ou seulement sur certaines, selon vos besoins.
- Testez la résolution avec `dig` pour valider la configuration.


------------
------------
--------------

# Annexe : Déclaration de la zone **forward** dans Bind9


- Dans la déclaration de la zone **forward** dans Bind9, vous ne déclarez pas directement les interfaces réseau (**NIC**) dans le fichier de configuration DNS. 
- Les zones DNS (forward ou reverse) sont définies en fonction des noms de domaine et des adresses IP associées, indépendamment des interfaces.
- Cependant, voici comment gérer cela dans votre cas précis avec 3 NIC sur le routeur :

---

## **1. Gestion des 3 NIC dans Bind9**

### **Zones Forward**
Pour les zones forward (résolution nom → IP), vous déclarez toutes les zones nécessaires, mais cela n'est pas lié aux interfaces réseau. Exemple :
```text
zone "test.local" {
    type master;
    file "/etc/bind/db.test.local";
};
```

Dans le fichier `/etc/bind/db.test.local`, vous listez toutes les correspondances nom → IP pour les trois sous-réseaux :
```text
@       IN      A       10.11.12.254
router  IN      A       10.11.12.254
mail    IN      A       20.21.22.10
master  IN      A       10.11.12.1
backup  IN      A       10.11.12.2
noeud1  IN      A       192.168.100.10
noeud2  IN      A       192.168.100.20
```

---

### **Zones Reverse**
Pour les zones reverse (résolution IP → nom), vous devez configurer une zone inversée par sous-réseau associé à chaque NIC. Exemple :

1. **Réseau Interne (NIC2 : 20.21.22.0/24)** :
   ```text
   zone "22.21.20.in-addr.arpa" {
       type master;
       file "/etc/bind/db.22.21.20";
   };
   ```

2. **Réseau DMZ (NIC3 : 10.11.12.0/24)** :
   ```text
   zone "12.11.10.in-addr.arpa" {
       type master;
       file "/etc/bind/db.12.11.10";
   };
   ```

3. **Réseau Backend (NIC4 : 192.168.100.0/24)** :
   ```text
   zone "100.168.192.in-addr.arpa" {
       type master;
       file "/etc/bind/db.100.168.192";
   };
   ```

Vous créez un fichier pour chaque zone, associant les adresses IP à leurs noms.

---

## **2. Configuration pour l'écoute des interfaces (listen-on)**
Bind9 écoute par défaut sur toutes les interfaces. Si vous souhaitez que Bind9 réponde uniquement sur certaines NIC, vous devez le préciser dans `/etc/bind/named.conf.options`.

Par exemple :
```text
options {
    listen-on { 20.21.22.254; 10.11.12.254; 192.168.100.254; };
    listen-on-v6 { none; }; # Si vous n'utilisez pas IPv6
    directory "/var/cache/bind";

    allow-query { any; }; # Permet à tout le monde de faire des requêtes
};
```

---

## **3. Gestion du Forwarding des Requêtes**
Si vous voulez que Bind9 relaie certaines requêtes DNS non gérées par votre configuration vers un autre DNS (comme 8.8.8.8 de Google), ajoutez une directive `forwarders` :
```text
options {
    forwarders {
        8.8.8.8;
        8.8.4.4;
    };
    forward only;
};
```

---

## **4. Exemple Complet pour Bind9 avec 3 NIC**
Voici une configuration Bind9 qui gère les trois NIC :

### **named.conf.local**
```text
zone "test.local" {
    type master;
    file "/etc/bind/db.test.local";
};

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

### **named.conf.options**
```text
options {
    listen-on { 20.21.22.254; 10.11.12.254; 192.168.100.254; };
    listen-on-v6 { none; };
    directory "/var/cache/bind";

    forwarders {
        8.8.8.8;
        8.8.4.4;
    };
    forward only;
};
```

---

## **Résumé**
- Vous **ne déclarez pas les NIC directement dans les zones forward ou reverse**. Ces zones gèrent uniquement les résolutions DNS (nom ↔ IP).
- Vous configurez Bind9 pour écouter sur toutes les NIC ou sur une sélection spécifique en modifiant `named.conf.options`.
- Configurez une zone inversée pour chaque sous-réseau correspondant à une NIC.
- Si vous configurez une machine comme routeur avec Bind9, vous devez également configurer le routage IP et le NAT pour permettre la communication entre les réseaux.

