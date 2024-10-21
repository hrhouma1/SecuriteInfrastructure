# **IPSec - AVANCÉ**

1. Quelle commande permet d'afficher toutes les politiques IPSec existantes sur un système Windows ?
   - A) `netsh ipsec static show filteraction`
   - B) `netsh ipsec static show policy all`
   - C) `netsh ipsec dynamic show policy`
   - D) `netsh ipsec static delete policy`

2. Lors de la création d'une nouvelle action de filtre avec la commande `netsh ipsec static add filteraction`, quel paramètre permet de définir l'exigence de chiffrement ?
   - A) `action=block`
   - B) `action=requireinrequireout`
   - C) `action=allow`
   - D) `action=negotiate`

3. Quel mode IPSec chiffre uniquement les données d'un paquet IP sans affecter l'en-tête ?
   - A) Mode Tunnel
   - B) Mode Transport
   - C) Mode Encapsulation
   - D) Mode Authentification

4. Quelle commande permet de créer une nouvelle liste de filtres pour capturer le trafic HTTP sur le port 80 ?
   - A) `netsh ipsec static add policy`
   - B) `netsh ipsec static add filterlist`
   - C) `netsh ipsec static add rule`
   - D) `netsh ipsec static add filteraction`

5. Quelle méthode de chiffrement est utilisée dans l'exemple de configuration IPSec pour sécuriser le trafic HTTP ?
   - A) DES
   - B) AES
   - C) 3DES
   - D) RSA

6. Comment nomme-t-on une règle IPSec qui associe une politique à une action de filtrage spécifique pour sécuriser le trafic ?
   - A) Filtre de trafic
   - B) Action de filtre
   - C) Règle IPSec
   - D) Politique de sécurité

7. Quelle commande active une politique IPSec afin qu'elle soit appliquée sur le réseau ?
   - A) `netsh ipsec static set policy assign`
   - B) `netsh ipsec static apply policy`
   - C) `netsh ipsec static set filteraction assign`
   - D) `netsh ipsec static activate policy`

8. Dans une configuration IPSec, quel protocole est utilisé pour garantir la confidentialité et l'intégrité des données ?
   - A) TCP
   - B) ICMP
   - C) ESP
   - D) HTTP

9. Lors de la suppression d'une configuration IPSec, quelle commande est utilisée pour supprimer une politique spécifique ?
   - A) `netsh ipsec static delete policy`
   - B) `netsh ipsec static show policy`
   - C) `netsh ipsec static delete filteraction`
   - D) `netsh ipsec static remove policy`

10. En IPSec, quelle différence y a-t-il entre les actions de filtre `negotiate` et `requireinrequireout` ?
    - A) `negotiate` permet la négociation de sécurité, tandis que `requireinrequireout` l'exige pour l'entrée et la sortie.
    - B) `requireinrequireout` permet uniquement le chiffrement des données, `negotiate` ne fait rien.
    - C) `negotiate` bloque le trafic non sécurisé, `requireinrequireout` l'autorise.
    - D) Il n'y a pas de différence entre les deux.

11. Quelle commande permet de vérifier toutes les actions de filtre IPSec configurées ?
    - A) `netsh ipsec static show filteraction`
    - B) `netsh ipsec static show policy all`
    - C) `netsh ipsec static delete filteraction`
    - D) `netsh ipsec dynamic show filteraction`

12. Quel protocole est utilisé pour encapsuler les paquets IP dans le mode Tunnel d'IPSec ?
    - A) SSL
    - B) ESP
    - C) AH (Authentication Header)
    - D) TCP

13. Quelle commande permet d'ajouter un filtre qui capture le trafic HTTP sur le port 80 ?
    - A) `netsh ipsec static add rule`
    - B) `netsh ipsec static add filterlist`
    - C) `netsh ipsec static add filter`
    - D) `netsh ipsec static show policy`

14. Lorsqu'on configure une action de filtre pour l'authentification seule, quel algorithme est couramment utilisé dans IPSec pour vérifier l'intégrité des paquets ?
    - A) MD5
    - B) AES
    - C) RSA
    - D) SHA1

15. Comment appelle-t-on une politique IPSec qui regroupe plusieurs règles de filtrage et d'actions de filtre pour protéger un type spécifique de trafic ?
    - A) Action de filtre
    - B) Filtre de trafic
    - C) Politique de sécurité
    - D) Liste de filtres

16. Pour appliquer une règle IPSec au trafic interne sur le port 8080, quelle commande permet d'ajouter un filtre ciblant ce port ?
    - A) `netsh ipsec static add filterlist`
    - B) `netsh ipsec static add filter`
    - C) `netsh ipsec static add rule`
    - D) `netsh ipsec static show filterlist`

17. Quelle commande permet de supprimer une action de filtre spécifique en IPSec ?
    - A) `netsh ipsec static delete policy`
    - B) `netsh ipsec static delete filteraction`
    - C) `netsh ipsec static show filteraction`
    - D) `netsh ipsec static set filteraction`

18. Comment vérifie-t-on que toutes les politiques IPSec ont bien été supprimées après nettoyage ?
    - A) `netsh ipsec static show policy all`
    - B) `netsh ipsec static delete policy`
    - C) `netsh ipsec static reset policy`
    - D) `netsh ipsec static remove policy`

19. En IPSec, quel algorithme est couramment utilisé avec ESP pour le chiffrement des paquets ?
    - A) AES-128
    - B) 3DES
    - C) SHA256
    - D) MD5

20. Quelle commande permet de vérifier les actions de filtrage configurées et leur état dans le système ?
    - A) `netsh ipsec static show filteraction all`
    - B) `netsh ipsec static set filteraction`
    - C) `netsh ipsec static show policy`
    - D) `netsh ipsec static add filteraction`
