---
title: Kompatibilität von Treibern und Tools – Azure Database for MySQL
description: In diesem Artikel werden die MySQL-Treiber und -Verwaltungstools beschrieben, die mit Azure Database for MySQL kompatibel sind.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: c2faf8e8b6a814989fa9e1693fddeedb525df13a
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "100006852"
---
# <a name="mysql-drivers-and-management-tools-compatible-with-azure-database-for-mysql"></a>MySQL-Treiber und -Verwaltungstools, die mit Azure Database for MySQL kompatibel sind
In diesem Artikel werden die Treiber und Verwaltungstools beschrieben, die mit Azure Database for MySQL kompatibel sind.

## <a name="mysql-drivers"></a>MySQL-Treiber
Azure Database for MySQL verwendet die weltweit am häufigsten verwendete Communityedition von MySQL-Datenbank. Daher ist es mit einer Vielzahl von Programmiersprachen und Treibern kompatibel. Das Ziel besteht darin, die drei letzten Versionen von MySQL-Treibern zu unterstützen. Außerdem soll durch einen ständigen Austausch mit den Autoren der Open-Source-Community eine kontinuierliche Verbesserung von Funktionalität und Verwendbarkeit der MySQL-Treibern erreicht werden. Eine Liste der Treiber, die getestet und als mit Azure Database for MySQL 5.6 und 5.7 kompatibel eingestuft wurden, finden Sie in der folgenden Tabelle:

| **Programmiersprache** | **Treiber** | **Links** | **Kompatible Versionen** | **Nicht kompatible Versionen** | **Hinweise** |
| :----------------------- | :--------- | :-------- | :---------------------- | :------------------------ | :-------- |
| PHP | mysqli, pdo_mysql, mysqlnd | https://secure.php.net/downloads.php | 5.5, 5.6, 7.x | 5.3 | Fügen Sie für PHP-7.0-Verbindungen mit SSL MySQLi das MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT in die Verbindungszeichenfolge ein. <br> ```mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);```<br> Für PDO legen Sie die Option ```PDO::MYSQL_ATTR_SSL_VERIFY_SERVER_CERT```auf FALSE fest.|
| .NET | Asynchroner MySQL-Connector für .NET | https://github.com/mysql-net/MySqlConnector <br> [Installationspaket von Nuget](https://www.nuget.org/packages/MySqlConnector/) | 0.27 und höher | 0.26.5 und früher | |
| .NET | MySQL-Connector/NET | https://github.com/mysql/mysql-connector-net | 6.6.3 ,7.0 ,8.0 |  | Bei einigen UTF8-fremden Windows-Systemen tritt unter Umständen ein Verbindungsfehler aufgrund eines Codierungsfehlers auf. |
| Node.js | mysqljs | https://github.com/mysqljs/mysql/ <br> Installationspaket von NPM:<br> Führen Sie `npm install mysql` von NPM aus. | 2.15 | 2.14.1 und früher | |
| Node.js | node-mysql2 | https://github.com/sidorares/node-mysql2 | 1.3.4+ | | |
| Go | Go MySQL-Treiber | https://github.com/go-sql-driver/mysql/releases | 1.3, 1.4 | 1.2 und früher | Verwenden Sie für Version 1.3 `allowNativePasswords=true` in der Verbindungszeichenfolge. Version 1.4 enthält eine Korrektur, sodass `allowNativePasswords=true` nicht mehr erforderlich ist. |
| Python | MySQL-Connector/Python | https://pypi.python.org/pypi/mysql-connector-python | 1.2.3, 2.0, 2.1, 2.2 verwenden 8.0.16+ mit MySQL 8.0  | 1.2.2 und früher | |
| Python | PyMySQL | https://pypi.org/project/PyMySQL/ | 0.7.11, 0.8.0, 0.8.1, 0.9.3+ | 0.9.0-0.9.2 (Regression in web2py) | |
| Java | MariaDB-Connector/J | https://downloads.mariadb.org/connector-java/ | 2.1, 2.0, 1.6 | 1.5.5 und früher | | 
| Java | MySQL-Connector/J | https://github.com/mysql/mysql-connector-j | 5.1.21+ verwendet 8.0.17+ mit MySQL 8.0 | 5.1.20 und früher | |
| C | MySQL-Connector/C (libmysqlclient) | https://dev.mysql.com/doc/refman/5.7/en/c-api-implementations.html | 6.0.2+ | | |
| C | MySQL-Connector/ODBC (myodbc) | https://github.com/mysql/mysql-connector-odbc | 3.51.29+ | | |
| C++ | MySQL-Connector/C++ | https://github.com/mysql/mysql-connector-cpp | 1.1.9+ | 1.1.3 und früher | | 
| C++ | MySQL++| https://github.com/tangentsoft/mysqlpp | 3.2.3+ | | |
| Ruby | mysql2 | https://github.com/brianmario/mysql2 | 0.4.10+ | | |
| R | RMySQL | https://github.com/rstats-db/RMySQL | 0.10.16+ | | |
| Swift | mysql-swift | https://github.com/novi/mysql-swift | 0.7.2+ | | |
| Swift | vapor/mysql | https://github.com/vapor/mysql-kit | 2.0.1+ | | |

## <a name="management-tools"></a>Verwaltungstools
Der Kompatibilitätsvorteil erstreckt sich bis in die Datenbank-Verwaltungstools. Ihre vorhandenen Tools sollten auch mit Azure Database for MySQL funktionieren, solange Änderungen an der Datenbank innerhalb der Grenzen der Benutzerberechtigungen ausgeführt werden. Drei häufig verwendete Datenbank-Verwaltungstools, die getestet und als mit Azure Database for MySQL 5.6 und 5.7 kompatibel eingestuft wurden, finden Sie in der folgenden Tabelle:

|                                     | **MySQL Workbench ab Version 6.x** | **Navicat 12** | **PHPMyAdmin ab Version 4.x** | **dbForge Studio for MySQL 9.0** |
| :---------------------------------- | :----------------------------- | :------------- | :-------------------------| :------------------------------- |
| **Erstellen, Aktualisieren, Lesen, Schreiben, Löschen** | X | X | X | X |
| **SSL-Verbindung** | X | X | X | X |
| **Automatische Vervollständigung von SQL-Abfragen** | X | X |  | X |
| **Importieren und Exportieren von Daten** | X | X | X | X |
| **Exportieren in mehreren Formaten** | X | X | X | X |
| **Sichern und Wiederherstellen** |  | X |  | X |
| **Anzeigen von Serverparametern** | X | X | X | X |
| **Anzeigen von Clientverbindungen** | X | X | X | X |

## <a name="next-steps"></a>Nächste Schritte

- [Beheben von Verbindungsproblemen mit Azure Database for MySQL](howto-troubleshoot-common-connection-issues.md)
