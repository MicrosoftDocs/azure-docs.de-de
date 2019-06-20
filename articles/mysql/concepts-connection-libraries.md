---
title: Datenverbindungsbibliotheken für Azure-Datenbank für MySQL
description: In diesem Artikel werden die einzelnen Bibliotheken bzw. Treiber aufgelistet, mit denen Clientprogramme Verbindungen zu Azure-Datenbank für MySQL herstellen können.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: 6ce0f2c761ede7d326f52f4d93d7f1b0bfa98cb2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60525560"
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
| C | Windows, Linux, Mac OS X | [MySQL-Connector-/C-Entwicklerhandbuch](https://dev.mysql.com/doc/connector-c/en/) | [Download](https://dev.mysql.com/downloads/connector/c/)
| Perl | Windows-, Linux-, Mac OS X- und Unix-Plattformen | [DBD::MySQL](https://metacpan.org/pod/DBD::mysql) | [Download](https://metacpan.org/pod/DBD::mysql) |


## <a name="next-steps"></a>Nächste Schritte
Lesen Sie diese Schnellstarts, in denen beschrieben wird, wie Azure Database for MySQL mit der gewünschten Sprache verbunden und abgefragt wird:

[PHP](./connect-php.md) | [Java](./connect-java.md) |  [.NET (C#)](./connect-csharp.md) | [Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Ruby](./connect-ruby.md) | [C++](connect-cpp.md) | [Go](./connect-go.md)

