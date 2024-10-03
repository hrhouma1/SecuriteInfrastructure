### Schéma : Attaque Man-in-the-Middle (MITM) sans tunneling (non sécurisé)

Dans cette situation, il n'y a pas de tunnel sécurisé. L'attaquant peut intercepter les données échangées entre l'utilisateur et le serveur.

```plaintext
Utilisateur (Client)                Attaquant (MITM)                 Serveur
      |                                  |                               |
      |-------- Données non chiffrées --->|                               |
      |                                  |                               |
      |<-------- Données non chiffrées ---|                               |
      |                                  |                               |
      |                                  |                               |
      |         Interception             |                               |
      |----->(l'attaquant voit les données)                              |
      |                                  |                               |
      |                                  |                               |
      |        Modifications             |                               |
      |<-----(l'attaquant modifie les données)                          |
      |                                  |                               |
```

#### Explication de l'attaque :
1. **Utilisateur (Client)** : L'utilisateur envoie des données (par exemple, un mot de passe) à un serveur sans utiliser de tunnel sécurisé (VPN).
2. **Attaquant (MITM)** : L'attaquant intercepte ces données car elles ne sont pas chiffrées. Il peut alors lire, copier, ou modifier les informations avant de les renvoyer au serveur ou au client.
3. **Serveur** : Le serveur reçoit les données, qui peuvent avoir été modifiées par l'attaquant.

### Schéma ASCII : Attaque MITM protégée par un tunnel VPN (sécurisé)

Dans ce schéma, les données sont protégées par un **tunnel VPN chiffré**. L'attaquant peut toujours intercepter le trafic, mais il ne peut pas le lire ni le modifier grâce au chiffrement.

```plaintext
Utilisateur (Client VPN)                Attaquant (MITM)                 Serveur
      |                                  |                               |
      |-------- Tunnel sécurisé (chiffré) -->|                               |
      |                                  |                               |
      |<-------- Tunnel sécurisé (chiffré) ---|                               |
      |                                  |                               |
      |        Données interceptées       |                               |
      |----->(l'attaquant voit des données)                               |
      |      (mais ne peut pas les lire)  |                               |
      |                                  |                               |
      |       Impossible de modifier      |                               |
      |<-----(car les données sont chiffrées)                             |
      |                                  |                               |
```

#### Explication avec un tunnel VPN :
1. **Utilisateur (Client VPN)** : L'utilisateur utilise un VPN, ce qui signifie que toutes ses données sont envoyées à travers un **tunnel chiffré**. Même si un attaquant intercepte les données, il ne peut pas les lire.
   
2. **Attaquant (MITM)** : L'attaquant intercepte le trafic, mais ne peut pas lire ou modifier les données car elles sont protégées par le chiffrement du VPN.
   
3. **Serveur** : Le serveur reçoit les données d'origine via le tunnel sécurisé, sans avoir été altérées par un attaquant.

### Comparaison entre les deux scénarios :
- **Sans tunnel (non sécurisé)** : L'attaquant peut intercepter, lire, et modifier les données. Cela rend l'utilisateur vulnérable aux attaques de type MITM.
- **Avec tunnel (VPN sécurisé)** : Le tunnel VPN chiffre les données, les rendant illisibles pour l'attaquant. Même s'il intercepte les paquets de données, il ne pourra ni les lire ni les modifier.

### Points importants:
1. **Man-in-the-Middle (MITM)** : C'est une attaque où un attaquant se place entre deux parties (l'utilisateur et le serveur) pour intercepter ou modifier des communications non sécurisées.
   
2. **Tunneling avec VPN** : Le tunnel VPN **chiffre** les données, empêchant l'attaquant de les lire ou de les modifier. C'est une méthode efficace pour contrer les attaques MITM.

3. **Chiffrement** : Grâce au chiffrement, même si un attaquant capture les données qui transitent par le tunnel VPN, il ne peut pas les exploiter.

---

Ce schéma  montre clairement l'importance du tunneling dans la protection des données contre des attaques telles que **Man-in-the-Middle**. 
