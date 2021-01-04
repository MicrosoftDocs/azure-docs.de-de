---
title: Datenverbindungsbibliotheken für Azure Database for MySQL
description: In diesem Artikel werden die einzelnen Bibliotheken bzw. Treiber aufgelistet, mit denen Clientprogramme Verbindungen zu Azure-Datenbank für MySQL herstellen können.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 8/3/2020
ms.openlocfilehash: ac6e5ff2ce775b8ca273ce31a9a35a0e8e37bc07
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94542625"
---
# <a name="connection-libraries-for-azure-database-for-mysql"></a>Datenverbindungsbibliotheken für Azure-Datenbank für MySQL
In diesem Artikel werden die einzelnen Bibliotheken bzw. Treiber aufgelistet, mit denen Clientprogramme Verbindungen zu Azure-Datenbank für MySQL herstellen können.

## <a name="client-interfaces"></a>Clientschnittstellen
MySQL bietet standardmäßige Datenbanktreiberkonnektivität zur Verwendung von MySQL mit Anwendungen und Tools, die mit den Branchenstandards ODBC und JDBC kompatibel sind. Alle Systeme, die mit ODBC oder JDBC kompatibel sind, können MySQL verwenden.

| **Sprache** | **Plattform** | **Zusätzliche Ressourcen** | **Download** |
| :----------- | :------------| :-----------------------| :------------|
| PHP | Windows, Linux | [Systemeigener MySQL-Treiber für PHP – mysqlnd](https://dev.mysql.com/downloads/connector/php-mysqlnd/) | [Download](https://secure.php.net/downloads.php) |
| ODBC | Windows-, Linux-, Mac OS X- und Unix-Plattformen | [MySQL-Connector-/ODBC-Entwicklerhandbuch](https://dev.mysql.com/doc/connector-odbc/en/) | [Download](https://dev.mysql.com/downloads/connector/odbc/) |
| ADO.NET | Windows | [MySQL-Connector-/Net-Entwicklerhandbuch](https://dev.mysql.com/doc/connector-net/en/) | [Download](https://dev.mysql.com/downloads/connector/net/) |
| JDBC | Plattformunabhängig | [MySQL-Connector-/J 5.1-Entwicklerhandbuch](https://dev.mysql.com/doc/connector-j/5.1/en/) | [Download](https://dev.mysql.com/downloads/connector/j/) |
| Node.js | Windows, Linux, Mac OS X | [sidorares/node-mysql2](https://github.com/sidorares/node-mysql2/tree/master/documentation) | [Download](https://github.com/sidorares/node-mysql2) |
| Python | Windows, Linux, Mac OS X | [MySQL-Connector-/Python-Entwicklerhandbuch](https://dev.mysql.com/doc/connector-python/en/) | [Download](https://dev.mysql.com/downloads/connector/python/) |
| C++ | Windows, Linux, Mac OS X | [MySQL-Connector-/C++-Entwicklerhandbuch](https://dev.mysql.com/doc/connector-cpp/en/) | [Download](https://dev.mysql.com/downloads/connector/python/) |
| C | Windows, Linux, Mac OS X | [MySQL-Connector-/C-Entwicklerhandbuch](https://dev.mysql.com/doc/c-api/8.0/en/) | [Download](https://dev.mysql.com/downloads/connector/c/)
| Perl | Windows-, Linux-, Mac OS X- und Unix-Plattformen | [DBD::MySQL](https://metacpan.org/pod/DBD::mysql) | [Download](https://metacpan.org/pod/DBD::mysql) |


## <a name="next-steps"></a>Nächste Schritte
Lesen Sie diese Schnellstarts, in denen beschrieben wird, wie Azure Database for MySQL mit der gewünschten Sprache verbunden und abgefragt wird:

- [PHP](./connect-php.md)
- [Java](./connect-java.md)
- [.NET (C#)](./connect-csharp.md)
- [Python](./connect-python.md)
- [Node.JS](./connect-nodejs.md)
- [Ruby](./connect-ruby.md)
- [C++](connect-cpp.md)
- [Go](./connect-go.md)
