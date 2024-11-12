# Question :

Quelle adresse IP doit-on attribuer à la machine cliente qui va effectuer une requête vers `www.test.local` afin d'assurer une communication efficace avec le cluster NLB ?

# Réponse:

Pour la machine cliente qui va effectuer la requête vers `www.test.local`, il est recommandé d'attribuer une adresse IP sur le même réseau que le VLAN de **gestion** (par exemple, **VLAN 100**) pour permettre la communication avec le cluster NLB.

### Adresse IP de la machine cliente
Supposons que le VLAN 100 utilise la plage d'adresses **192.168.0.0/24**. Voici comment configurer la machine cliente (nommée **WinCLI**) :

- **Adresse IP** : Par exemple, **192.168.0.50**
- **Masque de sous-réseau** : **255.255.255.0** (/24)
- **Passerelle par défaut** : Si vous avez un routeur ou un pare-feu sur le réseau pour l'accès internet, spécifiez son adresse (par exemple, **192.168.0.1**). Sinon, laissez-le vide pour les tests internes.
- **Serveur DNS** : Pointez vers l'adresse IP du **contrôleur de domaine** ou du serveur DNS dans votre domaine (par exemple, **192.168.0.14**), où les enregistrements DNS pour `ClusterWeb.test.local` et `www.test.local` sont configurés.

Cette configuration permet à la machine cliente **WinCLI** de résoudre et d'accéder à `www.test.local`, redirigeant ainsi les requêtes vers le cluster NLB en utilisant l'adresse virtuelle configurée pour la répartition de charge.
