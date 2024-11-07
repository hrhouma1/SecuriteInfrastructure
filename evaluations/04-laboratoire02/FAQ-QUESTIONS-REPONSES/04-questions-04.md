# NLB vs SLB ?

## ==> points clés concernant NLB (Network Load Balancing) et SLB (Software Load Balancing) sur Windows Server 2019 :

1. NLB est toujours supporté sur Windows Server 2019 pour les déploiements non-SDN (Software-Defined Networking)[3].

2. Cependant, Windows Server 2016 et les versions ultérieures incluent une nouvelle fonctionnalité appelée Software Load Balancer (SLB) qui fait partie de l'infrastructure SDN[3].

3. Microsoft recommande d'utiliser SLB plutôt que NLB dans les cas suivants[3] :
   - Si vous utilisez SDN
   - Si vous avez des charges de travail non-Windows
   - Si vous avez besoin de NAT (Network Address Translation) sortant
   - Si vous avez besoin d'équilibrage de charge de couche 3 (L3) ou non basé sur TCP

4. NLB reste une option viable pour les déploiements non-SDN sur Windows Server 2019[3].

5. NLB est une fonctionnalité intégrée de Windows Server 2019 et 2022, qui permet de distribuer le trafic réseau sur plusieurs serveurs sans matériel supplémentaire[5].

En résumé, NLB est toujours supporté sur Windows Server 2019, mais Microsoft pousse vers l'utilisation de SLB pour les nouveaux déploiements, en particulier ceux utilisant SDN. Le choix entre NLB et SLB dépendra donc de votre infrastructure spécifique et de vos besoins en matière d'équilibrage de charge.

# Citations:

[1] https://msftwebcast.com/2020/02/configure-network-load-balancing-in-windows-server-2019.html

[2] https://resonatenetworks.com/2022/09/14/how-can-i-load-balance-windows-server-2019/

[3] https://learn.microsoft.com/en-us/windows-server/networking/technologies/network-load-balancing

[4] https://www.a10networks.com/glossary/what-is-server-load-balancing-slb/

[5] https://www.admin-magazine.com/Archive/2024/81/Network-load-balancing-on-Windows-Server
