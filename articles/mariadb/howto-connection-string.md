---
title: Verbindungszeichenfolgen in Azure Database for MariaDB
description: In diesem Dokument sind alle derzeit unterstützten Verbindungszeichenfolgen für Anwendungen aufgeführt, um eine Verbindung mit Azure Database for MariaDB herzustellen. Hierzu zählen ADO.NET (C#), JDBC, Node.js, ODBC, PHP, Python und Ruby.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-python, devx-track-js, devx-track-csharp
ms.openlocfilehash: 8119f0bfd1f9007cab0df93ad2c7ef22b4db2199
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94533003"
---
# <a name="how-to-connect-applications-to-azure-database-for-mariadb"></a>Herstellen einer Verbindung zwischen Anwendungen und Azure Database for MariaDB
In diesem Thema sind alle von Azure Database for MariaDB unterstützten Typen von Verbindungszeichenfolgen sowie Vorlagen und Beispiele aufgeführt. Ihre Verbindungszeichenfolge enthält unter Umständen andere Parameter und Einstellungen.

- Informationen zum Einholen des Zertifikats finden Sie unter [Vorgehensweise zum Konfigurieren von SSL](./howto-configure-ssl.md).
- {your_host} = [Servername].mariadb.database.azure.com
- {your_user}@{servername} = userID-Format für die korrekte Authentifizierung.  Wenn Sie nur die Benutzer-ID verwenden, tritt bei der Authentifizierung ein Fehler auf.

## <a name="adonet"></a>ADO.NET
```csharp
Server={your_host}; Port=3306; Database={your_database}; Uid={username@servername}; Pwd={your_password}; SslMode=Preferred;
```

In diesem Beispiel lautet der Servername `mydemoserver`, der Datenbankname `wpdb`, der Benutzername `WPAdmin` und das Kennwort `mypassword!2`. Daher muss die Verbindungszeichenfolge wie folgt lauten:

```csharp
Server= "mydemoserver.mariadb.database.azure.com"; Port=3306; Database= "wpdb"; Uid= "WPAdmin@mydemoserver"; Pwd="mypassword!2"; SslMode=Required;
```

## <a name="jdbc"></a>JDBC
```java
String url ="jdbc:mariadb://{your_host}:3306/{your_database}?useSSL=true&trustServerCertificate=true"; myDbConn = DriverManager.getConnection(url, "{username@servername}", {your_password});
```

## <a name="nodejs"></a>Node.js
```javascript
var conn = mysql.createConnection({host: "{your_host}", user: "{your_username}", password: {your_password}, database: {your_database}, port: 3306, ssl:{ca:fs.readFileSync({ca-cert filename})}});
```

## <a name="odbc"></a>ODBC
```cpp
DRIVER={MARIADB ODBC 3.0 Driver}; Server="{your_host}"; Port=3306; Database={your_database}; Uid="{username@servername}"; Pwd={your_password}; sslca={ca-cert filename}; sslverify=1;
```

## <a name="php"></a>PHP
```php
$con=mysqli_init(); mysqli_ssl_set($con, NULL, NULL, {ca-cert filename}, NULL, NULL); mysqli_real_connect($con, "{your_host}", "{username@servername}", {your_password}, {your_database}, 3306);
```

## <a name="python"></a>Python
```python
cnx = mysql.connector.connect(user="{username@servername}", password={your_password}, host="{your_host}", port=3306, database={your_database}, ssl_ca={ca-cert filename}, ssl_verify_cert=true)
```

## <a name="ruby"></a>Ruby
```ruby
client = Mysql2::Client.new(username: "{username@servername}", password: {your_password}, database: {your_database}, host: "{your_host}", port: 3306, sslca:{ca-cert filename}, sslverify:false, sslcipher:'AES256-SHA')
```

## <a name="get-the-connection-string-details-from-the-azure-portal"></a>Abrufen von Details zur Verbindungszeichenfolge aus dem Azure-Portal
Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrem Azure Database for MariaDB-Server, und klicken Sie dann auf **Verbindungszeichenfolgen**, um die Zeichenfolgenliste für Ihre Instanz abzurufen: ![Bereich „Verbindungszeichenfolgen“ im Azure-Portal](./media/howto-connection-strings/connection-strings-on-portal.png)

Die Zeichenfolge enthält Details wie Treiber, Server und andere Parameter für die Datenbankverbindung. Passen Sie diese Beispiele mit Ihren eigenen Parametern an, z.B. Ihrem Datenbanknamen und Ihrem Kennwort. Sie können diese Zeichenfolge dann verwenden, um eine Verbindung zwischen Ihrem Code sowie Ihren Anwendungen und dem Server herzustellen.

<!-- 
## Next steps
- For more information about connection libraries, see [Concepts - Connection libraries](./concepts-connection-libraries.md).
- -->
