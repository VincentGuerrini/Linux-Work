# B1 Linux - TP2

## Partie 1 - Exploration locale

### 1 - Affichage d'informations sur la pile TCP/IP locale

*>>> ipconfig /all*

![alt text](image.png)

**NOM** : MediaTek Wi-Fi 7 MT7925 Wireless LAN Card

**ADRESSE MAC** : 48-45-E6-8C-67-67

**ADRESSE IP** : 10.33.68.42

**ADRESSE RÉSEAU** : 10.33.68./20

**ADRESSE BROADCAST** : 10.33.68.255

![alt text](image/image-1.png)

**NOM** : Realtek PCIe GbE Family Controller

**ADRESSE MAC** : 18-3D-2D-EF-2A-0F

**ADRESSE IP** : Aucune (pas connecté)

La commande pour connaître l'adresse IP de la passerelle de votre carte WiFi est "*ipconfig /all*" dans un cmd par exemple

Pour obtenir les informations en passant par des méthodes graphiques soit GUI on doit : 

Aller dans **Paramètres** puis **Réseau et Internet** :
![alt text](image/image-2.png)

Ensuite aller dans **Propriétés du Matériel** :
![alt text](image/image-3.png)

Le Gateway permet de relier deux réseaux informatiques de types **différents**, donc à Ingésup elle permet de relier le réseau **interne** au réseau **externe**.

### 2 - Modifications des informations

#### A - Modification d'adresse IP - pt. 1

Dans mon réseau actuel il existe **4096** adresses car il s'agit d'un **/20**. Mais seulement **4094** sont disponibles donc de la **10.33.68.1** à la **10.33.68.4094**.

Je peux changer mon IP manuellement en allant encore dans **Paramètres** puis **Réseau et Internet** :
![alt text](image/image-4.png)

---

#### B - nmap

![alt text](image/image-5.png)

*>>> nmap -sn -PE 10.33.68.0/20*
![alt text](image/image-6.png)

*>>> nmap -sL -PE 10.33.68.0/20*
![alt text](image/image-7.png)

---

#### C - Modification d'adresse IP - pt. 2

Avec la commande *"nmap -sn 10.33.68.0/20"* j'ai pu obtenir une adresse IP libre : **"10.33.68.85"** et avoir pu la modifier comme précédemment 

Après avoir modifié le Gateway je n'ai plus accès à internet car il doit sûrement être incorrect 

---

## Partie 3 - Manipulations d'autres outils/protocoles côté client

### 1 - DHCP

![alt text](image/image-8.png)
**Adresse du DHCP** : 10.33.79.254

**le bail expire** : samedi 24 janvier 2026 01:20:58

La commande "*ipconfig /renew*" me permet de demander au DHCP une nouvelle IP :
![alt text](image/image-9.png)

### 2 - DNS

![alt text](image/image-8.png)
**Adresse DNS** : 8.8.8.8

la commande "*nslookup*" me donne :
![alt text](image/image-10.png)

***nslookup google.com***
![alt text](image/image-11.png)

***nslookup ynov.com***
![alt text](image/image-12.png)

***nslookup 78.78.21.21***
![alt text](image/image-13.png)

***nslookup 92.16.54.88***
![alt text](image/image-14.png)