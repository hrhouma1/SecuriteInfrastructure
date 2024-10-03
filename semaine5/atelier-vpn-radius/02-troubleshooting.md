
## Installation du client VPN

1. Rendez-vous sur le site officiel d'OpenVPN : https://openvpn.net/client-connect-vpn-for-windows/[4].

2. Téléchargez la version appropriée pour votre système (32 ou 64 bits).

3. Une fois le téléchargement terminé, exécutez le fichier d'installation.

4. Suivez l'assistant d'installation en acceptant les conditions d'utilisation et en autorisant les demandes d'élévation de privilèges[1].

5. Une fois l'installation terminée, vous verrez l'icône OpenVPN dans la barre des tâches.

## Exportation de la configuration VPN depuis pfSense

1. Connectez-vous à l'interface web de pfSense.

2. Allez dans VPN -> OpenVPN -> Client Export.

3. Trouvez la configuration que vous souhaitez exporter et cliquez sur le bouton de téléchargement correspondant au fichier .ovpn pour Windows.

4. Enregistrez ce fichier sur votre machine Windows 10.

## Configuration d'OpenVPN sur Windows 10

1. Cliquez sur l'icône OpenVPN dans la barre des tâches pour ouvrir l'interface.

2. Cliquez sur le bouton "+" ou "Ajouter un profil" pour importer une nouvelle configuration[4].

3. Choisissez "Importer depuis un fichier" et sélectionnez le fichier .ovpn que vous avez téléchargé depuis pfSense[4].

4. Donnez un nom à votre connexion VPN si nécessaire.

5. Cliquez sur "Ajouter" pour terminer l'importation du profil[4].

6. Pour vous connecter, cliquez sur le profil que vous venez d'ajouter.

7. Entrez les identifiants d'un utilisateur appartenant au groupe VPNUsers d'Active Directory lorsque vous y êtes invité.

8. Cliquez sur "Connecter" pour établir la connexion VPN[4].

Assurez-vous que l'utilisateur que vous utilisez pour vous connecter appartient bien au groupe VPNUsers dans Active Directory, comme configuré précédemment dans les étapes de configuration de Windows Server et pfSense.

Citations:

[1] https://openvpn.net/client/client-connect-vpn-for-windows/

[2] https://openvpn.net/connect-docs/installation-guide-windows.html

[3] https://openvpn.net/connect-docs/connect-for-windows.html

[4] https://openvpn.net/client/client-connect-vpn-for-windows/
