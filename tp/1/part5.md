# Part V : OpenSSH Server

**Le serveur OpenSSH, lui aussi, a une place cruciale dans le niveau de sécurité d'une machine.**

En effet, on parle d'un programme qui tourne en `root` (obligé...), qui écoute sur un port réseau (il est donc attaquable, c'est une porte potentiellement ouverte), et qui en plus, bah sert à prendre le contrôle d'une machine à distance.

Besoin d'un dessin pour expliquer à quel point c'est sensible ? Et nécessaire partout.

## 1. Basics

🌞 **Afficher l'identifiant du processus serveur OpenSSH en cours d'exécution**

- listez tous les programmes en cours d'exécution (avec une commande `ps`)
- mettez en évidence uniquement la ligne qui concerne le serveur SSH (y'en a qu'une)

```bash
ps aux | grep sshd
```

![alt text](image-16.png)

> On peut aussi obtenir l'info avec un `systemctl status` bien senti ;D

🌞 **Changer le port d'écoute du serveur OpenSSH**

- prouvez que votre changement a pris effet
- prouvez que vous pouvez toujours vous connecter à la machine en SSH, sur ce nouveau port
- expliquez pourquoi on considère parfois utile de changer le port d'écoute par défaut du serveur SSH

```bash
sudo sed -i 's/#Port 22/Port 2222/' /etc/ssh/sshd_config
sudo systemctl restart sshd
sudo netstat -tlnp
ssh -p 2222 ah4@localhost
```

## 2. Authentication modes

### A. Key-based authentication

> Un classique ! Vous **devez** être à l'aise avec ça. Jamais trop tard pour s'y mettre.

🌞 **Configurer une authentification par clé**

- vous devez pouvoir vous connecter sur votre utilisateur
- sans saisir de password
- en utilisant une paire de clés

🌞 **Désactiver la connexion par password**

🌞 **Désactiver la connexion en tant que `root`**

## 3. Cert-based authentication

> Moins classique, mais supporté depuis très longtemps par OpenSSH, et très fort en terme de sécurité !

🌞 **Configurer une authentification par certificat**

- j'ai dit par certificat, pas par simple clé
- pareil, faites-le avec votre utilisateur pour les tests

> L'authentification par certificat est toujours plus forte que l'authentification par simple clé : les deux parties (typiquement, le client et le serveur) doivent prouver l'identité à l'autre. De plus, le certificat ne peut pas être falsifié, du moins si on utilise une autorité de certification digne de confiance. L'idée du certificat : on va signer la clé du client avec la clé d'une autorité de certification. Ainsi, la clé n'est plus falsifiable, l'autorité de certification peut attester que c'est la bonne clé pour le bon client.

## 3. Further hardening

🌞 **Proposer au moins 5 configurations supplémentaires qui permettent de renforcer la sécurité du serveur OpenSSH**

> Je vous recommande fooooortement de vous inspirer de ressources d'Internet pour ça. Regardez par exemple le guide de l'ANSSI à ce sujet (obsolète, mais la plupart des principes sont toujours valides), ou encore le guide CIS sur le sujet, ou l'excellent guide Mozilla sur le sujet, . Il existe d'autres ressources de confiance, à votre meilleur moteur de recherches !

## 4. fail2ban

> Un outil extrêmement récurrent dans le monde Linux : un premier rempart contre les attaques de bruteforce.

🌞 **Installer fail2ban sur la machine**

🌞 **Configurer fail2ban**

- en cas de multiples tentatives de connexion échouées sur le serveur SSH, l'utilisateur sera banni
- précisément : après 7 tentatives de connexion échouées en moins de 5 minutes
- c'est l'adresse IP de la personne qui fait des connexions échouées de façon répétée qui est blacklistée

🌞 **Prouvez que fail2ban est effectif**

- faites-vous ban
- montrez l'état de la jail fail2ban pour voir quelles IP sont ban
- levez le ban avec une commande adaptée

