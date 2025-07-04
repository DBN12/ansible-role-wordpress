# WordPress Deploy Role

Ce rôle Ansible automatise le déploiement de WordPress avec MariaDB sur des serveurs Ubuntu et Rocky Linux.

## Description

Ce rôle installe et configure :
- Apache/httpd
- PHP avec les modules nécessaires
- MariaDB
- WordPress
- Configuration sécurisée

## Prérequis

- Ansible >= 2.10
- Privilèges sudo sur les machines cibles
- Connexion SSH configurée

## Plateformes supportées

- Ubuntu 20.04 (Focal)
- Ubuntu 22.04 (Jammy)
- Rocky Linux 8
- Rocky Linux 9
- Debian 11 (Bullseye)
- Debian 12 (Bookworm)

## Variables

### Variables par défaut (defaults/main.yml)

```yaml
# Configuration de la base de données
wordpress_db_name: wordpress
wordpress_db_user: example
wordpress_db_password: examplePW
wordpress_db_host: localhost
mariadb_root_password: examplerootPW

# Configuration Apache
apache_server_admin: admin@localhost
apache_document_root: /var/www/html
wordpress_site_name: wordpress

# Configuration WordPress
wordpress_download_url: https://wordpress.org/latest.zip
wordpress_temp_dir: /tmp

# Permissions
wordpress_owner: www-data
wordpress_group: www-data
wordpress_dir_mode: '755'
wordpress_file_mode: '644'

# Ports
apache_port: 80
```

### Variables importantes à personnaliser

- `mariadb_root_password`: Mot de passe root MariaDB
- `wordpress_db_password`: Mot de passe utilisateur WordPress
- `apache_server_admin`: Email administrateur Apache

## Utilisation

### 1. Installation via Ansible Galaxy

```bash
ansible-galaxy install votre-username.wordpress-deploy
```

### 2. Playbook d'exemple

```yaml
---
- name: Déployer WordPress
  hosts: webservers
  become: yes
  vars:
    mariadb_root_password: "MonMotDePasseSecurise123!"
    wordpress_db_password: "WordPressSecure456!"
    apache_server_admin: "admin@mondomaine.com"
  
  roles:
    - votre-username.wordpress-deploy
```

### 3. Inventaire d'exemple

```ini
[webservers]
serveur1 ansible_host=192.168.1.10
serveur2 ansible_host=192.168.1.11

[webservers:vars]
ansible_user=ubuntu
ansible_ssh_private_key_file=~/.ssh/id_rsa
```

### 4. Exécution

```bash
ansible-playbook -i inventaire.ini playbook.yml
```

## Fonctionnalités

- **Idempotence**: Le rôle peut être exécuté plusieurs fois sans effet de bord
- **Multi-plateforme**: Supporte Ubuntu/Debian et Rocky Linux/CentOS
- **Sécurisé**: Configuration Apache sécurisée, suppression des utilisateurs MySQL anonymes
- **Optimisé**: Compression, expiration des fichiers statiques
- **Handlers**: Redémarrage automatique des services lors de changements de configuration

## Structure du rôle

```
wordpress-deploy/
├── defaults/
│   └── main.yml          # Variables par défaut
├── vars/
│   ├── main.yml          # Variables communes
│   ├── Debian.yml        # Variables spécifiques Debian/Ubuntu
│   └── RedHat.yml        # Variables spécifiques Rocky/CentOS
├── tasks/
│   └── main.yml          # Tâches principales
├── handlers/
│   └── main.yml          # Handlers pour redémarrage services
├── templates/
│   ├── wp-config.php.j2  # Template WordPress config
│   ├── wordpress.conf.j2 # Template Apache Debian/Ubuntu
│   └── wordpress-rocky.conf.j2 # Template Apache Rocky/CentOS
├── meta/
│   └── main.yml          # Métadonnées du rôle
└── README.md             # Documentation
```

## Sécurité

- Suppression des utilisateurs MySQL anonymes
- Suppression de la base de données test
- Protection du fichier wp-config.php
- Désactivation du listing des répertoires
- Protection contre les fichiers cachés

## Tests

### Test avec Docker

```bash
# Ubuntu
docker run -d --name test-ubuntu ubuntu:22.04 /bin/bash -c "while true; do sleep 30; done"

# Rocky Linux
docker run -d --name test-rocky rockylinux:9 /bin/bash -c "while true; do sleep 30; done"

# Exécuter les tests
ansible-playbook -i test-inventory.ini test-playbook.yml
```

### Inventaire de test

```ini
[test-servers]
test-ubuntu ansible_connection=docker
test-rocky ansible_connection=docker
```

## Dépannage

### Problèmes courants

1. **Erreur de connexion MySQL**
   - Vérifiez que MariaDB est démarré
   - Vérifiez les mots de passe configurés

2. **Erreur 403 Apache**
   - Vérifiez les permissions sur `/var/www/html`
   - Vérifiez la configuration du VirtualHost

3. **Module PHP non trouvé**
   - Vérifiez que tous les paquets sont installés
   - Redémarrez Apache après installation

### Logs utiles

```bash
# Logs Apache Ubuntu
tail -f /var/log/apache2/wordpress_error.log

# Logs Apache Rocky
tail -f /var/log/httpd/wordpress_error.log

# Logs MariaDB
tail -f /var/log/mysql/error.log
```

## Contribution

Les contributions sont les bienvenues ! Pour contribuer :

1. Forkez le repository
2. Créez une branche pour votre fonctionnalité
3. Commitez vos changements
4. Poussez vers la branche
5. Créez une Pull Request

## Licence

MIT

## Auteur

VotreNom - votre.email@example.com

## Support

Pour obtenir de l'aide :
- Ouvrez une issue sur GitHub
- Consultez la documentation Ansible
- Vérifiez les logs des services
