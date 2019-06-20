---
title: Kompatibilität von Azure Database for MySQL-Treibern und -Verwaltungstools
description: In diesem Artikel werden die MySQL-Treiber und -Verwaltungstools beschrieben, die mit Azure Database for MySQL kompatibel sind.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 03/19/2019
ms.openlocfilehash: 05f48145973777052590f8d10e1a2ce1fd22ec7a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60525402"
---
# <a name="mysql-drivers-and-management-tools-compatible-with-azure-database-for-mysql"></a>MySQL-Treiber und -Verwaltungstools, die mit Azure Database for MySQL kompatibel sind
In diesem Artikel werden die Treiber und Verwaltungstools beschrieben, die mit Azure Database for MySQL kompatibel sind.

## <a name="mysql-drivers"></a>MySQL-Treiber
Azure Database for MySQL verwendet die weltweit am häufigsten verwendete Communityedition von MySQL-Datenbank. Daher ist es mit einer Vielzahl von Programmiersprachen und Treibern kompatibel. Das Ziel besteht darin, die drei letzten Versionen von MySQL-Treibern zu unterstützen. Außerdem soll durch einen ständigen Austausch mit den Autoren der Open-Source-Community eine kontinuierliche Verbesserung von Funktionalität und Verwendbarkeit der MySQL-Treibern erreicht werden. Eine Liste der Treiber, die getestet und als mit Azure Database for MySQL 5.6 und 5.7 kompatibel eingestuft wurden, finden Sie in der folgenden Tabelle:

| **Treiber** | **Links** | **Kompatible Versionen** | **Nicht kompatible Versionen** | **Hinweise** |
| :-------- | :------------------------ | :----------- | :---------------------- | :--------------------------------------- |
| PHP | https://secure.php.net/downloads.php | 5.5, 5.6, 7.x | 5.3 | Fügen Sie für PHP-7.0-Verbindungen mit SSL MySQLi das MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT in die Verbindungszeichenfolge ein. <br> ```mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);```<br> Für PDO legen Sie die Option ```PDO::MYSQL_ATTR_SSL_VERIFY_SERVER_CERT```auf FALSE fest.|
| .NET | [MySqlConnector auf GitHub](https://github.com/mysql-net/MySqlConnector) <br> [Installationspaket von Nuget](https://www.nuget.org/packages/MySqlConnector/) | 0.27 und höher | 0.26.5 und früher | |
| MySQL-Connector/NET | [MySQL-Connector/NET](https://github.com/mysql/mysql-connector-net) | 8.0, 7.0, 6.10 |  | Bei einigen UTF8-fremden Windows-Systemen tritt unter Umständen ein Verbindungsfehler aufgrund eines Codierungsfehlers auf. |
| Nodejs |  [MySQLjs auf GitHub](https://github.com/mysqljs/mysql/) <br> Installationspaket von NPM:<br> Führen Sie `npm install mysql` von NPM aus. | 2.15 | 2.14.1 und früher | |
| GO | https://github.com/go-sql-driver/mysql/releases | 1.3, 1.4 | 1.2 und früher | Verwenden Sie für Version 1.3 `allowNativePasswords=true` in der Verbindungszeichenfolge. Version 1.4 enthält eine Korrektur, sodass `allowNativePasswords=true` nicht mehr erforderlich ist. |
| Python | https://pypi.python.org/pypi/mysql-connector-python | 1.2.3, 2.0, 2.1, 2.2 | 1.2.2 und früher | |
| Java | https://downloads.mariadb.org/connector-java/ | 2.1, 2.0, 1.6 | 1.5.5 und früher | |

## <a name="management-tools"></a>Verwaltungstools
Der Kompatibilitätsvorteil erstreckt sich bis in die Datenbank-Verwaltungstools. Ihre vorhandenen Tools sollten auch mit Azure Database for MySQL funktionieren, solange Änderungen an der Datenbank innerhalb der Grenzen der Benutzerberechtigungen ausgeführt werden. Drei häufig verwendete Datenbank-Verwaltungstools, die getestet und als mit Azure Database for MySQL 5.6 und 5.7 kompatibel eingestuft wurden, finden Sie in der folgenden Tabelle:

|                                     | **MySQL Workbench ab Version 6.x** | **Navicat 12** | **PHPMyAdmin ab Version 4.x** |
| :---------------------------------- | :----------------------------- | :------------- | :-------------------------|
| Erstellen, Aktualisieren, Lesen, Schreiben, Löschen | X | X | X |
| SSL-Verbindung | X | X | X |
| Automatische Vervollständigung von SQL-Abfragen | X | X |  |
| Importieren und Exportieren von Daten | X | X | X |
| Exportieren in mehreren Formaten | X | X | X |
| Sichern und Wiederherstellen |  | X |  |
| Anzeigen von Serverparametern | X | X | X |
| Anzeigen von Clientverbindungen | X | X | X |

## <a name="next-steps"></a>Nächste Schritte

- [Beheben von Verbindungsproblemen mit Azure Database for MySQL](howto-troubleshoot-common-connection-issues.md)