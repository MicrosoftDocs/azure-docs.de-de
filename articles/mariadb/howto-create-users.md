---
title: Erstellen von Benutzern auf einem Azure Database for MariaDB-Server
description: In diesem Artikel wird beschrieben, wie Sie neue Benutzerkonten für die Interaktion mit einem Azure Database for MariaDB-Server erstellen können.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: ed373cfa0ac755d56e7bc2601c65e0e6482ff6d5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61038870"
---
# <a name="create-users-in-azure-database-for-mariadb"></a>Erstellen von Benutzern in Azure Database for MariaDB 
In diesem Artikel wird beschrieben, wie Sie Benutzer in Azure Database for MariaDB erstellen können.

Bei der anfänglichen Erstellung Ihrer Azure Database for MariaDB-Instanz haben Sie einen Serveradministrator-Benutzernamen und das dazugehörige Kennwort angegeben. Weitere Informationen erhalten Sie in der [Schnellstartanleitung](quickstart-create-mariadb-server-database-using-azure-portal.md). Sie können Ihren Serveradministrator-Benutzernamen im Azure-Portal ermitteln.

Der Serveradministrator erhält folgende Berechtigungen für Ihren Server: SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER

Nach der Erstellung des Azure Database for MariaDB-Servers können Sie das erste Serveradministrator-Benutzerkonto verwenden, um weitere Benutzer zu erstellen und ihnen Administratorzugriff zu gewähren. Außerdem kann das Serveradministratorkonto genutzt werden, um Benutzer mit weniger Berechtigungen zu erstellen, die Zugriff auf einzelne Datenbankschemas haben.

## <a name="create-additional-admin-users"></a>Erstellen von zusätzlichen Administratorbenutzern
1. Beschaffen Sie die Verbindungsinformationen und den Administratorbenutzernamen.
   Für die Verbindungsherstellung mit Ihrem Datenbankserver benötigen Sie den vollständigen Servernamen und die Administratoranmeldeinformationen. Der Servername und die Anmeldeinformationen sind auf der Seite **Übersicht** des Servers oder auf der Seite **Eigenschaften** im Azure-Portal leicht zu finden. 

2. Verwenden Sie das Administratorkonto und das Kennwort zum Herstellen einer Verbindung mit Ihrem Datenbankserver. Nutzen Sie Ihr bevorzugtes Clienttool, z.B. MySQL Workbench, mysql.exe, HeidiSQL oder andere. 
   Wenn Sie unsicher sind, wie Sie die Verbindung herstellen sollen, helfen Ihnen die Informationen unter [Verwenden von MySQL Workbench zum Verbinden und Abfragen von Daten](./connect-workbench.md) weiter.

3. Bearbeiten Sie den folgenden SQL-Code, und führen Sie ihn aus. Fügen Sie Ihren neuen Benutzernamen anstelle des Platzhalterwerts `new_master_user` ein. Mit dieser Syntax werden die aufgeführten Berechtigungen aller Datenbankschemas ( *.* ) für den Benutzernamen (hier: new_master_user) gewährt. 

   ```sql
   CREATE USER 'new_master_user'@'%' IDENTIFIED BY 'StrongPassword!';
   
   GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER ON *.* TO 'new_master_user'@'%' WITH GRANT OPTION; 
   
   FLUSH PRIVILEGES;
   ```

4. Überprüfen der gewährten Berechtigungen 
   ```sql
   USE sys;
   
   SHOW GRANTS FOR 'new_master_user'@'%';
   ```

## <a name="create-database-users"></a>Erstellen von Datenbankbenutzern

1. Beschaffen Sie die Verbindungsinformationen und den Administratorbenutzernamen.
   Für die Verbindungsherstellung mit Ihrem Datenbankserver benötigen Sie den vollständigen Servernamen und die Administratoranmeldeinformationen. Der Servername und die Anmeldeinformationen sind auf der Seite **Übersicht** des Servers oder auf der Seite **Eigenschaften** im Azure-Portal leicht zu finden. 

2. Verwenden Sie das Administratorkonto und das Kennwort zum Herstellen einer Verbindung mit Ihrem Datenbankserver. Nutzen Sie Ihr bevorzugtes Clienttool, z.B. MySQL Workbench, mysql.exe, HeidiSQL oder andere. 
   Wenn Sie unsicher sind, wie Sie die Verbindung herstellen sollen, helfen Ihnen die Informationen unter [Verwenden von MySQL Workbench zum Verbinden und Abfragen von Daten](./connect-workbench.md) weiter.

3. Bearbeiten Sie den folgenden SQL-Code, und führen Sie ihn aus. Ersetzen Sie den Platzhalterwert `db_user` durch den gewünschten neuen Benutzernamen und den Platzhalterwert `testdb` durch Ihren eigenen Datenbanknamen.

   Mit dieser SQL-Codesyntax wird zu Beispielzwecken eine neue Datenbank mit dem Namen testdb erstellt. Anschließend wird im Azure Database for MariaDB-Dienst ein neuer Benutzer erstellt, dem dann alle Berechtigungen für das neue Datenbankschema (testdb.\*) gewährt werden. 

   ```sql
   CREATE DATABASE testdb;
   
   CREATE USER 'db_user'@'%' IDENTIFIED BY 'StrongPassword!';
   
   GRANT ALL PRIVILEGES ON testdb . * TO 'db_user'@'%';
   
   FLUSH PRIVILEGES;
   ```

4. Überprüfen Sie die gewährten Berechtigungen in der Datenbank.
   ```sql
   USE testdb;
   
   SHOW GRANTS FOR 'db_user'@'%';
   ```

5. Melden Sie sich am Server an, und geben Sie die gewünschte Datenbank an, indem Sie den neuen Benutzernamen und das Kennwort verwenden. In diesem Beispiel wird die mysql-Befehlszeile angezeigt. Bei diesem Befehl werden Sie zum Eingeben des Kennworts für den Benutzernamen aufgefordert. Fügen Sie Ihren eigenen Servernamen, Datenbanknamen und Benutzernamen ein.

   ```bash
   mysql --host mydemoserver.mariadb.database.azure.com --database testdb --user db_user@mydemoserver -p
   ```
   Weitere Informationen zur Verwaltung von Benutzerkonten finden Sie in der MariaDB-Dokumentation in den Abschnitten zu den Themen [Benutzerkontenverwaltung](https://mariadb.com/kb/en/library/user-account-management/), [GRANT-Syntax](https://mariadb.com/kb/en/library/grant/) und [Berechtigungen](https://mariadb.com/kb/en/library/grant/#privilege-levels).

## <a name="next-steps"></a>Nächste Schritte
Öffnen Sie die Firewall für die IP-Adressen der Computer der neuen Benutzer, um ihnen das Herstellen einer Verbindung zu ermöglichen: [Erstellen und Verwalten von Firewallregeln für Azure Database for MariaDB mithilfe des Azure-Portals](howto-manage-firewall-portal.md)  

<!--or [Azure CLI](howto-manage-firewall-using-cli.md).-->
