Pour afficher les mots de passe des profils Wi-Fi enregistrés sous Windows en utilisant `netsh`, vous pouvez utiliser la commande suivante :

```bash
netsh wlan show profile name="NOM_DU_RESEAU" key=clear
```

### Explications et options :

- **`netsh wlan show profile`** : Affiche les profils Wi-Fi enregistrés sur l'ordinateur.
- **`name="NOM_DU_RESEAU"`** : Remplacez `"NOM_DU_RESEAU"` par le nom exact du réseau Wi-Fi pour lequel vous voulez voir les détails. Si vous ne connaissez pas le nom, vous pouvez lister tous les profils disponibles avec :
  ```bash
  netsh wlan show profiles
  ```
- **`key=clear`** : Affiche le mot de passe du réseau en texte clair dans la section **Key Content**.

### Exemple :

Pour voir le mot de passe du réseau Wi-Fi nommé `"MonWifi"` :
```bash
netsh wlan show profile name="MonWifi" key=clear
```

Cela affichera les détails de la configuration du réseau `"MonWifi"`, y compris le mot de passe sous "Contenu de la clé" (Key Content) si celui-ci est stocké.
