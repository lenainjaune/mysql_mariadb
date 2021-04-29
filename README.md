# mysql_mariadb
Tout ce qui touche au SGBDR

# Localisation du fichier de configuration
```sh
mysqld --help | head
```
Sous la section "Default options are read...", il y a une liste de fichiers de configuration, qu'il faudra tester dans l'ordre.

Dans mon cas le premier qui existe est **/etc/mysql/my.cnf**, qui est un lien vers **/etc/mysql/mariadb.cnf** (```readlink -f /etc/mysql/my.cnf```), qui indique qu'il y a une inclusion (**!includedir**) des dossiers **/etc/mysql/conf.d/** et **/etc/mysql/mariadb.conf.d/**, ce dernier contenant entre autre, le fichier **50-server.cnf** qui contient le GROS de la configuration (port, localisation de la base de données, etc.)

# Erreur
## mysqldump: Got error: 1932 ... doesn't exist in engine" when using LOCK TABLES
Voir [ici](https://www.dba-ninja.com/2020/07/how-to-fix-table-doesnt-exist-in-engine-error-for-mariadb-error-1932.html)
