---
title: Erstellen von Datenbanken und Benutzern – Azure Database for MySQL
description: In diesem Artikel wird beschrieben, wie neue Benutzerkonten für die Interaktion mit einem Azure Database for MySQL-Server erstellt werden.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 10/1/2020
ms.openlocfilehash: 2e934ede193d6efb9cc795c6b63cb485b88f792e
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94541418"
---
# <a name="create-databases-and-users-in-azure-database-for-mysql"></a>Erstellen von Datenbanken und Benutzern in Azure Database for MySQL

[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

In diesem Artikel wird beschrieben, wie Benutzer in Azure Database for MySQL erstellt werden.

> [!NOTE]
> **Unvoreingenommene Kommunikation**
>
> Microsoft setzt sich für Diversität und Inklusion ein. In diesem Artikel wird das Wort *Slave* (Sklave) verwendet. Laut [Microsoft-Styleguide für unvoreingenommene Kommunikation](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md) sollte dieses Wort jedoch vermieden werden. Es wird in diesem Artikel aus Konsistenzgründen verwendet, weil es in der Software zurzeit noch enthalten ist. Wenn die Software aktualisiert und um dieses Wort bereinigt wird, wird auch der Artikel entsprechend aktualisiert.
>

Bei der ersten Erstellung Ihrer Azure Database for MySQL-Serverinstanz haben Sie einen Serveradministrator-Benutzernamen und das zugehörige Kennwort angegeben. Weitere Informationen finden Sie in diesem [Schnellstart](quickstart-create-mysql-server-database-using-azure-portal.md). Sie können den Benutzernamen Ihres Serveradministrators im Azure-Portal festlegen.

Der Benutzer „Serveradministrator“ hat folgende Berechtigungen: 

   SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER


Nachdem Sie einen Azure Database for MySQL-Server erstellt haben, können Sie über das erste Serveradministratorkonto weitere Benutzer erstellen und ihnen Administratorzugriff gewähren. Sie können über das Serveradministratorkonto auch weniger privilegierte Benutzern erstellen, die Zugriff auf einzelne Datenbankschemas haben.

> [!NOTE]
> Die Berechtigung „SUPER“ und die Rolle „DBA“ werden nicht unterstützt. Überprüfen Sie die [Berechtigungen](concepts-limits.md#privileges--data-manipulation-support) im Artikel zu den Einschränkungen, um zu verstehen, was im-Dienst nicht unterstützt wird.
>
> Kennwort-Plug-Ins wie `validate_password` und `caching_sha2_password` werden vom Dienst nicht unterstützt.


## <a name="to-create-a-database-with-a-non-admin-user-in-azure-database-for-mysql"></a>So erstellen Sie eine Datenbank mit einem Nicht-Administratorbenutzer in Azure Database for MySQL:

1. Beschaffen Sie die Verbindungsinformationen und den Administratorbenutzernamen.
   Für die Verbindungsherstellung mit Ihrem Datenbankserver benötigen Sie den vollständigen Servernamen und die Administratoranmeldeinformationen. Sie können den Servernamen und die Anmeldeinformationen auf der Seite **Übersicht** des Servers oder auf der Seite **Eigenschaften** im Azure-Portal problemlos finden.

2. Verwenden Sie das Administratorkonto und das Kennwort zum Herstellen einer Verbindung mit Ihrem Datenbankserver. Verwenden Sie Ihr bevorzugtes Clienttool, z. B. „MySQL Workbench“, „mysql.exe“ oder „HeidiSQL“.
   
   Wenn Sie nicht genau wissen, wie Sie die Verbindung herstellen können, lesen Sie [Connect and query data for Single Server](./connect-workbench.md) (Verbinden und Abfragen von Daten für Single Server) bzw. [Connect and query data for Flexible Server](./flexible-server/connect-workbench.md) (Verbinden und Abfragen von Daten für Flexible Server).

3. Bearbeiten Sie den folgenden SQL-Code, und führen Sie ihn aus. Ersetzen Sie den Platzhalterwert `db_user` durch Ihren vorgesehenen neuen Benutzernamen. Ersetzen Sie den Platzhalterwert `testdb` durch Ihren Datenbanknamen.

   Dieser SQL-Code erstellt die neue Datenbank „testdb“. Anschließend erstellt er im MySQL-Dienst einen neuen Benutzer und gewährt ihm alle Berechtigungen für das neue Datenbankschema (testdb.\*).

   ```sql
   CREATE DATABASE testdb;

   CREATE USER 'db_user'@'%' IDENTIFIED BY 'StrongPassword!';

   GRANT ALL PRIVILEGES ON testdb . * TO 'db_user'@'%';

   FLUSH PRIVILEGES;
   ```

4. So überprüfen Sie die gewährten Berechtigungen in der Datenbank:

   ```sql
   USE testdb;

   SHOW GRANTS FOR 'db_user'@'%';
   ```

5. Melden Sie sich am Server an, und geben Sie dabei die gewünschte Datenbank sowie den neuen Benutzernamen und das zugehörige Kennwort an. In diesem Beispiel wird die mysql-Befehlszeile angezeigt. Wenn Sie diesen Befehl verwenden, werden Sie zur Eingabe des Benutzerkennworts aufgefordert. Verwenden Sie Ihren eigenen Servernamen, Datenbanknamen und Benutzernamen.

   # <a name="single-server"></a>[Single Server](#tab/single-server)

   ```azurecli-interactive
   mysql --host mydemoserver.mysql.database.azure.com --database testdb --user db_user@mydemoserver -p
   ```
   # <a name="flexible-server"></a>[Flexible Server](#tab/flexible-server)

   ```azurecli-interactive
   mysql --host mydemoserver.mysql.database.azure.com --database testdb --user db_user -p
   ```
 ---

## <a name="to-create-additional-admin-users-in-azure-database-for-mysql"></a>So erstellen Sie zusätzliche Administratorbenutzer in Azure Database for MySQL:

1. Beschaffen Sie die Verbindungsinformationen und den Administratorbenutzernamen.
   Für die Verbindungsherstellung mit Ihrem Datenbankserver benötigen Sie den vollständigen Servernamen und die Administratoranmeldeinformationen. Sie können den Servernamen und die Anmeldeinformationen auf der Seite **Übersicht** des Servers oder auf der Seite **Eigenschaften** im Azure-Portal problemlos finden.

2. Verwenden Sie das Administratorkonto und das Kennwort zum Herstellen einer Verbindung mit Ihrem Datenbankserver. Verwenden Sie Ihr bevorzugtes Clienttool, z. B. „MySQL Workbench“, „mysql.exe“ oder „HeidiSQL“.
   
   Wenn Sie nicht genau wissen, wie Sie die Verbindung herstellen können, lesen Sie [Verwenden von MySQL Workbench zum Verbinden und Abfragen von Daten](./connect-workbench.md).

3. Bearbeiten Sie den folgenden SQL-Code, und führen Sie ihn aus. Ersetzen Sie den Platzhalterwert `new_master_user` durch Ihren neuen Benutzernamen. Mit dieser Syntax werden die aufgeführten Berechtigungen aller Datenbankschemas ( *.* ) für den Benutzer (in diesem Beispiel: `new_master_user`) gewährt.

   ```sql
   CREATE USER 'new_master_user'@'%' IDENTIFIED BY 'StrongPassword!';

   GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER ON *.* TO 'new_master_user'@'%' WITH GRANT OPTION;

   FLUSH PRIVILEGES;
   ```

4. Überprüfen der gewährten Berechtigungen:

   ```sql
   USE sys;

   SHOW GRANTS FOR 'new_master_user'@'%';
   ```

## <a name="azure_superuser"></a>azure_superuser

Alle Azure Database for MySQL-Server werden mit dem Benutzer „azure_superuser“ erstellt. Dies ist ein von Microsoft erstelltes Systemkonto zum Verwalten des Servers, um Überwachungs-, Sicherungs- und andere regelmäßige Wartungsarbeiten durchzuführen. Bereitschaftstechniker können über dieses Konto auch während eines Vorfalls mit Zertifikatauthentifizierung auf den Server zugreifen und müssen den Zugriff mithilfe von JIT-Prozessen (Just-In-Time) anfordern.

## <a name="next-steps"></a>Nächste Schritte

Öffnen Sie die Firewall für die IP-Adressen der Computer der neuen Benutzer, um ihnen das Herstellen einer Verbindung zu ermöglichen:
- [Erstellen und Verwalten von Firewallregeln für Single Server](howto-manage-firewall-using-portal.md) 
- [Erstellen und Verwalten von Firewallregeln für Flexible Server](flexible-server/how-to-connect-tls-ssl.md)

Weitere Informationen zur Verwaltung von Benutzerkonten finden Sie in der MySQL-Produktdokumentation in den Abschnitten zu den Themen [Benutzerkontenverwaltung](https://dev.mysql.com/doc/refman/5.7/en/access-control.html), [GRANT-Syntax](https://dev.mysql.com/doc/refman/5.7/en/grant.html) und [Berechtigungen](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html).
