## Configuration d'Apache sur Ubuntu

Pour installer Apache sur Ubuntu, suis ces étapes :

### 1. Mettre à jour les paquets
Avant d’installer Apache, mets à jour la liste des paquets :  
```bash
sudo apt update && sudo apt upgrade -y
```

### 2. Installer Apache
Utilise la commande suivante pour installer le serveur Apache :  
```bash
sudo apt install apache2 -y
```

### 3. Vérifier le statut du service Apache
Une fois installé, Apache démarre automatiquement. Vérifie son état avec :  
```bash
sudo systemctl status apache2
```
Si Apache n’est pas actif, démarre-le avec :  
```bash
sudo systemctl start apache2
```

### 4. Activer Apache au démarrage
Pour s’assurer qu’Apache démarre automatiquement au démarrage du système :  
```bash
sudo systemctl enable apache2
```

### 5. Autoriser Apache dans le pare-feu (si activé)
Si `ufw` (Uncomplicated Firewall) est activé, autorise Apache :  
```bash
sudo ufw allow 'Apache'
sudo ufw enable
sudo ufw status
```

### 6. Vérifier l’installation
Ouvre un navigateur et entre l’adresse suivante :  
```
http://localhost
```
Ou, si tu accèdes à distance, utilise l’adresse IP de ton serveur.

Si tout fonctionne bien, tu verras la page par défaut d’Apache.

### 7. Gérer Apache
- **Redémarrer Apache :**  
  ```bash
  sudo systemctl restart apache2
  ```
- **Recharger la configuration sans redémarrer :**  
  ```bash
  sudo systemctl reload apache2
  ```
- **Arrêter Apache :**  
  ```bash
  sudo systemctl stop apache2
  ```

Si tu veux configurer des sites web avec Apache, tu peux créer des fichiers de configuration dans `/etc/apache2/sites-available/` et activer les hôtes virtuels avec `a2ensite`.

---

### 🔹 1. Modifier la configuration principale
Les fichiers de configuration d’Apache se trouvent dans `/etc/apache2/`. Les plus importants sont :
- **Fichier principal** : `/etc/apache2/apache2.conf`
- **Hôtes virtuels** : `/etc/apache2/sites-available/`
- **Modules activés** : `/etc/apache2/mods-enabled/`
- **Ports d’écoute** : `/etc/apache2/ports.conf`

Si tu veux modifier la configuration globale, édite `apache2.conf` :
```bash
sudo nano /etc/apache2/apache2.conf
```
Puis recharge la configuration :
```bash
sudo systemctl reload apache2
```

---

### 🔹 2. Configurer un hôte virtuel (Virtual Host)
Si tu veux héberger plusieurs sites, utilise des **hôtes virtuels**.

1️⃣ **Créer un fichier de configuration pour le site**  
Remplace `monsite.conf` par le nom de ton site :
```bash
sudo nano /etc/apache2/sites-available/monsite.conf
```
Ajoute ce contenu en remplaçant `monsite.com` par ton domaine ou ton IP :
```apache
<VirtualHost *:80>
    ServerAdmin admin@monsite.com
    ServerName monsite.com
    ServerAlias www.monsite.com
    DocumentRoot /var/www/monsite

    <Directory /var/www/monsite>
        AllowOverride All
        Require all granted
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/monsite_error.log
    CustomLog ${APACHE_LOG_DIR}/monsite_access.log combined
</VirtualHost>
```

2️⃣ **Créer le dossier du site et y mettre un index**
```bash
sudo mkdir -p /var/www/monsite
echo "<h1>Bienvenue sur mon site Apache</h1>" | sudo tee /var/www/monsite/index.html
```

3️⃣ **Donner les bons droits**
```bash
sudo chown -R www-data:www-data /var/www/monsite
sudo chmod -R 755 /var/www/monsite
```

4️⃣ **Activer le site et recharger Apache**
```bash
sudo a2ensite monsite.conf
sudo systemctl reload apache2
```

---

### 🔹 3. Activer le module **.htaccess** (mod_rewrite)
Si ton site utilise `.htaccess`, assure-toi que `mod_rewrite` est activé :
```bash
sudo a2enmod rewrite
sudo systemctl restart apache2
```
Puis, modifie ton fichier de configuration (`monsite.conf`) et ajoute :
```apache
<Directory /var/www/monsite>
    AllowOverride All
</Directory>
```
Recharge Apache :
```bash
sudo systemctl reload apache2
```

---

### 🔹 4. Changer le port d'écoute
Si tu veux qu’Apache écoute sur un autre port (ex : 8080), modifie `/etc/apache2/ports.conf` :
```bash
sudo nano /etc/apache2/ports.conf
```
Ajoute ou modifie :
```
Listen 8080
```
Puis, dans ton fichier `monsite.conf`, change :
```apache
<VirtualHost *:8080>
```
Recharge Apache :
```bash
sudo systemctl restart apache2
```

---

### 🔹 5. Sécuriser Apache avec HTTPS (Let's Encrypt)
Si tu veux activer HTTPS gratuitement avec **Let's Encrypt**, installe `certbot` :
```bash
sudo apt install certbot python3-certbot-apache -y
```
Puis génère un certificat SSL :
```bash
sudo certbot --apache
```
Suis les instructions et Let’s Encrypt s’occupera du HTTPS.

Vérifie le renouvellement automatique :
```bash
sudo certbot renew --dry-run
```

---

### 🔹 6. Tester la configuration et redémarrer Apache
Avant de redémarrer, vérifie s’il y a des erreurs :
```bash
apachectl configtest
```
S'il affiche `Syntax OK`, recharge Apache :
```bash
sudo systemctl reload apache2
```

---

## ✅ Conclusion
Tu as maintenant une configuration Apache fonctionnelle avec :
✔ Un site web (`/var/www/monsite`)  
✔ Des **hôtes virtuels** pour plusieurs sites  
✔ **mod_rewrite** activé pour `.htaccess`  
✔ Un **certificat SSL** avec Let's Encrypt (HTTPS)  

Si tu veux encore plus de configuration (proxy, performance, sécurité), dis-moi ! 🚀
