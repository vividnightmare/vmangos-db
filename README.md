# Setup

Install SQL client and server:

```
sudo apt install mariadb-client mariadb-server
```

Acquire needed SQL files:

```
git clone https://github.com/vividnightmare/vmangos
git clone https://github.com/vividnightmare/vmangos-db
```

Create tables and user:

```
sudo mariadb -u root
```
```
CREATE DATABASE mangos;
CREATE DATABASE realmd;
CREATE DATABASE characters;
CREATE DATABASE logs;
CREATE USER 'mangos'@'localhost' IDENTIFIED BY 'your_password';
GRANT ALL PRIVILEGES ON mangos.* TO 'mangos'@'localhost';
GRANT ALL PRIVILEGES ON realmd.* TO 'mangos'@'localhost';
GRANT ALL PRIVILEGES ON characters.* TO 'mangos'@'localhost';
GRANT ALL PRIVILEGES ON logs.* TO 'mangos'@'localhost';
FLUSH PRIVILEGES;
exit
```

Apply base SQL schemas:

```
mariadb -u mangos -p mangos < vmangos-db/world_full_14_june_2021.sql
mariadb -u mangos -p realmd < vmangos/sql/logon.sql
mariadb -u mangos -p characters < vmangos/sql/characters.sql
mariadb -u mangos -p logs < vmangos/sql/logs.sql
```

Merge SQL migrations:

```
cd vmangos/sql/migrations
./merge.sh
```

Apply SQL migrations:

```
mariadb -u mangos -p mangos < world_db_updates.sql
mariadb -u mangos -p realmd < logon_db_updates.sql
mariadb -u mangos -p characters < characters_db_updates.sql
mariadb -u mangos -p logs < logs_db_updates.sql
```

Add realm to database:

```
mariadb -u mangos -p realmd
```
```
INSERT INTO realmlist (id, name, address, port, icon, realmflags, timezone, allowedSecurityLevel)
VALUES ('1', 'your_server_name', 'your_server_ip', '8085', '1', '0', '1', '0');
exit
```

After starting mangosd, check for and apply `DBErrorFixes.sql` in your logs directory.
