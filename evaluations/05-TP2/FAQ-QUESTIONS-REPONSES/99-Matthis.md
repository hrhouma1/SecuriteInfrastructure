## **Étapes pour résoudre le problème du ping**

### 1. **Vérifiez les routes et passerelles par défaut**

Les clients et les machines dans le réseau **Backend (192.168.100.0/24)** doivent savoir comment atteindre les autres machines. Voici ce qu’il faut vérifier :

#### a) **Configuration de la passerelle par défaut sur les Nœuds**
- Assurez-vous que les **nœuds (192.168.100.10 et 192.168.100.20)** utilisent la **VGW (192.168.100.99)** comme passerelle par défaut.

Pour vérifier cela sur les nœuds :
```bash
ip route
```
Vous devriez voir une ligne comme :
```text
default via 192.168.100.99 dev ens160
```

Si ce n’est pas configuré :
1. Éditez le fichier de configuration réseau (Netplan sur Ubuntu) :
   ```bash
   sudo nano /etc/netplan/01-netcfg.yaml
   ```
2. Ajoutez la passerelle pour chaque nœud (par exemple, pour **Nœud1**) :
   ```yaml
   network:
     version: 2
     ethernets:
       ens160:
         addresses:
           - 192.168.100.10/24
         gateway4: 192.168.100.99
   ```
3. Appliquez la configuration :
   ```bash
   sudo netplan apply
   ```

#### b) **Vérifiez la configuration de Keepalived pour le VGW**
- La **VGW (192.168.100.99)** doit être active sur l’interface **ens224 (Backend)** du **Master** ou du **Backup**.

Sur le Master ou Backup, utilisez :
```bash
ip addr show ens224
```
Vous devriez voir :
```text
192.168.100.99/24
```
Si ce n’est pas le cas, vérifiez la configuration de Keepalived.

---

### 2. **Assurez-vous que le Routeur relaye correctement les paquets**

#### a) **Activer le routage IP sur le Routeur**
Sur la machine **pfSense (Routeur)** :
1. Activez le routage IP. Dans Linux/Unix, cela se fait avec cette commande :
   ```bash
   sudo sysctl -w net.ipv4.ip_forward=1
   ```
2. Ajoutez-le au fichier de configuration pour que cela persiste après redémarrage :
   ```bash
   echo "net.ipv4.ip_forward=1" | sudo tee -a /etc/sysctl.conf
   ```

#### b) **Vérifiez les règles de pare-feu**
- Si un pare-feu bloque le trafic, les paquets ICMP (utilisés pour le ping) ne passeront pas.
- Vérifiez les règles sur **pfSense** ou tout pare-feu configuré sur vos machines :
  ```bash
  sudo iptables -L
  ```

---

### 3. **Vérifiez les réseaux VMnet dans VMware**

#### a) **Confirmez que toutes les VM connectées au même VMnet communiquent**
- Testez les pings entre machines dans un même VMnet. Par exemple, depuis **Master** (192.168.100.1) vers **Nœud1** (192.168.100.10) :
  ```bash
  ping 192.168.100.10
  ```
  Si cela ne fonctionne pas, il y a peut-être un problème dans la configuration VMware.

#### b) **Assurez-vous que chaque VMnet est correctement configuré**
- Accédez au Virtual Network Editor dans VMware :
  - **VMnet3 (Backend)** : Host-Only, sans DHCP.
  - Assurez-vous que toutes les machines connectées à ce réseau utilisent le même VMnet dans leurs paramètres réseau.

---

### 4. **Diagnostic avec tcpdump**

Sur les machines Master et Backup, utilisez **tcpdump** pour vérifier si les paquets arrivent :
1. Lancez tcpdump sur l’interface du Backend (par exemple, sur le Master) :
   ```bash
   sudo tcpdump -i ens224 icmp
   ```
2. Essayez de pinguer **Nœud1** depuis le client ou une autre machine.
3. Si vous ne voyez aucun paquet, cela signifie que :
   - Les paquets ne sont pas envoyés.
   - Une règle de pare-feu ou une configuration réseau empêche le trafic.

---

### 5. **Testez le basculement Keepalived**

Pour tester si la **VGW (192.168.100.99)** fonctionne correctement :
1. Vérifiez que le **Master** gère la VGW par défaut :
   ```bash
   ip addr show ens224
   ```
2. Simulez une panne du Master en arrêtant Keepalived :
   ```bash
   sudo systemctl stop keepalived
   ```
3. Vérifiez que le **Backup** a pris le relais :
   ```bash
   ip addr show ens224
   ```
   Vous devriez voir **192.168.100.99** attribuée sur le Backup.

---

### **Plan de vérification étape par étape**

| **Étape**                           | **Commande/Action**                                                                 |
|-------------------------------------|------------------------------------------------------------------------------------|
| Vérifiez la passerelle sur Nœud1    | `ip route` ou configurez avec Netplan.                                            |
| Vérifiez la VGW sur Master/Backup   | `ip addr show ens224` pour voir si 192.168.100.99 est attribuée.                  |
| Vérifiez la connectivité entre VM   | `ping` entre les machines du même VMnet.                                          |
| Vérifiez les routes sur le Routeur  | `sudo sysctl net.ipv4.ip_forward=1` pour activer le routage IP.                   |
| Vérifiez les règles du pare-feu     | `sudo iptables -L` ou vérifiez sur pfSense.                                       |
| Capturez le trafic réseau           | `tcpdump -i ens224 icmp` pour vérifier les paquets ICMP (ping) entre les machines.|

---

### **Question **

1. **Pourquoi le ping peut échouer entre le client et les nœuds si la passerelle n’est pas correctement configurée ?**
2. **Expliquez comment Keepalived gère la bascule de la VGW (192.168.100.99) entre le Master et le Backup.**
3. **Quels outils utiliseriez-vous pour diagnostiquer les problèmes de connectivité dans ce TP ?**



- Matthis, si le problème persiste, partage-moi les résultats des tests, et je pourrai vous guider davantage !
