# B1 Linux - TP5

## Partie 1 – Prise en main et sécurisation

### 1. Accès à l'interface

Après avoir tout configuré sur ma VM Ubuntu et pfSense, j'essaie de me connecter à ma VM pfSense par Ubuntu via Firefox :
```https://192.168.56.104```

![image](image/image.png)

1. 192.168.56.104 
2. 10.0.2.15/24
3. Pour chiffrer la communication entre ton navigateur et l'interface d'administration de pfSense. Ça empêche quelqu'un qui snifferait le réseau de voir tes identifiants et tes actions
4. Parce que les identifiants par défaut (admin/pfsense) sont publics et connus de tous

### 2. Sécurisation de l'accès administrateur

Ensuite je change le mot de passe :

![image](image/image2.png)

1. Dans le menu System puis dans User Manager et onglet Users
2. C'est un mot de passe qui est : Long - Complexe - Imprévisible - Unique
3. Parce que le compte admin a tous les pouvoirs sur le pare-feu

## Partie 2 – Prise en main et sécurisation

### 3. Vérification des interfaces

Pour vérifier l'affectation WAN / LAN je vais directement sur le dashboard et je vérifie : 

![image](image/image3.png)

On remarque qu'on a bien :
**WAN** : avec l'IP 10.0.2.15/24 
**LAN** : avec l'IP 192.168.56.104 

## Partie 3 – Configuration des services réseau

### 4. DHCP

Tout d'abord je configure le serveur DHCP pour le réseau LAN : 

![image](image/image4.png)

(je fais bien attention de mettre la plage entre 100 & 199 comme indiqué sur VirtualBox)

1. Les raisons pour lesquelles on utilise un serveur DHCP : Simplicité - Évite les conflits d'IP - Flexibilité - Gestion centralisée - Gain de temps
2. Celles qui sont disponibles soit 192.168.56.100 à 192.168.56.200 (dans notre cas)
3. Les suivantes :
- L'adresse réseau : 192.168.56.0 (identifie le réseau lui-même)
- L'adresse de broadcast : 192.168.56.255 (diffusion à tous)
- L'IP du pfSense/passerelle : 192.168.56.104 (déjà utilisée)
- Les IPs fixes déjà attribuées manuellement à des serveurs/équipements
- Idéalement, toutes les adresses basses (.1 à .99) pour avoir une zone réservée aux IPs statiques

On peut voir que ma VM Ubuntu obtient une IP via mon DHCP : 

![image](image/image5.png)

### 5. DNS

Ensuite j'active le résolveur DNS et le configure : 

![image](image/image6.png)

1. Les raisons sont que le serveur DNS permet de : Centralisation - Cache DNS - Filtrage/Sécurité - Logs centralisés  - Contrôle - Résolution locale
2. C'est car on est connecté à une IP donc à internet donc le ping 8.8.8.8 arrive à passer mais quand il s'agit d'un nom de domaine il faut passer par le DNS

## Partie 4 – Autoriser l'accès Interne

### 6. Règles de pare-feu

Je regarde d'abord les règles de mon pare-feu et j'observe qu'elles sont déjà parfaites par défaut :

![image](image/image7.png)

1. LAN ou 192.168.56.0/24
2. On veut que les machines LAN puissent accéder à n'importe quelle destination sur Internet donc any
3. Oui (protocole any) pour pouvoir accéder à tout 

Pour finir on fait les tests :

1. Ping vers pfSense :
![image](image/image8.png)

2. Ping vers Internet (8.8.8.8) :
![image](image/image9.png)

3. Test DNS :
![image](image/image10.png)

4. Accès web (dans le terminal) :
![image](image/image11.png)

### 7. NAT

Ensuite on va observer que toutes les configs sont bien réglées sur les règles NAT :

![image](image/image12.png)

On remarque que tout est bon.

1. Le NAT pfSense traduit les IP privées du LAN en IP WAN pour que le trafic soit routable sur Internet
2. La différence est la suivante :

**NAT Automatique** : 
- pfSense crée automatiquement toutes les règles NAT nécessaires
- Dès qu'il détecte une interface WAN, il génère les règles pour tous les réseaux LAN

