# Cours: Le protocole RADIUS et son fonctionnement en cybersécurité

# Introduction
Le protocole **RADIUS** (Remote Authentication Dial-In User Service) est un protocole essentiel en matière de cybersécurité, permettant d'assurer trois fonctions majeures : **Authentification**, **Autorisation** et **Comptabilisation** (AAA). Ce protocole joue un rôle similaire à celui des abeilles gardiennes qui défendent leur ruche contre les intrus. De même, RADIUS agit comme une première ligne de défense, vérifiant méticuleusement chaque utilisateur cherchant à accéder à un réseau.

# 1. Qu'est-ce que RADIUS ?

RADIUS est un **protocole client-serveur** qui fonctionne au niveau de la couche application du modèle OSI. Il est principalement utilisé pour permettre l'authentification des utilisateurs dans un réseau, et ce, via différents types de dispositifs comme des **VPN**, des **commutateurs**, ou des **points d'accès sans fil**.

# 2. Analogie de la ruche

Pour comprendre le fonctionnement de RADIUS, il est utile de le comparer à une ruche où des abeilles gardiennes contrôlent l'accès à la ruche en identifiant chaque abeille arrivante. De la même manière, RADIUS vérifie l'identité de chaque utilisateur avant de lui accorder l'accès au réseau.

# 3. Fonctionnement du processus d'authentification RADIUS

Le processus d'authentification avec RADIUS suit ces étapes :

1. **L'utilisateur ou le "suppléant"** tente de se connecter à un **client RADIUS**, qui peut être un VPN ou un autre appareil réseau.
   
2. **Le client RADIUS** envoie une **requête d'accès** au **serveur RADIUS**, qui contient les informations d'identification de l'utilisateur (comme un nom d'utilisateur et un mot de passe) ainsi qu'un **secret partagé**.

3. **Le serveur RADIUS** déchiffre ce message et compare les informations reçues avec celles stockées dans sa base de données d'utilisateurs autorisés. Si le client ou les informations d'identification ne correspondent pas, la requête est rejetée.

4. Si les informations sont correctes, le serveur vérifie ensuite si une **politique d'accès** est applicable à cet utilisateur (par exemple, des restrictions sur les horaires d'accès).

5. Si tout est conforme, un **message d'acceptation** est envoyé au client RADIUS, autorisant l'utilisateur à accéder au réseau.

6. En cas de non-conformité avec les politiques d'accès, un **message de rejet** est renvoyé, et l'utilisateur est bloqué.

# 4. Comptabilisation RADIUS

En plus de l'authentification et de l'autorisation, **RADIUS** permet également la **comptabilisation** (ou audit). Cela signifie que chaque session utilisateur est surveillée pour collecter des informations telles que la durée de la connexion, la quantité de données utilisées, etc. Voici les étapes du processus de comptabilisation :

- **Début de session** : Lorsqu'un utilisateur obtient l'accès, le client RADIUS envoie un paquet "début de session" au serveur, contenant des informations comme l'ID de l'utilisateur et l'adresse réseau.
  
- **Mises à jour de session** : Pendant la session, des paquets de mise à jour peuvent être envoyés pour prolonger l'accès ou pour surveiller l'utilisation des ressources.
  
- **Fin de session** : Lorsque la session se termine, un paquet "fin de session" est envoyé avec des informations telles que la durée totale, les données transférées, et la raison de la déconnexion.

# 5. RADIUS : Un système AAA

RADIUS est un outil complet pour la gestion des réseaux, offrant un système AAA :
- **Authentification** : Vérification de l'identité des utilisateurs.
- **Autorisation** : Contrôle des permissions d'accès basées sur des politiques définies.
- **Comptabilisation** : Suivi des activités des utilisateurs pour des raisons de sécurité et de facturation.

# Conclusion

Le protocole RADIUS est un élément clé pour assurer la sécurité des réseaux dans des environnements complexes. Il permet de garantir que seuls les utilisateurs autorisés peuvent accéder à des ressources spécifiques, et il offre une surveillance continue des sessions utilisateur. Grâce à ses fonctionnalités de sécurité robustes et à sa capacité d'intégration avec des systèmes existants, RADIUS est un choix privilégié pour de nombreuses organisations cherchant à renforcer la protection de leurs informations sensibles.

# Questions de Réflexion
1. Quelle analogie peut-on faire entre le fonctionnement de RADIUS et les abeilles gardiennes d'une ruche ?
2. Pourquoi est-il important de vérifier à la fois l'authentification et les politiques d'accès ?
3. Quelles sont les informations collectées lors du processus de comptabilisation avec RADIUS ?
4. Quelles sont les trois fonctions principales de RADIUS dans la gestion de la sécurité réseau ?


# Références : 
- https://www.youtube.com/watch?v=LLrb3em-_po&ab_channel=TheInfosecAcademy
