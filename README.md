RO en attente de la fin de migration











































# mysql_mariadb
Tout ce qui touche au SGBDR

# Localisation du fichier de configuration
```sh
mysqld --help | head
```
Sous la section "Default options are read...", il y a une liste de fichiers de configuration, qu'il faudra tester dans l'ordre.

Dans mon cas le premier qui existe est **/etc/mysql/my.cnf**, qui est un lien vers **/etc/mysql/mariadb.cnf** (```readlink -f /etc/mysql/my.cnf```), qui indique qu'il y a une inclusion (**!includedir**) des dossiers **/etc/mysql/conf.d/** et **/etc/mysql/mariadb.conf.d/**, ce dernier contenant, entre autre, le fichier **50-server.cnf** qui contient le GROS de la configuration (port, localisation de la base de données, etc.)
# Export / Import en SQL
```sh
# ATTENTION : ici les commandes sont exécutées depuis l'hôte en root qui n'a pas besoin de s'authentifier (-u root -p)
#  aussi, il y a dépendances : 
#   - `pv` permet de voir la progression (sans, il n'y aura aucun indicateur de progression)
#   - `gzip`/`gunzip` permettent de compresser/décompresser

# export compressé
root@host:~# mysqldump glpidb | gzip > $(date +%Y-%m-%d).glpi.backup.sql.gz

# réinitialisation de la BDD
root@host:~# mysql -e "drop database glpidb ; create database glpidb"

# import de la BDD non compressée
root@host:~# pv 2021-04-29.glpi.backup.sql | mysql glpidb

# import de la BDD compressée
root@host:~# pv 2021-04-29.glpi.backup.sql | gunzip | mysql glpidb

# definition d'une table en vue tabulaire
root@host:~# mysql -e "desc glpidb.glpi_notimportedemails"
+-------------------+--------------+------+-----+---------+----------------+
| Field             | Type         | Null | Key | Default | Extra          |
+-------------------+--------------+------+-----+---------+----------------+
| id                | int(11)      | NO   | PRI | NULL    | auto_increment |
| from              | varchar(255) | NO   |     | NULL    |                |
| to                | varchar(255) | NO   |     | NULL    |                |
| mailcollectors_id | int(11)      | NO   | MUL | 0       |                |
| date              | datetime     | NO   |     | NULL    |                |
| subject           | text         | YES  |     | NULL    |                |
| messageid         | varchar(255) | NO   |     | NULL    |                |
| reason            | int(11)      | NO   |     | 0       |                |
| users_id          | int(11)      | NO   | MUL | 0       |                |
+-------------------+--------------+------+-----+---------+----------------+

# définition d'une table en dump SQL
# ATTENTION : ne PAS importer directement sans les /*! ... */ (commentaires C améliorés)
#  car ce sont des commandes SQL pour MySQL et des commentaires pour les autres SGBDR
#  https://dev.mysql.com/doc/refman/8.0/en/comments.html
root@host:~# mysqldump glpidb glpi_notimportedemails | grep -Ev "^(--|/\*\!|$)"
DROP TABLE IF EXISTS `glpi_notimportedemails`;
CREATE TABLE `glpi_notimportedemails` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `from` varchar(255) NOT NULL,
  `to` varchar(255) NOT NULL,
  `mailcollectors_id` int(11) NOT NULL DEFAULT 0,
  `date` datetime NOT NULL,
  `subject` text DEFAULT NULL,
  `messageid` varchar(255) NOT NULL,
  `reason` int(11) NOT NULL DEFAULT 0,
  `users_id` int(11) NOT NULL DEFAULT 0,
  PRIMARY KEY (`id`),
  KEY `users_id` (`users_id`),
  KEY `mailcollectors_id` (`mailcollectors_id`)
) ENGINE=InnoDB DEFAULT CHARSET=latin1;
LOCK TABLES `glpi_notimportedemails` WRITE;
UNLOCK TABLES;
```
# Erreur
## mysqldump: Got error: 1932 ... doesn't exist in engine" when using LOCK TABLES
Voir [ici](https://www.dba-ninja.com/2020/07/how-to-fix-table-doesnt-exist-in-engine-error-for-mariadb-error-1932.html)