**NAT Manuel** :
- Tu dois créer manuellement chaque règle NAT
- Contrôle total : choix des sources, destinations, ports, traduction

3. Sur pfSense on va sur **Diagnostics** puis sur **States** ensuite on filtre avec l'IP Ubuntu (192.168.56.103) et on voit la traduction en temps réel : 192.168.56.103:port → 10.0.2.15:port

## Partie 5 – Filtrage

### 8. Blocage d'un site spécifique

Tout d'abord je bloque l'accès à un site web en passant par le DNS (facebook.com) :

![image](image/image13.png)

Puis je teste si ça fonctionne bien (sur terminal) : 
![image](image/image14.png)

(sur Firefox) :
![image](image/image15.png)

1. Par nom de domaine car les gros sites changent régulièrement d'IP
2. Le blocage fonctionne quand même. HTTPS chiffre seulement le contenu, pas l'adresse IP de destination
3. VPN/Proxy - IP multiples - IP qui changent - DNS alternatif

### 9. Blocage d'une catégorie de sites (jeux d'argent)

Maintenant on va enlever tout accès aux sites de jeux d'argent à toute personne passant par le firewall :

![image](image/image16.png)

1. On ne le fait pas car : Trop de règles = difficile à gérer et à maintenir - Ralentit le pare-feu - Impossible à lire - Modification compliquée
2. En allant dans **Firewall** puis **Aliases**
3. En passant par **Status** puis dans **System Logs** puis dans **Firewall** et on cherche les lignes rouges "Block"

## Partie 6 – Aller plus loin (partie plus tendue)

### 10. Blocage par catégorie

Comme avant on bloque tout accès aux réseaux sociaux donc je commence par créer mon alias :
![image](image/image17.png)

Ensuite je crée ma règle de blocage :
![image](image/image18.png)

Je finis par observer les logs :
![image](image/image19.png)

1. Elle ne s'applique pas 

### 11. Blocage par catégorie

Tout d'abord on va créer un horaire :

![image](image/image20.png)

Puis régler une règle existante sur cet horaire :

![image](image/image21.png)

1. Pour : Productivité - Gestion de la bande passante - Conformité et politique d'entreprise - Sécurité - Flexibilité - Équilibre vie pro/perso

### 12. Serveur web local

Tout d'abord j'installe un serveur Web sur ma VM Ubuntu (nginx) : 
![image](image/image22.png)

Puis j'autorise l'accès qu'aux spécifiques et le bloque aux autres :
![image](image/image23.png)

1. Oui, c'est ce qu'on vient de faire
2. Oui, on a filtré sur le port 80 (HTTP)
3. Car les menaces peuvent venir aussi en interne : Machines compromises - Employés malveillants - Erreurs humaines - Segmentation de sécurité - Conformité

### 13. Logs et analyse

Maintenant on va activer la journalisation sur certaines règles 
![image](image/image24.png)

1. Un paquet bloqué ne va pas passer par le firewall comme une IP alors qu'un autorisé oui
2. Dans les logs (Status puis System Logs puis Firewall)
3. Format des logs : Source IP:Port vers Destination IP:Port

### 15. Filtrage MAC

Il est impossible de faire un filtrage par adresse MAC 

1. NON, c'est une sécurité très faible car il est facilement contournable
2. L'adresse MAC peut être changée en quelques secondes (ex : MAC Spoofing)

### 16. Portail captif 

On va donc créer le portail captif :
![image](image/image25.png)

1. Wi-Fi public - Réseaux invités - Contrôle d'accès / authentification
2. Les avantages : Authentification des utilisateurs - Acceptation de conditions d'usage - Suivi / journalisation des connexions - Contrôle fin sans gérer les IP manuellement

### 17. Sauvegarde / restauration

Maintenant je vais enregistrer toute la configuration :
![image](image/image26.png)

1. Pour : Prévenir pertes après panne / erreur - Récupération rapide (continuité de service) - Protection contre mauvaise manipulation / mise à jour - Indispensable pour reprise après incident