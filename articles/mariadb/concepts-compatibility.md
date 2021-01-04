---
title: Kompatibilität von Treibern und Tools – Azure Database for MariaDB
description: In diesem Artikel werden die MariaDB-Treiber und -Verwaltungstools beschrieben, die mit Azure Database for MariaDB kompatibel sind.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 49fd2c8da3ba0a44f4901bc6c7a2241e9e20df4d
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94541690"
---
# <a name="mariadb-drivers-and-management-tools-compatible-with-azure-database-for-mariadb"></a>MariaDB-Treiber und -Verwaltungstools, die mit Azure Database for MariaDB kompatibel sind

In diesem Artikel werden die Treiber und Verwaltungstools beschrieben, die mit Azure Database for MariaDB kompatibel sind.

## <a name="mariadb-drivers"></a>MariaDB-Treiber

Azure Database for MariaDB nutzt die Community-Edition von MariaDB Server. Daher ist es mit einer Vielzahl von Programmiersprachen und Treibern kompatibel. Die API und das Protokoll von MariaDB sind kompatibel mit den von MySQL verwendeten. Dies bedeutet, dass Connectors, die mit MySQL funktionieren, auch mit MariaDB funktionieren sollten.

Das Ziel besteht darin, die drei letzten Versionen von MariaDB-Treibern zu unterstützen. Außerdem soll durch einen ständigen Austausch mit den Autoren der Open-Source-Community eine kontinuierliche Verbesserung von Funktionalität und Verwendbarkeit der MariaDB-Treibern erreicht werden. Eine Liste der Treiber, die getestet und als mit Azure Database for MariaDB 10.2 kompatibel eingestuft wurden, finden Sie in der folgenden Tabelle:

**Treiber** | **Links** | **Kompatible Versionen** | **Nicht kompatible Versionen** | **Hinweise**
---|---|---|---|---
PHP | https://secure.php.net/downloads.php | 5.5, 5.6, 7.x | 5.3 | Fügen Sie für PHP-7.0-Verbindungen mit SSL MySQLi das MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT in die Verbindungszeichenfolge ein. <br> ```mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);```<br> Für PDO legen Sie die Option ```PDO::MYSQL_ATTR_SSL_VERIFY_SERVER_CERT```auf FALSE fest.
.NET | [MySqlConnector auf GitHub](https://github.com/mysql-net/MySqlConnector) <br> [Installationspaket von Nuget](https://www.nuget.org/packages/MySqlConnector/) | 0.27 und höher | 0.26.5 und früher |
MySQL-Connector/NET | [MySQL-Connector/NET](https://github.com/mysql/mysql-connector-net) | 8.0, 7.0, 6.10 |  | Bei einigen UTF8-fremden Windows-Systemen tritt unter Umständen ein Verbindungsfehler aufgrund eines Codierungsfehlers auf.
Node.js |  [MySQLjs auf GitHub](https://github.com/mysqljs/mysql/) <br> Installationspaket von NPM:<br> Führen Sie `npm install mysql` von NPM aus. | 2.15 | 2.14.1 und früher
GO | https://github.com/go-sql-driver/mysql/releases | 1.3, 1.4 | 1.2 und früher | Verwenden Sie für Version 1.3 `allowNativePasswords=true` in der Verbindungszeichenfolge. Version 1.4 enthält eine Korrektur, sodass `allowNativePasswords=true` nicht mehr erforderlich ist.
Python | https://pypi.python.org/pypi/mysql-connector-python | 1.2.3, 2.0, 2.1, 2.2 | 1.2.2 und früher |
Java | https://downloads.mariadb.org/connector-java/ | 2.1, 2.0, 1.6 | 1.5.5 und früher |

## <a name="management-tools"></a>Verwaltungstools

Der Kompatibilitätsvorteil erstreckt sich bis in die Datenbank-Verwaltungstools. Ihre vorhandenen Tools sollten auch mit Azure Database for MariaDB funktionieren, solange Änderungen an der Datenbank innerhalb der Grenzen der Benutzerberechtigungen ausgeführt werden. Drei häufig verwendete Datenbank-Verwaltungstools, die getestet und als mit Azure Database for MariaDB 10.2 kompatibel eingestuft wurden, finden Sie in der folgenden Tabelle:

| Aktion | **MySQL Workbench ab Version 6.x** | **Navicat 12** | **PHPMyAdmin ab Version 4.x**
---|---|---|---
Erstellen, Aktualisieren, Lesen, Schreiben, Löschen | X | X | X
SSL-Verbindung | X | X | X
Automatische Vervollständigung von SQL-Abfragen | X | X |
Daten importieren und exportieren | X | X | X
Exportieren in mehreren Formaten | X | X | X
Sichern und Wiederherstellen |  | X |
Anzeigen von Serverparametern | X | X | X
Anzeigen von Clientverbindungen | X | X | X

## <a name="next-steps"></a>Nächste Schritte

- [Beheben von Verbindungsproblemen mit Azure Database for MariaDB](howto-troubleshoot-common-connection-issues.md)