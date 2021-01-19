# Stack docker pour multi-projets

![](https://gitlab.liloteam.org/cells/commondockerstack/-/wikis/uploads/1a8feaf42b5cec465d9b2534d4c5f959/lilo_docker_stack.jpeg)

### Prérequis

Installer docker en suivant ce lien https://docs.docker.com/install/linux/docker-ce/ubuntu/
Installer docker-compose en suivant ce lien https://docs.docker.com/compose/install/

### Essentiel
Ce stack à été testé pour ubuntu 16 à version supérieur.  
Un stack docker optimal pour multi-projets php7 et Symfony .  
Commencer par cloner le repo, se placer dans dossier `docker-stack` puis lancer `docker-compose up -d` .   
Tous vos projets sont à placer dans le dossier `symfony` qui sera généré dans le dossier `docker-stack` .  
Faire un `docker exec -it lilo_apps bash` pour entrer dans le container où vous pouvez voir vos projets existants vous pouvez également créer de nouveau projets symfony avec composer par exemple .

### Rajout d'un projet au stack

Si projet existant, le placer dans le dossier `docker-stack/symfony`.
Pour créer un nouveau projet faire `docker-compose up -d` puis `docker exec -it lilo_apps bash` et lancer 
par exemple `composer create-project symfony/website-skeleton my_project_name`.

### Lancer composer et jouer avec bin/console

`docker-compose ps` permet de checker le status de vos container s'il sont up comme suit.
```
lilo_admina   entrypoint.sh docker-php-e ...   Up      0.0.0.0:8081->8080/tcp  
lilo_apache   /bin/sh -c /usr/sbin/apach ...   Up      0.0.0.0:80->80/tcp  
lilo_apps     docker-php-entrypoint apac ...   Up      80/tcp, 0.0.0.0:9000->9000/tcp
lilo_db       docker-entrypoint.sh --def ...   Up      3306/tcp, 33060/tcp
```


après un `docker exec -it lilo_apps bash` on atterit sur un dossier `/var/www/` là resident tous vos projets.Un `cd mon-super-projet` puis `composer install` ensuite si vous voulez `bin/console list` vous dans vôtre environnement PHP7

### Configuration base de donnée
Voici un exemple de conf :


`DATABASE_URL=pgsql://postgres:changeme@db:5432/drops-engine?serverVersion=12&charset=utf8` ainsi pour ce stack votre `database_host` `db`, votre `database_user` est `postgres` et votre `database_user_password` est `changeme`.

### Configuration vhost

#### Prémière action

#### NB: dans ce qui suit remplacer le text `mon-super-projet` par le nom de vôtre projet

Rajouter deux lignes comme suit dans `docker-compose.yml` au service apache :

```
 apache:  
    container_name: lilo_apache
    build: apache-tools
    volumes:
      - ./symfony:/var/www/html
      - ./apache-tools/logs:/var/log/apache2
      - ./apache-tools/vhosts/mon-super-projet.conf:/etc/apache2/sites-available/mon-super-projet.conf:ro
      - ./apache-tools/vhosts/mon-super-projet.conf:/etc/apache2/sites-enabled/mon-super-projet.conf:ro
```

Très exactement les deux lignes à rajouter sont celles ci :

```
- ./apache-tools/vhosts/mon-super-projet.conf:/etc/apache2/sites-available/mon-super-projet.conf:ro
- ./apache-tools/vhosts/mon-super-projet.conf:/etc/apache2/sites-enabled/mon-super-projet.conf:ro
```


Créer dans le dossier `docker-stack/apache-tools/vhosts` le fichier `mon-super-projet.conf` avec le contenu :

```
<VirtualHost *:80>
    ServerName mon-super-projet.local

    DocumentRoot /var/www/html/mon-super-projet/public
    <Directory /var/www/html/mon-super-projet/public>
        AllowOverride All
        Order Allow,Deny
        Allow from All
        FallbackResource /index.php
    </Directory>

    # uncomment the following lines if you install assets as symlinks
    # or run into problems when compiling LESS/Sass/CoffeeScript assets
    # <Directory /var/www/project>
    #     Options FollowSymlinks
    # </Directory>

    ErrorLog /var/log/apache2/mon-super-projet_error.log
    CustomLog /var/log/apache2/mon-super-projet_access.log combined
</VirtualHost>
```


puis redemarrer vos container avec `docker-compose up -d`.
Après rajout de cette ligne `127.0.0.1 mon-super-projet.local` dans le fichier `/etc/hosts` votre app est accecible à l'url `http://mon-super-projet.local`

NB : Il existe dèja des vhosts pour les projets `drops-engine` `search-engine` dans le dossier `docker-stack/apache-tools/vhosts`.Pour ces deux projets rajouter juste la conf dans `docker-compose.yml` conformement à ce README .
 
### Interface graphique pour la DB

Se rendre à l'url http://localhost:8081/ , selectionner PostreSQL et se connecter avec `postgres/changeme`

### Interface graphique pour les mails en local

Se rendre à l'url http://localhost:8025/
