# mysql_mariadb
Tout ce qui touche au SGBDR

# Localisation du fichier de configuration
```sh
mysqld --help --verbose | head
```
Sous la section "Default options are read", il y a une liste de fichier de configuration, qu'il faudra tester dans l'ordre

# Erreur
## mysqldump: Got error: 1932 ... doesn't exist in engine" when using LOCK TABLES
Voir [ici](https://www.dba-ninja.com/2020/07/how-to-fix-table-doesnt-exist-in-engine-error-for-mariadb-error-1932.html)
