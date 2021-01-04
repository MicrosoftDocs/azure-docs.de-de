---
title: Verbindungszeichenfolgen – Azure Database for MySQL
description: In diesem Dokument sind alle derzeit unterstützten Verbindungszeichenfolgen für Anwendungen aufgeführt, um eine Verbindung mit Azure-Datenbank für MySQL herzustellen. Hierzu zählen ADO.NET (C#), JDBC, Node.js, ODBC, PHP, Python und Ruby.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-python, devx-track-js
ms.openlocfilehash: 40af2660f0052a876ef9310bc2426295ba67558b
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94541486"
---
# <a name="how-to-connect-applications-to-azure-database-for-mysql"></a>Herstellen einer Verbindung zwischen Anwendungen und Azure-Datenbank für MySQL
In diesem Thema sind alle von Azure Database for MySQL unterstützten Typen von Verbindungszeichenfolgen sowie Vorlagen und Beispiele aufgeführt. Ihre Verbindungszeichenfolge enthält unter Umständen andere Parameter und Einstellungen.

- Informationen zum Einholen des Zertifikats finden Sie unter [Vorgehensweise zum Konfigurieren von SSL](./howto-configure-ssl.md).
- {your_host} = \<servername>.mysql.database.azure.com
- {your_user}@{servername} = userID-Format für die korrekte Authentifizierung.  Wenn Sie nur die Benutzer-ID verwenden, tritt bei der Authentifizierung ein Fehler auf.

## <a name="adonet"></a>ADO.NET
```ado.net
Server={your_host};Port={your_port};Database={your_database};Uid={username@servername};Pwd={your_password};[SslMode=Required;]
```

In diesem Beispiel lautet der Servername `mydemoserver`, der Datenbankname `wpdb`, der Benutzername `WPAdmin` und das Kennwort `mypassword!2`. Daher muss die Verbindungszeichenfolge wie folgt lauten:

```ado.net
Server= "mydemoserver.mysql.database.azure.com"; Port=3306; Database= "wpdb"; Uid= "WPAdmin@mydemoserver"; Pwd="mypassword!2"; SslMode=Required;
```

## <a name="jdbc"></a>JDBC
```jdbc
String url ="jdbc:mysql://%s:%s/%s[?verifyServerCertificate=true&useSSL=true&requireSSL=true]",{your_host},{your_port},{your_database}"; myDbConn = DriverManager.getConnection(url, {username@servername}, {your_password}";
```

## <a name="nodejs"></a>Node.js
```node.js
var conn = mysql.createConnection({host: {your_host}, user: {username@servername}, password: {your_password}, database: {your_database}, Port: {your_port}[, ssl:{ca:fs.readFileSync({ca-cert filename})}}]);
```

## <a name="odbc"></a>ODBC
```odbc
DRIVER={MySQL ODBC 5.3 UNICODE Driver};Server={your_host};Port={your_port};Database={your_database};Uid={username@servername};Pwd={your_password}; [sslca={ca-cert filename}; sslverify=1; Option=3;]
```

## <a name="php"></a>PHP
```php
$con=mysqli_init(); [mysqli_ssl_set($con, NULL, NULL, {ca-cert filename}, NULL, NULL);] mysqli_real_connect($con, {your_host}, {username@servername}, {your_password}, {your_database}, {your_port});
```

## <a name="python"></a>Python
```python
cnx = mysql.connector.connect(user={username@servername}, password={your_password}, host={your_host}, port={your_port}, database={your_database}[, ssl_ca={ca-cert filename}, ssl_verify_cert=true])
```

## <a name="ruby"></a>Ruby
```ruby
client = Mysql2::Client.new(username: {username@servername}, password: {your_password}, database: {your_database}, host: {your_host}, port: {your_port}[, sslca:{ca-cert filename}, sslverify:false, sslcipher:'AES256-SHA'])
```

## <a name="get-the-connection-string-details-from-the-azure-portal"></a>Abrufen von Details zur Verbindungszeichenfolge aus dem Azure-Portal
Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrem Azure Database for MySQL-Server, und klicken Sie dann auf **Verbindungszeichenfolgen**, um die Zeichenfolgenliste für Ihre Instanz abzurufen: :::image type="content" source="./media/howto-connection-strings/connection-strings-on-portal.png" alt-text="Bereich „Verbindungszeichenfolgen“ im Azure-Portal":::

Die Zeichenfolge enthält Details wie Treiber, Server und andere Parameter für die Datenbankverbindung. Passen Sie diese Beispiele mit Ihren eigenen Parametern an, z.B. Ihrem Datenbanknamen und Ihrem Kennwort. Sie können diese Zeichenfolge dann verwenden, um eine Verbindung zwischen Ihrem Code sowie Ihren Anwendungen und dem Server herzustellen.

## <a name="next-steps"></a>Nächste Schritte
- Weitere Informationen zu Verbindungsbibliotheken finden Sie unter [Konzepte – Verbindungsbibliotheken](./concepts-connection-libraries.md).
